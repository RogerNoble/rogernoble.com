---
author: Roger
comments: true
date: 2010-11-23 05:32:26+00:00
layout: post
link: http://www.rogernoble.com/2010/11/23/my-sql-server-execution-plan-is-a-lier/
slug: my-sql-server-execution-plan-is-a-lier
title: My SQL Server Execution plan is a lier!
wordpress_id: 20
categories:
- Technology
tags:
- AdSSUG
- Denali
- SQL Server
- T-SQL
---

In preparation for [my talk today](http://www.sqlserver.org.au/Events/ViewEvent.aspx?EventId=502) at the Adelaide SQL Server User Group (AdSSUG) on what's new in SQL11, I've been putting together a few T-SQL demos based on the talk given by[ Tobias Ternström at the PASS Summit 2010](http://sqlpass.eventpoint.com/topic/details/DBA346M).

One new feature that I'm really liking is the new OFFSET operator so I thought I would give it a closer look comparing it to traditional paging methods.

To start I put together a simple query using the AdventureWorks2008 database just to see the execution plan that was generated -

    
    select CustomerID, StoreID, TerritoryID, AccountNumber, ModifiedDate
    from Sales.Customer
    order by CustomerID
    offset 10 rows
    fetch next 10 rows only


With the plan as follows:

[![Execution plan from query using the new OFFSET syntax]({{site.baseurl}}/assets/img/OFFSET-Plan.png)]({{site.baseurl}}/assets/img/OFFSET-Plan.png)

I then compared the query with the current equivalent using a CTE -

    
    WITH a AS ( -- CTE
    	SELECT ROW_NUMBER() OVER (ORDER BY CustomerID) AS RowNum, -- Ranking by CustomerID
    	CustomerID, StoreID, TerritoryID, AccountNumber, ModifiedDate
    	from Sales.Customer
    )
    SELECT a.CustomerID, a.StoreID, a.TerritoryID, a.AccountNumber, a.ModifiedDate
    FROM a
    WHERE a.RowNum BETWEEN 10 AND 20
    ORDER BY RowNum;


When comparing the two execution plans the OFFSET clause performed slightly better 0.003964 vs. 0.0033952 excellent!
So I then decided to raise the stakes a little, to see what happens when paging a much larger data set.

I created a test table with about 2 million rows -

    
    -- Create a table of numbers
    create table dbo.nums (num int primary key);
    insert dbo.nums values (1);
    go
    insert dbo.nums select num + count( * ) over () from dbo.nums;
    go 20
    
    create table dbo.MillionRows
    (ID int identity(1,1) primary key,
    someDate datetime not null,
    someVarchar varchar(20) not null)
    
    -- Insert 2000000 rows
    insert dbo.MillionRows
    select dateadd(second, num, '20000101') as someDate, 'Number ' + cast(num as varchar(10)) as someVarchar
    from dbo.nums
    where num <= 2000000;


I then ran both paging queries to collect 20 000 rows this time with the time statistics turned on -

    
    SET STATISTICS TIME ON
    GO
    WITH a AS ( -- CTE
    	SELECT
    	ROW_NUMBER() OVER (ORDER BY ID) AS RowNum,
    	ID, someDate, someVarchar
    	from dbo.MillionRows
    )
    SELECT a.ID, a.someDate, a.someVarchar
    FROM a
    WHERE a.RowNum BETWEEN 600001 AND 620000
    ORDER BY RowNum;
    
    -- New way
    select ID, someDate, someVarchar
    from dbo.MillionRows
    order by ID
    offset 600000 rows
    fetch next 20000 rows only;
    
    SET STATISTICS TIME OFF;
    GO


Once I ran it I got a surprise, upon looking at the execution plan the OFFSET paging performs horribly.

[![]({{site.baseurl}}/assets/img/OFFSET-Plan-Compared-300x93.png)]({{site.baseurl}}/assets/img/OFFSET-Plan-Compared.png)

But looking at the execution times reveals the exact opposite -
<table style="border: 1px solid black;" >
<tbody >
<tr style="border: 1px solid black;" >
Method
Estimated Subtree Cost
Actual Execution Times
</tr>
<tr style="border: 1px solid black;" >
CTE

<td >0.0038488
</td>

<td >CPU time = 703 ms,  elapsed time = 1158 ms.
</td>
</tr>
<tr >
OFFSET

<td >2.93175
</td>

<td >CPU time = 172 ms,  elapsed time = 608 ms.
</td>
</tr>
</tbody>
</table>
Something isn't quite right here...

To get to the bottom of the problem I asked [Rob Farley](http://sqlblog.com/blogs/rob_farley) for some help and quickly discovered that the Query Optimiser had incorrectly estimated the amount of rows returned by the clustered index scan causing the plan to wrongly assume that the CTE was better!

So the good news is that is safe to use the OFFSET clause :)

[![CTE Bad Estimate]({{site.baseurl}}/assets/img/CTE-Bad-Estimate.png)]({{site.baseurl}}/assets/img/CTE-Bad-Estimate.png)

For more info on Paging and performance have a look at this great post by Dave Ballantyne - [http://sqlblogcasts.com/blogs/sqlandthelike/archive/2010/11/19/denali-paging-key-seek-lookups.aspx](http://sqlblogcasts.com/blogs/sqlandthelike/archive/2010/11/19/denali-paging-key-seek-lookups.aspx)
