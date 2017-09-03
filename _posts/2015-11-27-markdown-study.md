---
layout: post
title: 151127 markdown study
comments: true
tags:
- markdown
- study
---

<!-- TOC -->

- [마크다운이 지원하는 syntax highlight 언어목록](#마크다운이-지원하는-syntax-highlight-언어목록)
- [마크다운 순서도](#마크다운-순서도)
- [마크다운 시퀀스 다이어그램](#마크다운-시퀀스-다이어그램)

<!-- /TOC -->

마크다운과 marxico사용하면서 배운 고급기능들 정리...

## 마크다운이 지원하는 syntax highlight 언어목록
https://en.support.wordpress.com/code/posting-source-code/
- actionscript3
- bash
- clojure
- coldfusion
- cpp
- csharp
- css
- delphi
- erlang
- fsharp
- diff
- groovy
- html
- javascript
- java
- javafx
- matlab (keywords only)
- objc
- perl
- php
- text
- powershell
- python
- r
- ruby
- scala
- sql
- vb
- xml

## 마크다운 순서도
```csharp
'''flow
start=>start: 시작
end=>end
o1=>operation: 오퍼레이션1
o2=>operation: 오퍼레이션2
o3=>operation: 오퍼레이션3
c1=>condition: 2 or 3 ?

start->o1->c1
c1(yes)->o2->end
c1(no)->o3->end
'''
```

```flow
start=>start: 시작
end=>end
o1=>operation: 오퍼레이션1
o2=>operation: 오퍼레이션2
o3=>operation: 오퍼레이션3
c1=>condition: 2 or 3 ?

start->o1->c1
c1(yes)->o2->end
c1(no)->o3->end
```

## 마크다운 시퀀스 다이어그램
```csharp
'''sequence
클라->서버: GET REQ
note right of 서버: 연산
서버-->클라: GET RES
'''
```

```sequence
클라->서버: GET REQ
note right of 서버: 연산
서버-->클라: GET RES
```
