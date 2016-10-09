---
author: Roger
comments: true
date: 2010-11-19 05:41:01+00:00
layout: post
link: http://www.rogernoble.com/2010/11/19/denali-t-sql-enhancements/
slug: denali-t-sql-enhancements
title: Denali T-SQL Enhancements
wordpress_id: 16
categories:
- Technology
tags:
- Denali
- SQL Server
- T-SQL
---

While T-SQL hasn't been the major focus in Denali there are still a few new things that should at the very least raise an eyebrow.

The first and I think most useful is the new paging syntax. Gone are the days of using a CTE with a ROW_NUMBER ranking function in order to page returned rows. The new syntax provides a much more elegant solution. For example:

    
    select CustomerID, AccountNumber, CustomerType
    from Sales.Customer
    order by CustomerID
    offset 10 rows --Where to start page
    fetch next 10 rows only --How many rows to return


Will return rows 11  - 20 from the Sales.Customer table. It's worth noting that the ORDER BY and OFFSET both required in order for it to work.


### Throw


Another useful feature that most developers will notice is the inclusion of the THROW clause. One of the great uses for this is when using a try/catch in a stored procedure that is called by another stored procedure. In the following example:

    
    begin try
    	RAISERROR('Error', 18, 1);
    end try
    begin catch
    	--handle in some way
    	throw
    end catch


The calling or parent stored procedure can still be notified of the exact exception, but the child can still perform any necessary clean-up.


### Containment


Since forever the issue of moving or restoring databases from different environments has been a problem. The most difficult of course being collation mismatches and missing roles. Denali aims to bring an end to the pain by introducing the CONTAINMENT option.

Databases can now be created with the option of a containment level, in Denali the only supported containment is PARTIAL meaning non-enforced containment (The database will still let you create un-contained entities). For example:

    
    CREATE DATABASE [Contained] SET CONTAINMENT = PARTIAL;


It's also possible to create per database users without a SQL logon. For example:

    
    CREATE USER [DatabaseUser] WITH PASSWORD = 'DB1';


For more info see Aaron Bertrans in depth look on Contained Databases - [http://sqlblog.com/blogs/aaron_bertrand/archive/2010/11/16/sql-server-v-next-denali-contained-databases.aspx](http://sqlblog.com/blogs/aaron_bertrand/archive/2010/11/16/sql-server-v-next-denali-contained-databases.aspx)


### Sequence Generators


Sequence generators are something that might not be new in other database offerings, but has made it to SQL Server in Denali. Used as an alternative to server generated identity values the CREATE SEQUENCE syntax creates a unique identity that can be accessed globally. See here for a detailed run down on how it can be used - [http://www.sergeyv.com/blog/archive/2010/11/09/sql-server-sequence-generators.aspx](http://www.sergeyv.com/blog/archive/2010/11/09/sql-server-sequence-generators.aspx)

Other T-SQL enhancements of note are support for UTF-16 with new collations (marked as <collation>_SC for Special Character) and circular arc segments for spacial data types. See here for more info - [http://msdn.microsoft.com/en-us/library/cc645577(v=SQL.110).aspx](http://msdn.microsoft.com/en-us/library/cc645577(v=SQL.110).aspx)
