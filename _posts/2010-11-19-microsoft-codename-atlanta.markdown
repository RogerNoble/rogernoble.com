---
author: Roger
comments: true
date: 2010-11-19 04:14:46+00:00
excerpt: Atlanta is a new cloud based monitoring service for SQL Server 2008 and up.
  Set to be released in the Denali time frame, the goal of Atlanta is to be able to
  monitor and analyse all SQL Server instances within an organisation and provide
  alerts in order to monitor SQL Server.
layout: post
link: http://www.rogernoble.com/2010/11/19/microsoft-codename-atlanta/
slug: microsoft-codename-atlanta
title: Microsoft Codename "Atlanta"
wordpress_id: 13
categories:
- Technology
tags:
- Atlanta
- Denali
- SQL Server
---

Atlanta is a new cloud based monitoring service for SQL Server 2008 and up. Set to be released in the Denali time frame, the goal of Atlanta is to be able to monitor and analyse all SQL Server instances within an organisation and provide alerts in order to monitor SQL Server.

More info can be found here: [http://onlinehelp.microsoft.com/en-us/atlanta/ff962512.aspx](http://onlinehelp.microsoft.com/en-us/atlanta/ff962512.aspx)

[](http://onlinehelp.microsoft.com/en-us/atlanta/ff962512.aspx)Its currently running as an open beta, and signing up is relatively painless here: [https://www.microsoftatlanta.com/](https://www.microsoftatlanta.com/)

[](https://www.microsoftatlanta.com/)Installation is relatively straight forward and consists of two separate components.

The first is the Agent, which is installed on every server  with a SQL instance installed. Its the agent that monitors all activity in SQL Server.

The second component is the Gateway. The Gateway is one or more internet connected server(s) and is used by the Agents when they have something to report. The Gateway is connected to the cloud and secured to a Windows Live ID via a certificate that is generated via the dashboard. During installation the certificate file is all that is required to set the connection. Additional servers can also be added using the same certificate.

Setting up both the Agent and the Gateway is very straight forward with no restart required. Once installed the information gathered is access via a silverlight enabled dashboard which displays a list of alerts for all servers registered. Access to server configurations are also available as well as a full history of past configurations.

Of course the first thing that jumps to anyone's mind when they think of their data being pushed to the cloud is how is this secured? As Brent Ozar rightly points out:


<blockquote>Some of my clients can’t let _their own developers_ get access to queries running on the production server, let alone send them offsite to servers that could be in any geographic location - [http://www.brentozar.com/archive/2010/11/microsoft-atlanta-cloudbased-sql-server-monitoring/](http://www.brentozar.com/archive/2010/11/microsoft-atlanta-cloudbased-sql-server-monitoring/)</blockquote>


I had the same initial fear when I first heard about Atlanta and would have to agree with Brent's conclusion - that this would be really useful for small business with no full time DBA. I know that as a consultant who is at times required to jump in and fix a problem, I could be far more effective with a remote dashboard which allows me to see what's going on.

For me Atlanta is a product that I can defiantly see has some great potential uses. As far as features go I certainly hope there is more in store. One limitation that I found was the lack of being able to assign other Windows Live ID to the same "set" of servers, unless I've missed something I can't see how to do that just yet.
