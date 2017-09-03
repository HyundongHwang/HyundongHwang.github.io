---
layout: post
title: 151208 Resharper 단축키 망가질때 해결법
comments: true
tags:
- .net
- resharper
---

<!-- TOC -->


<!-- /TOC -->

resharper 단축키 망가지면 개발 못할듯이 힘들다.
나도 어느날 갑자기 단축키가 안되는 순간이 왔는데
resharper, visual studio 리셋해도 안되더라...

- visual studio reset
https://msdn.microsoft.com/en-us/library/ms241273.aspx

답은 여기서 찾았다.
http://stackoverflow.com/questions/1596328/resharper-alt-enter-not-working

```text
ReSharper > Options > Environment > Keyboard & Menus
ReSharper Platform keyboard scheme: Visual Studio
Apply Scheme
Save
```