---
author: Roger
comments: true
date: 2010-12-29 01:22:27+00:00
layout: post
link: http://www.rogernoble.com/2010/12/29/pivotviewer-with-jit-collections/
slug: pivotviewer-with-jit-collections
title: PivotViewer with JIT collections
wordpress_id: 37
categories:
- Technology
tags:
- DeepZoom
- PivotViewer
---

I've spent the last week further extending our PivotViwer platform to allow for Just In Time (JIT) collections. Now creating JIT collections is nothing new or exiting, Microsoft even has a [sample application](http://www.silverlight.net/learn/pivotviewer/just-in-time-sample-code/) for creating them. While the sample application is great there were some limitations which meant that I had to roll my own. If you're interested in the first JIT collection I've created then have a look at our Flickr browser - [http://pivot.lobsterpot.com.au/flickr](http://pivot.lobsterpot.com.au/flickr) each photo with a tag can be further drilled-through to allow for further exploration - check it out!

In creating my own JIT collection generator I had certain goals in mind, some of them were successful, others were not. But in creating my own Pivot and DeepZoom handlers I got a fantastic insight into how the PivotViewer control and DeepZoom works.


### ASP.NET MVC


One of the goals was to keep the existing MVC application that is currently serving up [our static collections](http://pivot.lobsterpot.com.au) ([See my last post for more details](http://www.rogernoble.com/2010/12/21/enhanced-pivotviewer/)). The Microsoft example uses HttpHandlers to intercept collection requests and output the required xml. This could have been integrated into the existing application, but in order to achieve some of my goals I decided instead to use MVC's MapRoute feature to capture the collection requests and then render them using custom ActionResults. The required routes were:

    
    //PivotViewer collection request
    routes.MapRoute(
        "CXML",
        "{controller}/{action}/pivot.cxml",
        new { controller = "Collection", action = "Index" }
    );
    //DeepZoom collection request
    routes.MapRoute(
        "DeepZoomDzc",
        "DZ/{action}/{cacheKey}.dzc",
        new { controller = "DeepZoom", action = "Dzc", cacheKey = UrlParameter.Optional, itemId = UrlParameter.Optional }
    );
    //DeepZoom image definition request
    routes.MapRoute(
        "DeepZoomDzi",
        "DZ/Dzc/{cacheKey}/{itemId}.dzi",
        new { controller = "DeepZoom", action = "Dzi", cacheKey = UrlParameter.Optional, itemId = UrlParameter.Optional }
    );
    //DeepZoom tile request
    routes.MapRoute(
        "DeepZoomTile",
        "DZ/Dzc/{cacheKey}_files/{level}/{x}_{y}.jpg",
        new { controller = "DeepZoom", action = "ImageTile", cacheKey = UrlParameter.Optional }
    );
    //DeepZoom image request
    routes.MapRoute(
        "DeepZoomImage",
        "DZ/Dzc/{cacheKey}/{itemId}_files/{level}/{x}_{y}.jpg",
        new { controller = "DeepZoom", action = "Image", cacheKey = UrlParameter.Optional }
    );


As you can see each of the DeepZoom routes was mapped to a DeepZoom controller. The DeepZoom controller was then tasked with returning the appropriate ActionResult.


### Dynamic Image Selection


My second goal was to implement dynamic image selection. The plan was to leverage the different image sizes available in flickr photos, and at run time decide which image to download. I had decided to pick Thumbnail, Medium and Large/Original sizes. When a DeepZoom image is requested it first requests the *.dzi definition which contains the image size. It then requests the image using the following formatted url:

<http://address>/<itemaddress>/**<level>**/<x-coordinate>_<y-coordinate>.jpg

The level being an integer associated with the size of the image. My plan was to get the level and then map that to the image sizes: 0 - 2 = Thumbnail, 3 - 5 = Medium and 6+ = Large/Original. Unfortunately for me part of the image definition xml also contains the size of the image. This means that if the size of the image is different from the expected size then the rendering is thrown completely off. So if the Large size is 640 x 426 and the thumbnail is 100 x 67 then things go horribly wrong! The solution would be to then try and dynamically resize the images, but I'm not convinced it's worth the CPU power. In the end I dropped this feature and stuck with the Thumbnail size to improve performance.


### Drill-through collections


The final feature was to include drill-through functionality that allowed for filters/tags to be passed to the CXML controller and generate a new collection to be consumed. Using a method of prefacing links with "drillthrough:" I was that able to use e.Link.Scheme on the PivotViewers OnLinkClicked event to determine if the link was a drill-through action. If it was I was then able to reload to collection - a big thanks to [Mike Taulty's post on PivotViewer collections](http://mtaulty.com/CommunityServer/blogs/mike_taultys_blog/archive/2010/08/19/pivotviewer-collections-and-the-silverlight-control.aspx) for the idea.

While there is still so much to be done, the flickr JIT collection is a great example of how the PivotViewer can be leveraged to provide more than just static collections.
