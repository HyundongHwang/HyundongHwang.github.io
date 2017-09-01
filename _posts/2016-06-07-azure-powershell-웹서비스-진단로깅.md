---
layout: post
title: 160607 azure powershell 웹서비스 진단로깅
comments: true
tags:
- .net
- c#
- ef
- entityframework
- db
- database
- mssql
- sqlserver
---


<!-- TOC -->

- [웹서비스 진단로깅](#웹서비스-진단로깅)
- [로그인](#로그인)
- [웹사이트 리스팅](#웹사이트-리스팅)
- [웹사이트 로그 파일로 저장, 확인](#웹사이트-로그-파일로-저장-확인)
- [웹사이트 로그파일 스트림으로 보기](#웹사이트-로그파일-스트림으로-보기)

<!-- /TOC -->



# 웹서비스 진단로깅



# 로그인

```powershell
PS C:\WINDOWS\system32> Add-AzureAccount

Id                  Type Subscriptions                        Tenants
--                  ---- -------------                        -------
hhd2002@hotmail.com User 8f104cad-0000-0000-0000-a3ad0b5a99eb {a0abba16-0000-0000-0000-0d3e766803b1}
```


# 웹사이트 리스팅

```powershell
PS C:\temp> Get-AzureWebsite

Name       : hhdyidbot
State      : Running
Host Names : {hhdyidbot.azurewebsites.net}
```



# 웹사이트 로그 파일로 저장, 확인

```powershell
PS C:\temp> Save-AzureWebsiteLog -Name hhdyidbot

PS C:\temp> ls .\logs.zip

    디렉터리: C:\temp

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----     2016-06-07   오후 6:41          38908 logs.zip
```



# 웹사이트 로그파일 스트림으로 보기

```powershell
PS C:\temp> Get-AzureWebsiteLog -Name hhdyidbot -Tail
2016-06-07T09:42:51  Welcome, you are now connected to log-streaming service.
2016-06-07T09:42:59 sandboxproc.exe C:\DWASFiles\Sites\hhdyidbot\Temp\applicationhost.config
2016-06-07T09:42:59 env XPROC_TYPENAME=Microsoft.Web.Hosting.Transformers.ApplicationHost.SiteExtensionHelper, Microsoft.Web.Hosting, Version=7.1.0.0, Cultu
re=neutral, PublicKeyToken=31bf3856ad364e35
2016-06-07T09:42:59 env XPROC_METHODNAME=Transform
2016-06-07T09:42:59 Start 'Monaco' site extension transform
2016-06-07T09:42:59 :(6,10), No element in the source document matches '/configuration/system.applicationHost/sites/site[@name='~1hhdyidbot']/application[@p
ath='/Dev']'
2016-06-07T09:42:59 Not executing Remove (transform line 6, 60)
2016-06-07T09:42:59 StartSection Executing Insert (transform line 7, 70)
2016-06-07T09:42:59 on /configuration/system.applicationHost/sites/site[@name='~1hhdyidbot']/application
2016-06-07T09:43:00 Applying to 'site' element (no source line info)
2016-06-07T09:43:00 Inserted 'application' element
2016-06-07T09:43:00 EndSection Done executing Insert
2016-06-07T09:43:00 Successful 'D:\home\SiteExtensions\Monaco\applicationHost.xdt' site extension transform
2016-06-07T09:43:00 sandboxproc.exe complete successfully. Ellapsed = 349.00 ms
2016-06-07T09:43:00  PID[2588] Information Application_Start
2016-06-07T09:43:00  PID[2588] Information Application_BeginRequest
2016-06-07T09:43:00  PID[2588] Information Application_AuthenticateRequest
2016-06-07T09:43:01  PID[2588] Information Application_BeginRequest
2016-06-07T09:43:01  PID[2588] Information Application_AuthenticateRequest
2016-06-07T09:43:01  PID[2588] Information KeyboardController.Get
2016-06-07T09:43:01  PID[2588] Information MessageController.Post postIn : {
  "user_key": "UkMcIPwqsL8H",
  "type": "text",
  "content": "선택 3"
}
2016-06-07T09:43:01  PID[2588] Information Application_BeginRequest
2016-06-07T09:43:01  PID[2588] Information Application_AuthenticateRequest
2016-06-07T09:43:01  PID[2588] Information KeyboardController.Get
t
2016-06-07T09:43:04  PID[2588] Information Application_BeginRequest
2016-06-07T09:43:04  PID[2588] Information Application_AuthenticateRequest
2016-06-07T09:43:04  PID[2588] Information MessageController.Post postIn : {
  "user_key": "UkMcIPwqsL8H",
  "type": "text",
  "content": "ㅂㅅㅈ"
}
```