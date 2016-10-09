---
author: Roger
comments: true
date: 2012-11-06 21:11:17+00:00
layout: post
link: http://www.rogernoble.com/2012/11/07/power-view-how-it-works-part-3/
slug: power-view-how-it-works-part-3
title: 'Power View – how it works: part 3'
wordpress_id: 120
categories:
- Technology
tags:
- power view
---

So far in this series we've explored some of the internals of Power View, how it communicates with Reporting Services and how it's possible to create our own service to mimic the SSRS web service. Last year at  the PASS Summit 2011 Microsoft demonstrated Power View working on various mobile devices, but over a year later all we currently have is a Silverlight version.




So in this final post I want to explore the possibility of creating a HTML5 version of Power View using the existing interfaces in order to simply replace the Silverlight version with a HTML5 one. To some this may seem like a strange thing to do, but I really believe the future of BI (especially mobile BI) is based on open web standards. In fact it was one of my main motivators in [liberating PivotViewer from Silverlight](http://www.rogernoble.com/2012/02/02/addressing-the-elephant-in-the-room-the-html5-pivotviewer/).




However it turns out there are quite a few challenges to overcome in order to render an rdlx report using only JavaScript. This post will cover just a few of the pieces of the puzzle that I've investigated and is not the entire solution.




The first task is to extract the actual report definition from the rdlx zip file. To do this I found a really great library called [zip.js](http://gildas-lormeau.github.com/zip.js/) that uses HTML5 web workers to enumerate and extract the contents of zip files. It's actually a pretty impressive library, and allows for extraction from http, blob or string encoded zip files.





### Reading the RDLX



    
    // use a HttpReader to read the zip from URL
                zip.createReader(new zip.HttpReader('/Content/rdlx/Report1.rdlx.zip'), function (reader) {
                    // get all entries from the zip
                    reader.getEntries(function (entries) {
                        if (entries.length) {
                            for (var i = 0; i < entries.length; i++) {
                                if (entries[i].filename.indexOf('.rdl') >= 0) {
                                    //get rdl
                                    entries[i].getData(new zip.TextWriter(), function (text) {
                                        // text contains the entry data as a String
                                        console.log(text);
                                        // close the zip reader
                                        reader.close(function () {
                                            // onclose callback
                                        });
                                    }, function (current, total) {
                                        // onprogress callback
                                    });
                                }
                            }
                        }
                    });
                }, function (error) {
                    // onerror callback
                    console.log(error);
                });




In the example above I'm using a zip.js HttpReader object to grab my rdlx file from a URL. The created reader has a getEntries method that enumerates the zip file and returns it's contents as an array of files. I'm then looking for files with '.rdl' in their name and dumping the contents out to the console.





### Next Steps




Once we've extracted the rdl file we can start parsing the xml to create the elements of our report in HTML. As you can imagine this is not a trivial task, and I'm not going to go into this in any detail. I did however discover that the updated schema for Power View reports hasn't been published anywhere by Microsoft (http://schemas.microsoft.com/sqlserver/reporting/2011/01/reportdefinition) even though the SQL Server 2012 SSRS schema has been ([http://schemas.microsoft.com/sqlserver/reporting/2010/01/reportdefinition/ReportDefinition.xsd](http://schemas.microsoft.com/sqlserver/reporting/2010/01/reportdefinition/ReportDefinition.xsd)). I've only spent a little time looking around for it, so if anyone else finds it please let me know. Without the schema creating a one-for-one copy of a Power View report is a little trickier, but still possible.




Regardless, lets assume that parsing the rdl and building up the UI has been done, the next step is to start calling the report server web service with the RenderEdit command to grab the actual data. This could be done with a series of ajax calls, and the binary result could be parsed in JavaScript - but this would be horribly inefficient. JavaScript just isn't built to handle data in that way.





### Final Thoughts




While I believe a HTML5 version of Power View is possible, it would require a serious amount of effort to implement based on the current SSRS architecture. In it's current incarnation the SSRS web service returns mostly binary data instead of web friendly XML or JSON formats, which could quite easily be consumed by client side code. In my opinion it's a shame that PowerPivot and Power View are not more open and queryable.




The conclusion is that if Microsoft does release a HTML5 version of Power View some big changes are going to have to be made to the way the SSRS web service communicates.
