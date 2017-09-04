---
layout: post
title: 170619 .net 프로젝트에서 dll hell 을 해결하는 방법
comments: true
tags:
- .net
- bindingRedirect
- GAC
- nuget
- study
---


<br>
<br>
<br>



facebook에서 node npm 종속성을 관리하는 글(https://news.realm.io/kr/news/mobilization-konstantin-raev-taming-node_modules-at-facebook/) 을 읽고 문득 .net의 nuget에서는 어떨까 찾아봤는데
의외로 깔끔한 해결방법이 없는것 같아서 stackoverflow에 질문 던져봤다.

https://stackoverflow.com/questions/44622743/when-using-nuget-how-can-i-solve-dll-hell

꽤 갑론을박이 있었는데 2가지 정도로 해결방안이 요약된다.

1.
버전충돌이 발생하는 dll들을 GAC에 설치해서 사용하도록 함.
이 경우 설치 프로그램이 나서서 gacutil 로 한개한개 전역적으로 설치를 해주는 방식이라 번거롭기는 하지만 버전충돌 문제는 확실히 해결해 줄수 있다.

2.
프로젝트마다 bindingRedirect 을 작성하여 자신이 참조하는 정확한 버전을 적어줌.
GAC 설치보다 요즘은 더 선호되는 방식이라고 하는데 일단 deploy 디렉터리에 파일이 분리되서 설치된다든지 하는 구분이 없는 상태라 살짝 불안하다. 그렇지만 .net core에서 GAC가 없기도 하고 어쨋든 충돌버전을 nuget이 알아서 통합될수 있는 버전으로 자동으로 관리해준다고 해서 이쪽으로 써야 할것 같긴한데 충돌나는 파일중 한가지로만 설치되기 때문에 불안함을 떨칠수는 없다.

그래서 어쨌든 확실한 1번 방법을 테스트 해 보았다.
답글에 GAC 테스트했던 내용들이 기술된다.


<br>
<br>
<br>

<script src="https://htmlpartitionsync.azurewebsites.net/api/PartitionJs?url=https%3A%2F%2Fstackoverflow.com%2Fquestions%2F44622743%2Fwhen-using-nuget-how-can-i-solve-dll-hell&amp;xpath=%2F%2F*%5B%40id%3D%22content%22%5D"></script>
