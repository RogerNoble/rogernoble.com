---
author: Roger
comments: true
date: 2011-01-19 04:55:55+00:00
layout: post
link: http://www.rogernoble.com/2011/01/19/installing-powerpivot-for-sharepoint-2010-on-an-existing-farm/
slug: installing-powerpivot-for-sharepoint-2010-on-an-existing-farm
title: Installing PowerPivot for SharePoint 2010 on an existing farm
wordpress_id: 46
---

I ran into two major hurdles today getting PowerPivot installed and working in SharePoint 2010 on my dev machine.

Using the installation guide on MSDN [http://msdn.microsoft.com/en-us/library/ee210616.aspx](http://msdn.microsoft.com/en-us/library/ee210616.aspx) should have been straight forward, however there are a few bugs in the process. The first relates to central administration being installed on a user defined non-random port. In my case I installed it on the easy to remember port 11111. The second being that the installation cannot find a vital assembly. When installing the POWERPIVOT instance of SSAS the setup fails with an error about not being able to find the assembly - Microsoft.AnalysisServices.SharePoint.Integration.

Thankfully Cornelius J. van Dyk ([blog](http://www.cjvandyk.com/blog/default.aspx) \| [twitter](http://twitter.com/cjvandyk)) has written a great post on how to fix this - [http://www.cjvandyk.com/blog/Articles/How%20do%20I%20-%20Install%20PowerPivot%20into%20an%20EXISTING%20SharePoint%202010%20farm.aspx](http://www.cjvandyk.com/blog/Articles/How%20do%20I%20-%20Install%20PowerPivot%20into%20an%20EXISTING%20SharePoint%202010%20farm.aspx). The only thing I would add is that I was able to use the repair option on the SSAS instance and that I used the Visual Studio tool gacutil to remove to pesky assembly.

The next big hurdle was starting the Analysis Services service in Central Administration. The problem is because I'm installing everything on a Domain Controller things get a little screwed up (I do not recommend doing this in a Test or Production environment and is only for my Dev VM). Whats really scary is the solution! Dave Wickert ([blog](http://powerpivotgeek.com/)) found the solution by quickly changing the service account back after starting the service via Central Admin - [http://powerpivotgeek.com/2009/11/17/installing-powerpivot-for-sharepoint-on-a-domain-controller/](http://powerpivotgeek.com/2009/11/17/installing-powerpivot-for-sharepoint-on-a-domain-controller/).

Dave has also put together a great step-by-step guide on setting up a dev VM from scratch that is worth checking out - [http://blogs.msdn.com/b/powerpivot/archive/2011/01/11/install-powerpivot-for-sharepoint-on-a-domain-controller.aspx](http://blogs.msdn.com/b/powerpivot/archive/2011/01/11/install-powerpivot-for-sharepoint-on-a-domain-controller.aspx)

Good luck!
