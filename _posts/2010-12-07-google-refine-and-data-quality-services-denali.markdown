---
author: Roger
comments: true
date: 2010-12-07 02:19:16+00:00
layout: post
link: http://www.rogernoble.com/2010/12/07/google-refine-and-data-quality-services-denali/
slug: google-refine-and-data-quality-services-denali
title: Google Refine and Data Quality Services (Denali)
wordpress_id: 28
categories:
- Technology
tags:
- Denali
- DQS
- Google Refine
- SQL 11
- SQL Server
- SSIS
---

I've recently come across a new(ish) product from Google called Refine - [http://code.google.com/p/google-refine/](http://code.google.com/p/google-refine/) and I thought I would see how it compares to the upcoming Data Quality Services (DQS) that is going to be part of SQL 11 (Denali).

Refine is a tool that can easily and powerfully cleanse and enhance data from a flat file source. If you haven't seen the videos on Refine then check them out at - [http://code.google.com/p/google-refine/](http://code.google.com/p/google-refine/). To be honest I'm pretty impressed with Refines features and it's something I will defiantly use on some projects. But I can see some huge limitations especially when wanting to use it as part of a larger project. A quick pros and cons:

Pros



	
  * Powerful text search and clustering allowing quick data cleansing (Facets)

	
  * Integration of Freebase data allowing "reconciliation" with external data sets and including additional columns

	
  * Scripting language, web service and JSON support

	
  * Full Undo/Redo support


Cons

	
  * Only seems to work on flat file or web service based data

	
  * Once a project is created its not easily repeatable or automatable on similar data

	
  * It's run as a standalone application


All up this means that Refine is more suited to cleansing/creating data sets in a smaller once-off fashion. For example using Refine for a monthly ETL of *.CSV based data involves the manual steps of opening up a previous project, exporting the Undo/Redo JSON object, creating a new project based on the new data, and applying the JSON object - a bit of a pain, and it's something which hopefully the project addresses in the future.

So how does Refine compare to DQS? DQS was not part of Denali CTP1 and there isn't much information that I can find, however I was able to get a look at it in action while I was at the SQL PASS 2010 conference. While its too early to do a pros and cons on DQS I can say that it far surpasses Refine in usability and scale.

DQS has broken the area of data cleansing into 3 general areas:

**1. Knowledge management** - DQS uses a similar method of searching and clustering data, calling them Domains instead of Facets. Domains are created either via external data sets (from the Azure Marketplace or other reference data) or from your own data. Once defined domains become part of whats called a DQ Knowledge Base (DQKB) which can be mapped to a data source, which leads to part 2.

**2. Cleansing and Matching** - It's this stage where DQS shines over Refine. The created DQKB can then be applied over existing data or can be incorporated into an ETL process via SSIS. The domains apply their rules over the data and produce 1 of 4 results: Correct, Corrected, Not Correct or Auto Suggested. While the Not Correct and Auto Suggested results still require some manual intervention, the domain rules can then be updated to further enhance the DQKB. DQS can also apply matching over columns to remove duplicated in the same way Refine does.  This then leads on to part 3.

**3. Administration **- Not much has been revealed about this stage of the process except that Microsoft will make available the means to monitor and control the quality of data.

While there isn't much to see yet, DQS is looking like a far superior product than Refine - I can't wait to get my hands on it!
