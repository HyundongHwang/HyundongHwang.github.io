---
layout: post
title: 160308 w10 iot core 스터디, cmd line util, os 시작/종료, pssession연결, psdrive마운트
comments: true
tags:
- iotcore
- kakaocat
---

<!-- TOC -->

- [IoT Core Command Line Utils](#iot-core-command-line-utils)
    - [Update account password:](#update-account-password)
    - [Set password](#set-password)
    - [Query and set device name:](#query-and-set-device-name)
    - [Setting startup app:](#setting-startup-app)
    - [Set Boot Option (Headless vs. headed boot):](#set-boot-option-headless-vs-headed-boot)
    - [Device drivers:](#device-drivers)
    - [Services:](#services)
    - [Shutdown/restart device:](#shutdownrestart-device)
    - [Set display resolution](#set-display-resolution)
- [iot 부팅](#iot-부팅)
- [iot core 초기연결](#iot-core-초기연결)
- [장비 접속시 패스워드 스킵하기](#장비-접속시-패스워드-스킵하기)
- [노트북, 디바이스간 파일공유123](#노트북-디바이스간-파일공유123)

<!-- /TOC -->


## IoT Core Command Line Utils
https://ms-iot.github.io/content/en-US/win10/tools/CommandLineUtils.htm

### Update account password:
```bash
net user Administrator [new password]
```

### Set password
```bash
net user [account-username] [new-password]
```

### Query and set device name:
```bash
SetComputerName [new machinename]
```

### Setting startup app:

- `IotStartup list lists` installed applications
- `IotStartup list headed lists` installed headed applications
- `IotStartup list headless lists` installed headless applications
- `IotStartup list [MyApp] list` installed applications that match pattern MyApp
- `IotStartup add` adds headed and headless applications
- `IotStartup add headed [MyApp]` adds headed applications that match pattern MyApp. Pattern must match only one application.
- `IotStartup add headless [Task1]` adds headless applications that match pattern Task1
- `IotStartup remove` removes headed and headless applications
- `IotStartup remove headed [MyApp]` removes headed applications that match pattern MyApp
- `IotStartup remove headless [Task1]` removes headless applications that match pattern Task1
- `IotStartup startup lists` headed and headless applications registered for startup
- `IotStartup startup [MyApp]` lists headed and headless applications registered for startup that match pattern MyApp
- `IotStartup startup headed [MyApp]` lists headed applications registered for startup that match MyApp
- `IotStartup startup headless [Task1]` lists headless applications registered for startup that match Task1
- For further help, try `IotStartup help`

### Set Boot Option (Headless vs. headed boot):
```bash
setbootoption.exe [headed | headless]
```

### Device drivers:
```bash
devcon.exe /?
```

### Services:
```bash
net [start | stop] [service name]
```

### Shutdown/restart device:
To shut down your device, type `shutdown /s /t 0`
To restart the device, use the /r switch instead with the command `shutdown /r /t 0`

### Set display resolution
```bash
SetDisplayResolution [width] [height]
```



## iot 부팅
c:\Program Files (x86)\Microsoft IoT\contfig.txt 복사 -->> 루트


## iot core 초기연결
```bash
PS C:\WINDOWS\system32> net start winrm

PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts -Value 169.254.112.99
WinRM 보안 구성
이 명령에서 WinRM 클라이언트에 대한 TrustedHosts 목록을 수정합니다. TrustedHosts 목록에 있는 컴퓨터가 인증되지 않을 수
있으며 클라이언트에서 자격 증명 정보를 이러한 컴퓨터에 보낼 수도 있습니다. 이 목록을 수정하시겠습니까?
[Y] 예(Y)  [N] 아니요(N)  [S] 일시 중단(S)  [?] 도움말 (기본값은 "Y"): y


PS C:\WINDOWS\system32> Enter-PSSession -ComputerName 169.254.112.99 -Credential 169.254.112.99\Administrator


[169.254.112.99]: PS C:\Data\Users\Administrator> net user Administrator password
The command completed successfully.


[169.254.112.99]: PS C:\Data\Users\Administrator> setcomputername firstrp2
Computer name changed successfully. Please reboot the device for changes to take effect.


[169.254.112.99]: PS C:\Data\Users\Administrator> shutdown /r /t 0
System will shutdown in 0 seconds...


PS C:\temp> Set-Item WSMan:\localhost\Client\TrustedHosts -Value firstrp2


PS C:\temp> Enter-PSSession -ComputerName firstrp2 -Credential firstrp2\administrator


[firstrp2]: PS C:\Data\Users\Administrator\Documents> cd /


[firstrp2]: PS C:\> ls
    Directory: C:\
Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d----l       10/30/2015   5:46 AM                CrashDump
d----l       10/30/2015   5:46 AM                Data
d-----       10/30/2015   5:46 AM                EFI
d----l       10/30/2015   5:46 AM                EFIESP
d-----       10/30/2015   5:46 AM                Program Files
d-----       10/30/2015   5:46 AM                Program Files (x86)
d-----       10/30/2015   5:46 AM                PROGRAMS
d-----       10/30/2015   5:46 AM                SystemData
d-r---       10/30/2015   5:46 AM                Users
d-----       10/30/2015   1:14 PM                Windows
```




## 장비 접속시 패스워드 스킵하기
```bash
PS C:\> $password = ConvertTo-SecureString "password" -AsPlainText -Force


PS C:\> $cred = New-Object System.Management.Automation.PSCredential("firstrp2\administrator", $password)


PS C:\> Enter-PSSession -ComputerName firstrp2 -Credential $cred


[firstrp2]: PS C:\Data\Users\Administrator\Documents>
```





## 노트북, 디바이스간 파일공유

```powershell
PS C:\temp> New-PSDrive -Name firstrp2 -PSProvider FileSystem -Root \\firstrp2\c$ -Credential firstrp2\administrator
Name           Used (GB)     Free (GB) Provider      Root                                               
----           ---------     --------- --------      ----                                               
firstrp2                               FileSystem    \\firstrp2\c$



PS C:\temp> cd firstrp2:\


PS firstrp2:\> ls
    디렉터리: \\firstrp2\c$
Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d----l     2015-10-30   오후 9:46                CrashDump
d----l     2015-10-30   오후 9:46                Data
d-----     2015-10-30   오후 9:46                EFI
d----l     2015-10-30   오후 9:46                EFIESP
d-----     2015-10-30   오후 9:46                Program Files
d-----     2015-10-30   오후 9:46                Program Files (x86)
d-----     2015-10-30   오후 9:46                PROGRAMS
d-----     2015-10-30   오후 9:46                SystemData
d-----     2015-10-30   오후 1:04                temp
d-r---     2015-10-30   오후 9:46                Users
d-----     2015-10-31   오전 5:14                Windows
```