---
author: Roger
comments: true
date: 2011-02-03 00:41:34+00:00
layout: post
link: http://www.rogernoble.com/2011/02/03/tsql-evenly-distribute-rows-across-a-month-using-a-date-dimension/
slug: tsql-evenly-distribute-rows-across-a-month-using-a-date-dimension
title: TSQL - evenly distribute rows across a month using a date dimension
wordpress_id: 48
categories:
- Technology
tags:
- T-SQL
---

I recently had to solve a particular problem with a client and initially I wasn't quite sure how I was going to do it using set based approach. I was given a spreadsheet with budget data that had to be loaded into a data warehouse. The data had already been pivoted and aggregated by month and was in a format similar to below:
<table cellspacing="1" border="1" >
<tbody >
<tr >
Code
Name
Jul-10
Aug-10
Sep-10
Oct-10
...
</tr>
<tr >

<td >ABC
</td>

<td >DEF
</td>

<td >16
</td>

<td >21
</td>

<td >18
</td>

<td >24
</td>

<td >
</td>
</tr>
</tbody>
</table>
I needed to un-pivot the values for each month in order to be able to map it to the actual days in the month so I can have a count measure. In addition I also needed to duplicate each row twice as each also represented an 'In' and an 'Out' transaction. At first glance it would seem that the simple solution calls for some sort of cursor, but having recently seen Jeff Moden's talk on Numbers Tables at the SQL PASS Summit 2010 I decided instead to solve it using a numbers table but also apply the same logic to the date dimension table that was already in the warehouse. (Jeff has written a great post here: [http://www.sqlservercentral.com/articles/T-SQL/62867/](http://www.sqlservercentral.com/articles/T-SQL/62867/) which pretty much covers what he talked about)

First I created the numbers table (there are many ways to do this)

    
    create table dbo.Numb (n [int] primary key);
    insert into dbo.Numb (n)
    select top(1000)
    	ROW_NUMBER() over(order by (select 0))
    from sys.all_columns;


This simply generates a row number for every row in the source table. sys.all_columns was chosen as it typically contains a lot of columns (over 5000 on my dev instance), but any table will do.

I was then able to use the CROSS APPLY operator to then duplicate the rows per month.

    
    insert into [dw].[Budget] (
    	[ItemCode],
    	[ItemName],
    	[InOutKey],
    	[ActualLocalDateKey],
    	[StartDate])
    -- Jul
    SELECT
    		r.[Code] as [ItemCode]
    		,r.[Name] as [ItemName]
    		,case when m.n%2 = 1 then 'I' else 'O' end as [InOutKey]
    		,d.DateId as [ActualLocalDateKey]
    		,GETDATE() as [StartDate]
      FROM [dbo].[BudgetRaw] r
      cross apply (select d1.DateId from dw.dates d1 where d1.DateId = 20100701) d
      cross apply (select * from dbo.Nums n where n.n <= r.[Count Jul-10] * 2) m -- Multiplied by 2 for 'In' and 'Out'


The first CROSS APPLY gets the first day of the month and the second and first control the duplications. The added bonus of using the numbers table for the In/Out records meant that I was also able to use a simple CASE statement to set the value to be either 'I' or 'O'. By then using UNION ALL and incrementing the date comparer I could then unpivot the table and load in all twelve months. This allowed me to simple create 80 000 rows based on only 131 rows of budget data and in under 2 seconds.

But this didn't solve all my problems. All of my dates were set to the first of the month, but what I really wanted was an even spread across the days of the month. I first thought NTILE would solve my problems, but it has a little quirk. NTILE works great when the count is equal to or greater than the tile size, but when there the count is less than the tile size NTILE will stop short of the full tile size. For example

    
    with TileThis as(
    	select 1 as n
    	union all
    	select n + 1 from TileThis where n < 10)
    select NTILE(30) OVER(order by (select 0)) as [Tiled]
    from TileThis t


will result in
<table cellspacing="1" border="1" >
<tbody >
<tr >
Tiled
</tr>
<tr >

<td >1
</td>
</tr>
<tr >

<td >2
</td>
</tr>
<tr >

<td >3
</td>
</tr>
<tr >

<td >4
</td>
</tr>
<tr >

<td >5
</td>
</tr>
<tr >

<td >6
</td>
</tr>
<tr >

<td >7
</td>
</tr>
<tr >

<td >8
</td>
</tr>
<tr >

<td >9
</td>
</tr>
<tr >

<td >10
</td>
</tr>
</tbody>
</table>
The result is that it is basically a ROW_NUMBER(). Note that there is anything wrong with the output - it's not a bug, just not what I need. The answer was to roll my own NTILE using an example sourced from [Inside Microsoft SQL Server 2008 T-SQL Querying](http://oreilly.com/catalog/9780735626034/) by Itzik Ben-Gan ([blog](http://www.sqlmag.com/blogs/puzzled-by-t-sql.aspx)).

    
    with MonthlyItems as(
       SELECT
          r.[Code] as [ItemCode]
          ,r.[Name] as [ItemName]
          ,r.[Jul-10] as [MonthlyOccurrences]
          ,GETDATE() as [StartDate]
      FROM [dbo].[BudgetRaw] r
    ), MonthlyRowNumb as(
      SELECT m.[ItemCode]
         ,m.[ItemName]
        ,m.[StartDate]
        ,case when a.n%2 = 1 then 'I' else 'O' end as [InOutKey]
        ,d.DateId as [ActualLocalDateKey]
        ,1.* m.[MonthlyOccurrences]*2/(select top(1) [DayOfMonth] from dw.dates where [MonthKey] = d.MonthKey order by [DateId] desc) as [TileSize]
        ,a.n as [RowNumber]
      FROM MonthlyItems m
      cross apply (select d1.DateId from dw.dates d1 where d1.DateId = 20100701) d
      cross apply (select * from dbo.Nums n where n.n <= r.[Count Jul-10] * 2) a -- Multiplied by 2 for 'In' and 'Out'
    )
    insert into [dw].[Budget] (
    	[ItemCode],
    	[ItemName],
    	[InOutKey],
    	[ActualLocalDateKey],
    	[StartDate])
    SELECT
    	e.[ItemCode]
    	,e.[ItemName]
    	,e.[InOutKey]
    	,[ActualLocalDateKey] + CAST((e.[RowNumber] - 1) / e.[TileSize] + 1 AS INT) - 1 AS [ActualLocalDateKey]
    	,GETDATE() as [StartDate]
     from MonthlyFlightsWithTileSize e


As you can see I'm able to use the date dimension to determine the number of days in the month (tile size) and then take advantage of the integer based date column to simply add the tile number to the first day of the month to determine which day each row should be assigned to.
