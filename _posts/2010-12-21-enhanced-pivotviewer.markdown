---
author: Roger
comments: true
date: 2010-12-21 05:12:14+00:00
layout: post
link: http://www.rogernoble.com/2010/12/21/enhanced-pivotviewer/
slug: enhanced-pivotviewer
title: Enhanced PivotViewer
wordpress_id: 32
---

Ever since the [Silverlight PivotViewer ](http://www.microsoft.com/silverlight/pivotviewer/)control launched we at [LobsterPot](http://www.lobsterpot.com.au) have been keenly interested in building pivots for ourselves and especially for our clients. Some of the pivots we have built have made waves. One of our personal highlights was when our TechEd session pivot was featured during the keynote for TechEd Australia 2010.

[![TechEd Keynote Pivot]({{site.baseurl}}/assets/img/TechEdPivot.jpg)]({{site.baseurl}}/assets/img/TechEdPivot.jpg)

While it's fun and easy enough to build one-off pivots using the tools available, we were wanting a better way to easily add and configure new collections. Not only that, we wanted to further enhance the already fantastic PivotViewer experience. So we are launching [pivot.lobsterpot.com.au](pivot.lobsterpot.com.au) as a way to showcase some of the pivots we have put together, but also demonstrate some of the ways we have been able to further enhance the PivotViewer control.

Built on ASP.Net MVC the PivotViewer platform can easily accept new pivot collections and customise they way they look via simple xml based configuration. An example configuration entry would look like this:

    
    <Collection name="24HOP" listed="True">
        <cxml>24hrsOfPASSEvents.cxml</cxml>
        <title>24hrs Of PASS Events</title>
        <viewerstate><![CDATA[$view$=2]]></viewerstate>
        <chrome>
          <background type="image">http://pivot.lobsterpot.com.au/collections/PASS24BG.png</background>
        </chrome>
      </Collection>


This allows us to easily add new collections, but also control visual elements and set the initial viewerstate of the pivot.

One of the missing features of the PivotViewer control was the inclusion of a Data Grid view, so we added one to our pivot and integrated it into the PivotViewer controls by adding a Data Grid view button next to the existing  view controls. To further extend the Data Grid we also hooked it into the facet filter and sort controls. This enhancement means that sorting the Data Grid will also set the sort for the whole PivotViewer, and conversely sorting the PivotViewer will also sort the Data Grid.

At LobsterPot we've built up quite a collection of pivots ranging from [TechEd sessions](http://pivot.lobsterpot.com.au/TechEd) to [Windows Phone 7 handsets](http://pivot.lobsterpot.com.au/WP7), with many more to come. To keep track of them all we've built an index of all the currently available pivots at - [http://pivot.lobsterpot.com.au](http://pivot.lobsterpot.com.au/). So check out the pivots currently available, but don't forget to subscribe to the RSS feed as more pivots will be following shortly.

It's also worth mentioning that [Xpert360 ](http://xpert360.wordpress.com/)have also put a lot of work into enhancing the PivotViewer control and I would recommend checking out some of the awesome stuff they have done. [Rob](http://sqlblog.com/blogs/rob_farley) spent time discussing PivotViewer with some of their guys at the [SQLBits](http://www.sqlbits.com) conference in September this year, so it was good to see their series of posts come out over later months.

Also check out the [MSDN pivot](http://msdn.microsoft.com/en-us/magazine/default.aspx) that Howard Dierking from Microsoft has put together. Howard is another friend of ours from back in his MSL days, and was able to provide some direction on getting the links correctly working on the PivotViewer control. It's good to have friends to collaborate with!
