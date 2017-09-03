---
layout: post
title: 160311 IoT Core study
comments: true
tags:
- iotcore
---

<!-- TOC -->

- [MS, IoT 전략 및 ‘윈도우 10 IoT 에디션’ 출시 발표](#ms-iot-전략-및-윈도우-10-iot-에디션-출시-발표)
- [rp2 pr3 핀매핑](#rp2-pr3-핀매핑)
- [win32 -> uwp 코드 포팅 분석툴](#win32---uwp-코드-포팅-분석툴)
- [iot core 용어사전](#iot-core-용어사전)
- [wifi ap profile을 pc에서 디바이스로 옮겨서 연결](#wifi-ap-profile을-pc에서-디바이스로-옮겨서-연결)
- [디바이스와 powershell 세션 연결](#디바이스와-powershell-세션-연결)
- [호환되는 하드웨어 리스트](#호환되는-하드웨어-리스트)
- [BLE & GATT 프로필 샘플](#ble--gatt-프로필-샘플)
- [파일공유서비스 켜기/끄기 smb프로토콜](#파일공유서비스-켜기끄기-smb프로토콜)
- [ftp 서버로 사용](#ftp-서버로-사용)
- [iot core pro 상업용 라이센스 페이지](#iot-core-pro-상업용-라이센스-페이지)
- [uwp api 중에 iot core에서 사용할 수 없는것](#uwp-api-중에-iot-core에서-사용할-수-없는것)
- [Unified Write Filter : 하드디스크 보안관(?) 사용하기](#unified-write-filter--하드디스크-보안관-사용하기)

<!-- /TOC -->


## MS, IoT 전략 및 ‘윈도우 10 IoT 에디션’ 출시 발표
http://platum.kr/archives/56200



## rp2 pr3 핀매핑
http://ms-iot.github.io/content/en-US/win10/samples/PinMappingsRPi2.htm

GPIO 
As an example, the following code opens GPIO 5 as an output and writes a digital ‘1’ out on the pin:

Serial UART
Pin 8 - UART0 TX
Pin 10 - UART0 RX

I2C Bus
Pin 3 - I2C1 SDA
Pin 5 - I2C1 SCL

SPI Bus
Pin 19 - SPI0 MOSI
Pin 21 - SPI0 MISO
Pin 23 - SPI0 SCLK
Pin 24 - SPI0 CS0
Pin 26 - SPI0 CS1


## win32 -> uwp 코드 포팅 분석툴
http://ms-iot.github.io/content/en-US/win10/tools/IoTAPIPortingTool.htm
`IoTAPIPortingTool.exe <path> [-os].`


## iot core 용어사전
http://ms-iot.github.io/content/en-US/win10/Glossary.htm


## wifi ap profile을 pc에서 디바이스로 옮겨서 연결
http://ms-iot.github.io/content/en-US/win10/SetupWiFi.htm
```bash
# find the name of the profile you just added
PC> netsh wlan show profiles 

# This will export the profile to an XML file
PC> netsh wlan export profile name=<your profilename> 

# Connect to your device using PowerShell and add the new WiFi profile to your device by executing the following commands
DEVICE> netsh wlan add profile filename=<copied XML path>
DEVICE> netsh wlan show profiles

# Connect the Windows 10 IoT Core device to wireless network via netsh
DEVICE> netsh wlan connect name=<profile name>

# Verify that your device is connected to the wireless network and can reach the internet
DEVICE> netsh wlan show interfaces
DEVICE> ipconfig /all
DEVICE> ping /S <your WiFi adapter ip address> bing.com
```


## 디바이스와 powershell 세션 연결
http://ms-iot.github.io/content/en-US/win10/samples/PowerShell.htm
```bash
PC> net start WinRM
PC> Set-Item WSMan:\localhost\Client\TrustedHosts -Value <machine-name or IP Address>

# default password: p@ssw0rd
PC> Enter-PSSession -ComputerName <machine-name or IP Address> -Credential <machine-name or IP Address or localhost>\Administrator
```

알려진 이슈들

1. 
문제 : Appx, NetAdapter, NetSecurity, NetTCPIP, PnpDevice. 가 없다고 나옴...
해결책 : RemoteSigned로 실행권한 변경 
https://technet.microsoft.com/en-us/library/ee176961.aspx.

2. 
`Import-Module NetAdapter -Force` 처럼 Force붙일것



## 호환되는 하드웨어 리스트
http://ms-iot.github.io/content/en-US/win10/SupportedInterfaces.htm



## BLE & GATT 프로필 샘플
http://ms-iot.github.io/content/en-US/win10/samples/BLEGatt.htm

http://www.ti.com/tool/cc2541dk-sensor
여러가지 센서가 달려있는 개발용 센서뭉치 비콘



## 파일공유서비스 켜기/끄기 smb프로토콜
http://ms-iot.github.io/content/en-US/win10/samples/SMB.htm
```bash
# 파일공유끄기
DEVICE> reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\services\lanmanserver /v Start /t REG_DWORD /d 0x3 /f

# 파일공유켜기
DEVICE> reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\services\lanmanserver /v Start /t REG_DWORD /d 0x2 /f
```



## ftp 서버로 사용
http://ms-iot.github.io/content/en-US/win10/samples/FTP.htm
```bash
DEVICE> start C:\Windows\System32\ftpd.exe <PATH_TO_DIRECTORY>
```



## iot core pro 상업용 라이센스 페이지
https://www.windowsforiotdevices.com/



## uwp api 중에 iot core에서 사용할 수 없는것
http://ms-iot.github.io/content/en-US/win10/UnavailableApis.htm
BackgroundMediaPlayer
SpeechRecognizer
FileOpenPicker
FileSavePicker
FolderPicker
HardwareIdentification
CoreWindowDialog
InkManager



## Unified Write Filter : 하드디스크 보안관(?) 사용하기
http://ms-iot.github.io/content/en-US/win10/UWF.htm