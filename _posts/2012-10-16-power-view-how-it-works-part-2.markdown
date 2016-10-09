---
author: Roger
comments: true
date: 2012-10-16 01:57:37+00:00
layout: post
link: http://www.rogernoble.com/2012/10/16/power-view-how-it-works-part-2/
slug: power-view-how-it-works-part-2
title: 'Power View – how it works: part 2'
wordpress_id: 119
categories:
- Technology
tags:
- power view
---

In the last post on Power View I had a bit of a look at how Power View communicates with Reporting Services. You can read more about that in [Part 1](http://www.rogernoble.com/2012/10/11/power-view-how-it-works-part-1/).




Having a basic understanding on the inner workings of Power View allows us to appreciate all that is going on when a Power View report is requested. In addition we are able to use that insight to enhance the Power View experience and even create solutions to make it a better experience for our users.





### Warm the Cache




For example one thing that is lacking in a Power View .rdlx file is a caching mechanism like what is possible with their .rdl cousins. By default the PowerPivot database in Analysis Services will get cleaned up after 48 hours of inactivity, the duration can be configured in Central Administration > General Application Settings > Configure service application settings > Disk Cache.
[![PowerPivot Disk Cache]({{site.baseurl}}/assets/img/PowerPivotDiskCache.png)]({{site.baseurl}}/assets/img/PowerPivotDiskCache.png)But what if you've got a report that is accessed infrequently? Well based on our knowledge of the Reporting Services web service, we could, for instance write a PowerShell script to pre-load or warm the cache by sending an rs:GetReportAndModels request:




    
    $web = Get-SPWeb http://server2008r2
    $list = $web.Lists["PowerPivot Gallery"]
    
    $spQuery = New-Object Microsoft.SharePoint.SPQuery
    $spQuery.ViewAttributes = "Scope='Recursive'";
    $spQuery.RowLimit = 2000
    $caml = '<Where><Eq><FieldRef Name="File_x0020_Type" /><Value Type="Text">rdlx</Value></Eq></Where>' 
    $spQuery.Query = $caml 
    
    do
    {
        $listItems = $list.GetItems($spQuery)
        $spQuery.ListItemCollectionPosition = $listItems.ListItemCollectionPosition
        foreach($item in $listItems)
        {
    		$reportAddress = $web.Url + "/_vti_bin/reportserver/?" + [System.Uri]::EscapeDataString($web.Url + "/" + $item.Url) + "&rs:Command=GetReportAndModels"
    		$request = [System.Net.WebRequest]::Create($reportAddress)
    		$request.Credentials = [System.Net.CredentialCache]::DefaultNetworkCredentials
    		$request.ContentType = "application/progressive-report"
    		$request.GetResponse()
        }
    }
    while ($spQuery.ListItemCollectionPosition -ne $null)




### Powerless View




One of the early criticisms of Power View is that there are so many dependencies, and not everyone wants to have a full blown SharePoint Enterprise install just to make it work - in fact there is even [a connect item about it](https://connect.microsoft.com/SQLServer/feedback/details/738224/power-view-outside-of-sharepoint-enterprise) spearheaded by [Jen Stirrup](http://www.jenstirrup.com/2012/04/power-view-outside-of-sharepoint.html) ([blog](http://www.jenstirrup.com)|[twitter](http://twitter.com/jenstirrup)) (and yes, I do realise that in a lot of respects Excel 2013 makes this issue moot).




Well now that we know how Power View communicates with Reporting Services we can use that knowledge to simulate our own web service, slice out Power View from SharePoint, transplant it to a regular web application and then stitch it all up.




With ASP.NET MVC and custom routes this is remarkable easy. I'm using MVC 4, but custom routes are one of the core features of ASP.NET MVC and so this could be done in any version of MVC.




    
    routes.MapRoute(
      name: "ReportServer",
      url: "_vti_bin/{controller}/{action}/{id}",
      defaults: new { controller = "ReportServer", action = "Index", id = UrlParameter.Optional }
    );




We need to listen for calls to the _vti_bin/ReportServer start address, in my version I've set the second part of the string to be the controller, which defaults to ReportServer.




Once we've created the route, we then need to create a controller called ReportServer to handle to requests. I've put together a simple controller that looks for the rs:command query parameter and then returns a custom ActionResult for each rs:command type.




    
    public class ReportServerController : Controller
    {
      public ActionResult Index()
      {
        if(String.IsNullOrEmpty(Request.QueryString["rs:Command"]))
          return View();
        var command = Request.QueryString["rs:Command"];
    
        if (command == "GetReportAndModels")
          return new GetReportAndModelsActionResult(string.Format("http://{0}/Content/rdlx/Report.rdlx", Request.Url.Authority));
        else if (command == "RenderEdit")
        {
          if (!String.IsNullOrEmpty(Request.QueryString["rs:ProgressiveSessionId"]))
            return new RenderEditActionResult(string.Format("http://{0}/Content/rdlx/RenderEdit.bin", Request.Url.Authority));
        }
    
        //if unknown request
        return View();
      }
    }




The project is still very much a work in progress and currently I've hard-coded parts to suit the demo report, but with a little more effort it could be completely dynamic. If you're interested in the code I'm hosting it up on CodePlex with the cheeky name of [Powerless View](http://powerless.codeplex.com). Feel free to download and fork it. The only part that is missing from the project is the Power View Silverlight application itself - you'll have to supply that.




I'd also like to point out that while this solution allows Power View to be hosted without SharePoint, we cannot get away from SharePoint completely as it's still required to generate the initial .rdlx.
