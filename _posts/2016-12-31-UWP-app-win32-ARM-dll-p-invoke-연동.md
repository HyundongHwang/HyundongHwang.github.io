---
layout: post
title: 161231 UWP-app, win32-ARM-dll p/invoke 연동
comments: true
tags:
- uwp
- win32
- arm
- p/invoke
- study
---

<!-- TOC -->

- [개요](#개요)
- [구성](#구성)
- [MySimpleNativeDll.dll 설정](#mysimplenativedlldll-설정)
- [MyNativeDllUwpIntegApp 설정](#mynativedlluwpintegapp-설정)
- [MySimpleNativeDll.dll 구현](#mysimplenativedlldll-구현)
- [MyNativeDllUwpIntegApp 구현](#mynativedlluwpintegapp-구현)
- [자세한 설명은 생략...](#자세한-설명은-생략)

<!-- /TOC -->

<br>
<br>
<br>

## 개요

- 회사일로 라즈베리파이3-ARM 위에 Win10-IoTCore를 올려서 UWP-app(이하 MyNativeDllUwpIntegApp)을 만들고 있음.
- 그런데 winxp, win7에서 사용하던 native-legacy-dll 이 있어서 이를 MyNativeDllUwpIntegApp에서 사용해야 하는 상황임.
- 이걸 어떻게 붙일까 고민시작...
    1. c++/cx 를 이용해서 기존코드를 좀 변경해서 MySimpleNativeDll.winmd 로 만들어서 MyNativeDllUwpIntegApp 에 붙임
    2. 기존코드 그대로 ARM으로만 빌드해서 바로 p/invoke 해서 사용함.
- 2번(=ARM-dll-build) 방식으로 하기로 결정
    - 기존코드 변경이 전혀 없어서(!!!) 편하고 안전함.
    - native, managed 간 런타임 정지점 디버깅 기능도 

## 구성

## MySimpleNativeDll.dll 설정

## MyNativeDllUwpIntegApp 설정

## MySimpleNativeDll.dll 구현

## MyNativeDllUwpIntegApp 구현

## 자세한 설명은 생략...
- https://github.com/HyundongHwang/MyNativeDllUwpIntegApp