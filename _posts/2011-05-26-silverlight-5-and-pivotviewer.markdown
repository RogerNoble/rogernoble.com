---
author: Roger
comments: true
date: 2011-05-26 10:57:09+00:00
layout: post
link: http://www.rogernoble.com/2011/05/26/silverlight-5-and-pivotviewer/
slug: silverlight-5-and-pivotviewer
title: Silverlight 5 and PivotViewer
wordpress_id: 59
categories:
- Technology
tags:
- PivotViewer
- Silverlight
---

I'm a little late in writing this, but at [MIX11 ](http://live.visitmix.com/)Microsoft announced that Silverlight 5 - the next version of Silverlight will also contain the next version of PivotViewer out of the box!

[Nick Kramer](http://blogs.msdn.com/b/nickkramer/) gave an overview and demo of the features in his session on[ Advanced Features in Silverlight 5](http://channel9.msdn.com/events/MIX/MIX11/MED12) (The PivotViewer part is at 36:45)

While the basic functionality remains the same there are some impressive new features that will make using the PivotViewer control much better to use. The most notable feature is the ability to databind to client based collections, while I'm unsure if cxml is dead (not that I'll miss it), but data binding will bring true dynamic collections without having to worry about creating custom HttpHandlers or [Mvc.ActionResult](http://tepehughes.wordpress.com/2010/05/04/mvc-actionresult-to-export-cxml-for-livepivot/)s to serve up CXML.

Another great addition is the ability to create XAML based tiles instead of images and in addition the ability to specify different tiles at different zoom levels. This will be a huge bonus especially for adding additional item based data on a tile when zoomed in. Previously the way to do this was to use reporting services to either pre-render the tiles and generate a DeepZoom collection, or use [PivotViewer for Reporting Services](http://blogs.msdn.com/b/robertbruckner/archive/2010/07/18/pivotviewer-for-reporting-services.aspx) (a.k.a. PivotViewer for PowerPivot, SharePoint 2010 Enterprise and SQL Server 2008 R2 Enterprise)

Another welcome feature is the ability to get additional data for an item, giving the user the ability to drill-down even further into the data.

[caption id="attachment_60" align="alignnone" width="709" caption="A tile at the maximum zoom level showing the Get Data button"][![PivotViewer version 2]({{site.baseurl}}/assets/img/PivotViewer2.png)]({{site.baseurl}}/assets/img/PivotViewer2.png)[/caption]

While the[ Silverlight 5 beta is available for download](http://www.silverlight.net/getstarted/silverlight-5-beta/) unfortunately for us PivotViewer (Can I call it v2?) has not been included.

All in all the next version is looking fantastic and a definite step forward for PivotViewer.

Also stay tuned for my next post, I just remembered I put together an SSIS package for generating CXML and DZ Collections based on SSAS cubes and I forgot to post it!

**Update: As promised my post on[ CXML from SSIS](http://www.rogernoble.com/2011/05/27/using-ssis-to-generate-pivotviewer-cxml-from-ssas/)**
