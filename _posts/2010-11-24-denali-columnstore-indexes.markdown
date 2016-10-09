---
author: Roger
comments: true
date: 2010-11-24 22:31:42+00:00
layout: post
link: http://www.rogernoble.com/2010/11/25/denali-columnstore-indexes/
slug: denali-columnstore-indexes
title: Denali Columnstore Indexes
wordpress_id: 18
categories:
- Technology
tags:
- Denali
- Indexing
- SQL Server
---

Denali introduces a new type of index called the columnstore. This new type of index is built up on the values across columns instead of traditional row based indexes. As data tends to be less unique across a column it allows the columnstore to efficiently compress and store data. The columnstore is currently read-only, however it can be updated via dropping and recreating the index, or switching in a partition.

Due to the ability to compress and keep the index in memory the Columnstore can give massive (10x, 100x, 1000x...) increase in speed to warehouse queries - but have been warned that not all queries can benefit and some can regress. In general typical star join queries found in a Data Warehouse when only a portion of the columns are selected will get the biggest benefit.

For more info see the following white paper -[ ﻿﻿http://download.microsoft.com/download/8/C/1/8C1CE06B-DE2F-40D1-9C5C-3EE521C25CE9/Columnstore%20Indexes%20for%20Fast%20DW%20QP%20SQL%20Server%2011.pdf](http://download.microsoft.com/download/8/C/1/8C1CE06B-DE2F-40D1-9C5C-3EE521C25CE9/Columnstore%20Indexes%20for%20Fast%20DW%20QP%20SQL%20Server%2011.pdf)
