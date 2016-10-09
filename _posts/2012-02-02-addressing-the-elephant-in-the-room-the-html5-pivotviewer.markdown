---
author: Roger
comments: true
date: 2012-02-02 00:33:47+00:00
layout: post
link: http://www.rogernoble.com/2012/02/02/addressing-the-elephant-in-the-room-the-html5-pivotviewer/
slug: addressing-the-elephant-in-the-room-the-html5-pivotviewer
title: 'Addressing the elephant in the room: The HTML5 PivotViewer'
wordpress_id: 83
categories:
- Technology
tags:
- HTML5
- JavaScript
- PivotViewer
---

I've been working with the Silverlight PivotViewer control for quite a while now, and if you read this blog you'll also know that I've had some success in customising it. Now don't get me wrong, it's a fantastic control, I love they way it allows you to quickly visualise relationships between items, but for a while now the elephant in the room has been lack of support for the iPad and like devices.

Now I'm not going to go into a rant on Silverlight as a technology, it's future and what I think of the iPad or anything else - to be honest it's a boring discussion. What I'm interested in is providing the best user experience that clearly presents the data in a way that users can make the most of. By creating applications that are based on web standards then I don't have to worry about it. In fact recently Microsoft's Internet Explorer team and [Zeptolab](http://www.zeptolab.com/) partnered up to produce an excellent example of how well HTML5 games perform extremely well by creating a version of Cut the Rope - check it out at [http://www.cuttherope.ie/](http://www.cuttherope.ie/).

So before I got any further I encourage you to check out the latest version of a project that I've been working on: the [HTML5 PivotViewer](http://bit.ly/vZVpES). I'm calling this a beta and while functionally it's incomplete compared to the Silverlight version, it easily demonstrates that modern browsers have the power to process and render data at near native speeds.

So while the HTML5 PivotViewer control is functionally incomplete, there is no trickery involved, as it is in fact reading a CXML file (the XML schema that the PivotViewer control consumes) and is rendering images from a DeepZoom collection (at this point in time I'm only displaying images at level 6 which is why they are a bit blurry).

As with any serious JavaScript development these days I've leaned heavily on the fantastic jQuery library - in fact the control has been built as a jQuery plugin which means creating an instance of the control is remakable simple:

    
    <div id="pivotviewer" style="height:600px;"></div>
    <script type="text/javascript">
    	var debug = false;
    	$(document).ready(function () {
    		$('#pivotviewer').PivotViewer({ CXML: "/Collections/PASS Summit 2011.cxml" });
    	});
    </script>


If you're not familiar with jQuery (or JavaScript), the key parameter to take note of here is calling the PivotViewer() function and setting the CXML property to the  location of the Pivot collection - and that's all there is to it!

This project started out as a proof of concept to see if it could actually be done, and I've been continually surprised that not only can it be done, but that it performs extremely well - even on relatively low powered devices (like my ancient LG GW620).

So what's next? I've got a whole heap of functionality that I'm going to be adding, including the Data and Map views that I've previously added to the Silverlight version of the control.

Let me know what you think, I'm open to suggestions on what features you've been missing in PivotViewer.
