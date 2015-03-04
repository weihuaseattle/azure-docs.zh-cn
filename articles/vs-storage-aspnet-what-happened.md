﻿<properties title="Getting Started with Azure Storage" pageTitle="Azure 存储入门" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="vs-what-happened" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
> - [入门](/documentation/articles/vs-storage-aspnet-getting-started-blobs/)
> - [发生了什么情况](/documentation/articles/vs-storage-aspnet-what-happened/)

###<span id="whathappened">我的项目发生了什么情况？</span>

##### 已添加引用

Azure 存储 NuGet 包已添加到你的 Visual Studio 项目。  
此包添加了以下 .NET 引用：

- Microsoft.Data.Edm
- Microsoft.Data.OData
- Microsoft.Data.Services.Client
- Microsoft.WindowsAzure.Configuration
- Microsoft.WindowsAzure.Storage
- Newtonsoft.Json
- System.Data
- System.Spatial

#####已添加 Azure 存储的连接字符串 
在项目的 web.config 文件中，已使用选定存储帐户的连接字符串和密钥创建了一个元素。

有关详细信息，请参阅 [ASP.NET](http://www.asp.net)。