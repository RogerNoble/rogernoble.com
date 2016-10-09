---
author: Roger
comments: true
date: 2011-06-19 10:17:08+00:00
layout: post
link: http://www.rogernoble.com/2011/06/19/my-talk-from-spsadl-jumpstart-your-folksonomy-with-text-mining/
slug: my-talk-from-spsadl-jumpstart-your-folksonomy-with-text-mining
title: 'My talk from #SPSADL - Jumpstart your Folksonomy with Text Mining'
wordpress_id: 74
categories:
- Technology
tags:
- SharePoint
- SSIS
- Text Mining
---

Yesterday I attended, and was lucky enough to speak at, the second inaugural [SharePoint Saturday Adelaide](http://www.sharepointsaturday.org/adelaide/default.aspx). It was an awesome event and I just wanted to say a big thanks to Brian Farnhill ([blog](http://blog.brianfarnhill.com/) \| [twitter](http://twitter.com/#!/BrianFarnhill)) and Bruno Lanceleaux ([blog](http://brulan.wordpress.com/) \| [twitter](http://twitter.com/#!/Brulan)) for organising it and letting me speak.

I presented at one of the the last sessions of the day on using the Text Mining features of SSIS to rapidly create a Folksonomy with Managed Metadata in SharePoint 2010. The talk was in two parts, the first being an overview of Managed Metadata Services in SharePoint 2010, the second half was a proof of concept for using the Text Mining features in SSIS to extract terms from Word 2007/2010 documents (.docx). In speaking afterwards with Adam Cogan ([blog](http://www.ssw.com.au/ssw/standards/) \| [twitter](http://twitter.com/#!/adamcogan)) he thought I was missing a good story to really sell the concept, and as I'm happy to take free advice here it is:

So, I've got a large amount of Word documents (.docx) in a file share or SharePoint document library and I really want to use the new Managed Metadata Services in SharePoint 2010. Due to the large amount of documents I need a quick way of creating a set of Terms in the Term store and then tag the appropriate Term to a document.

The proof of concept was to use the out of the box Text Mining (Term Extraction) feature in SQL Server Integration Services to discover the key terms in each document, upload the documents to a document library and then apply the terms to a Managed Metadata column.

[![]({{site.baseurl}}/assets/img/TextMiningFlow.png)]({{site.baseurl}}/assets/img/TextMiningFlow.png)

The solution is made up into the following three SSIS packages:


### Step 1





	
  1. Open a word document using the [OpenXML v2 SDK](http://www.microsoft.com/downloads/en/details.aspx?FamilyID=c6e744e5-36e9-45f5-8d8c-331df206e0d0&displaylang=en)

	
  2. Get the key terms using the Term Extraction transform

	
  3. Insert the Terms and Score into a SQL Server table




### Step 2





	
  1. Select the terms from SQL Server

	
  2. Add the terms to a Term Set in Managed Metadata Services (SharePoint)




### Step 3





	
  1. Open the word document using OpenXML v2 SDK

	
  2. Extract the Terms and compare them to the Terms in the Term Set

	
  3. Upload the Word document to a Document Library and apply the Managed Metadata


[![]({{site.baseurl}}/assets/img/Step3.png)]({{site.baseurl}}/assets/img/Step3.png)

Although the overall process could easily be done in one single package, I've deliberately split it up in to the three parts. This was done to allow for extra (manual) analysis to be undertaken between each step. From my example set of 44 documents the Term Extraction transform identified just over 2100 terms, with a large percentage of them being rubbish. If I had allowed them all to be added to a term store it would make managing them a pain. Taking the time to get your terms in good order first, then uploading and then tagging the documents will save time in the long-term.

[The full solution can be downloaded from here (.zip 94KB)]({{site.baseurl}}/assets/SSIS-Folksonomy.zip). Don't hesitate to ask any questions, and if you find this useful then please let me know.
