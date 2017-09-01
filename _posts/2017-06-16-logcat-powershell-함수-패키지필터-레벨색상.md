---
layout: post
title: 170616 logcat powershell 함수(패키지필터, 레벨색상)
comments: true
tags:
- logcat
- android
- powershell
- study
---

<br>
<br>
<br>

<!-- TOC -->

- [개요](#개요)
- [기능](#기능)
- [사용법](#사용법)
- [구현](#구현)
- [전체설치](#전체설치)
- [추가작업](#추가작업)

<!-- /TOC -->

<br>
<br>
<br>

## 개요
- 안드로이드 스튜디오에서 logcat 쓸때 로그레벨(Error, Info, Debug, Verbose ...)별로 폰트컬러 구분 안되서 불편
	- 중요한 로그를 놓치기 쉬움
- 외부 쉘로 나와서 logcat을 띄워보면 안드로이드 스튜디오때 처럼 패키지이름으로 필터링이 안됨.
	- logcat 옵션에 패키지이름으로 필터를 거는 기능은 원래 없음.
	- 로그가 너무 많아서 내 앱의 로그만 확인하는 것은 거의 불가능.

<br>
<br>
<br>


## 기능
- 파워쉘에서도 패키지이름 필터링
	1. 디바이스에서 모든 프로세스 이름, pid 출력
	2. 패키지이름으로 검색된 항목의 pid 찾기
	3. pid를 이용하여 logcat 결과를 다시 필터링

- 로그레벨별로 컬러를 다르게함
	
	| level | font color |
	| :-------- | :------ |
	| Fatal, Error | Red |
	| Warning | Yellow |
	| Information | Green |
	| Debug | Gray |
	| Verbose | White |

- 로그를 출력하기전 디바이스내의 로그를 클리어하는 기능을 옵션으로 제공


<br>
<br>
<br>


## 사용법

```powershell
PS C:\hhdps> hhdandroid-adb-logcat -PACKAGE_NAME com.hhd2002.hhdtest -LOG_LEVEL V -CLEAR_LOG
```

![](http://s21.postimg.org/t2b9g3vef/screenshot_2017_06_17_at_00_13_27.png)

<br>
<br>
<br>



## 구현

```powershell
<#
.SYNOPSIS
.EXAMPLE
#>
function hhdandroid-adb-logcat
{
    [CmdletBinding()]
    param
    (
        [Parameter(Mandatory=$true, ValueFromPipeline=$true, ValueFromPipelinebyPropertyName=$true)]
        [System.String]
        $PACKAGE_NAME,

        [parameter(Mandatory = $false, ValueFromPipeline=$true, ValueFromPipelinebyPropertyName=$true)]
        [ValidateSet("E", "W", "I", "D", "V")]
        [string]
        $LOG_LEVEL = "V",

        [parameter(Mandatory=$false, ValueFromPipeline=$true, ValueFromPipelinebyPropertyName=$true)]
        [switch]$CLEAR_LOG = $false
    )



    if ($CLEAR_LOG) {
        adb -d logcat -c
    }
    
    $pidStr = (adb shell ps | sls $PACKAGE_NAME).ToString().Split(" ", [System.StringSplitOptions]::RemoveEmptyEntries)[1]

    write "PID : $pidStr start logcat ..."
    write ""
    write ""
    write ""

    adb -d logcat *:$LOG_LEVEL | sls $pidStr | 
    foreach {
        if ($_ -match "$pidStr\s\d*\sF") {
            Write-Host $_ -ForegroundColor Red
        } elseif ($_ -match "$pidStr\s\d*\sE") {
            Write-Host $_ -ForegroundColor Red
        } elseif ($_ -match "$pidStr\s\d*\sW") {
            Write-Host $_ -ForegroundColor Yellow
        } elseif ($_ -match "$pidStr\s\d*\sI") {
            Write-Host $_ -ForegroundColor Green
        } elseif ($_ -match "$pidStr\s\d*\sD") {
            Write-Host $_ -ForegroundColor Gray
        } elseif ($_ -match "$pidStr\s\d*\sV") {
            Write-Host $_ -ForegroundColor White
        } else {
            Write-Host $_ -ForegroundColor White
        }
    }
}
```

<br>
<br>
<br>



## 전체설치

- `hhdandroid-adb-logcat` 함수 이외에도 유용한 안드로이드 파워쉘모듈들이 있고, 전체설치 하려면...
	- https://github.com/HyundongHwang/hhdps

<br>
<br>
<br>


	
## 추가작업

일단 현재로 꼭 필요한 기능은 구현되었지만 좀더 욕심내 본다면 ...
- 디바이스 접속시 기본정보 출력
	- app info
		- package name
		- file path
		- 최초설치시각
		- 최근사용시각 
		- ...
	- device info
		- device id
		- 제조사 정보
		- 통신사 정보
		- ...
	- network info
		- 네트워크 상태
		- mac address
		- ip address
		- ...
- 각 로그들에 threadid 정보 추가
- ...


