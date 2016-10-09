---
author: Roger
comments: true
date: 2011-01-11 02:10:15+00:00
layout: post
link: http://www.rogernoble.com/2011/01/11/need-to-create-a-dsn-for-oracle/
slug: need-to-create-a-dsn-for-oracle
title: Need to create a DSN for Oracle?
wordpress_id: 45
categories:
- Technology
tags:
- BIDS
- DSN
- Oracle
- Server 2008 R2
---

I had to create a DSN to an Oracle DB to be used by SSIS yesterday and it was not as easy as it sounds. So here is a quick how-to for anyone else who needs to do this.

It's also important to note that the DSN is on Windows Server 2008 R2 x64.

If you've ever been to oracle.com you'll quickly discover a hive of menus and links for all sorts of things, but what you want is called the Instant Client. You can find it here: [http://www.oracle.com/technetwork/database/features/instant-client/index-097480.html](http://www.oracle.com/technetwork/database/features/instant-client/index-097480.html)

I made the mistake of then grabbing the x64 drivers, but what you really want is the 32 bit version. This is because BIDS is only 32 bit, so it can only see 32 bit DSN's. Selecting the 32 bit version will then take you to a page listing the latest version with about 7 different things under it. Whats you need is the "Instant Client Package - Basic Lite" and "Instant Client Package - ODBC" - unfortunately to download them you have to register with Oracle first.

Extract all three *.zip's into the same folder and place them somewhere accessible, like: c:\oracle\instantclient_x_x (Where x_x is the version number of the Instant Client). Open cmd/powershell as an admin and run** odbc_install.exe**. The last thing to do is set the PATH environmental variable. In explorer right click Computer > Properties. Then Advanced Settings > Environmental Variables. Find the PATH variable, edit it and put at the end ;c:\oracle\instantclient_x_x (the ; is a delimiter).

Ok now to create the DSN. First a file needs to be created called tnsnames.ora that will contain the connection information for the DSN. Create a new file in the instant client directory called tnsnames.ora and paste into it the following:

    
     [TNS Service Name] =
      (DESCRIPTION =
        (ADDRESS_LIST =
          (ADDRESS = (PROTOCOL = TCP)(HOST = [Server Address] )(PORT = [Port Number]
    ))
        )
        (CONNECT_DATA =
          (SID = [Database Name])
        )
      )


Replace the [Service Name], [Server Address], [Port Number] and [Database Name] with the details of your environment.

For 32 bit DSN's they need to be created using the 32 bit **ODBC Data Source Administrator** which can be found here: **%windir%\sysWOW64\odbacd32.exe**. Go to the** System DSN** tab and click Add. Select the **Oracle in instatnclient_x_x **as the driver and fill in the **Data Source Name** and select the **TNS Service Name **using the same name from the tnsnames.ora.

You should now be able to see the DSN listed in BIDS.

Last step when running the package is to make sure the **Use 32 bit runtime** is selected, otherwise you will see the following error:
_ The specified DSN contains an architecture mismatch between the Driver and Application_
