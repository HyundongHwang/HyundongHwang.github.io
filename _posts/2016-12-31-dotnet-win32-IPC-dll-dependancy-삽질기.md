---
layout: post
title: 161231 .net/win32 IPC, dll dependancy 삽질기
comments: true
tags:
- .net
- win32
- wpf
- native
- zmq
- ipc
- study
- blog
---

<!-- TOC -->

- [개요](#개요)
- [문제점 & 해결책](#문제점--해결책)
    - [winxp, win7 경우, MyWpfApp.exe에서 libzmq.dll 연결초기화 실패](#winxp-win7-경우-mywpfappexe에서-libzmqdll-연결초기화-실패)
    - [winxp, win7 경우, MyMfcApp.exe에서 MyBridge.dll 의 로드실패](#winxp-win7-경우-mymfcappexe에서-mybridgedll-의-로드실패)
    - [win7 경우, vcredist 2015 설치오류](#win7-경우-vcredist-2015-설치오류)
    - [winxp 경우, MyMfcApp.exe에서 MyBridge.dll 로드성공이후 런타임크래시](#winxp-경우-mymfcappexe에서-mybridgedll-로드성공이후-런타임크래시)
- [해결책 정리](#해결책-정리)
- [디버깅 꿀팁(?)](#디버깅-꿀팁)

<!-- /TOC -->

## 개요
- 최근 회사일로 WPF어플리케이션을 제작하고 있는데 외부 MFC어플리케이션 과 IPC를 해야 할일이 있었고, IPC솔루션으로 ZMQ REQ/REP 모델을 적용하기로 했다.
- WPF어플쪽에서는 ZMQ를 직접사용하기로 하고 MFC어플은 외부에서 만든거라서 그들이 연동하기 쉽게 ZMQ초기화, 입출력과 함께 연동로직들을 일부 담아서 win32 dll로 제작해서 배포했다.
- 개발했던 환경은 나는 Win10+VS2015+.NET4.0 에서 WPF어플리케이션을 제작하고 그들은 Win7+VS005+MFC 에서 MFC어플리케이션을 제작해으며 처음 연동했을때 한번에 잘 되서 앞으로도 별 문제 없겠거니 했다.
- 그러다가 XP, 7 하위호환을 위해서 가상머신을 만들어서 테스트하다가 며칠간 삽질을 했고 결국은 해결했지만 그 과정이 험난해서 같은 어려움이 있는 독자들을 위해서 기록으로 남긴다.
- 회사프로젝트 고유이름을 사용하기는 이슈가 있을것 같아서 본문글에서는 리네임을 하기로 한다.
	- WPF 어플르케이션 = MyWpfApp
	- MFC 어플리케이션 = MyMfcApp
	- 연동 dll = MyBridge.dll
- 함께 삽질을 했던 서초동 최과장님께 감사를 드린다.

<br/>
<br/>
<br/>

## 문제점 & 해결책
### winxp, win7 경우, MyWpfApp.exe에서 libzmq.dll 연결초기화 실패
- MyWpfApp.exe 실행은 잘 되는데, zmq 초기화를 하면 DllNotFoundException이 발생했음.
- 원인은 MyWpfApp이 사용하는 libzmq.dll 이 vc2010에서 /MD(MultiThread+Dll) 빌드되었으며 이때문에 vc2010의 crt dll들이 접근가능경로에 존재해야 함.
- win10등의 최신환경일수록 vcredist가 많이 설치되어 있어서 버그발생률이 낮지만 pos처럼 고립된 환경에서 사용되는 os에는 잘 발생할 수 있는 문제임.
    - clrzmq에 포함된 libzmq.dll의 종속성 해결에 대한 문답 : http://stackoverflow.com/questions/4718262/zeromq-dllnotfoundexception-using-net-bindings

```tex
I encountered this exact error on Windows Server 2008. As soon as my code attempted to create a clrzmq (2.2.3) object, Windows tried to load the DLL and failed with the error:

Unable to load DLL 'libzmq': The specified module could not be found.
The DLL is most definitely present. I attempted a variety of solutions with permissions, all of which failed to resolve the problem. Downloading & installing the VS2010 C++ redistributable solved my problem.
```

- 이를 해결하기 위해서 vcredist 2010을 배포할 필요가 있었고 MyWpfApp.exe 가 가진 종속성인 만큼 MyWpfAppInstaller가 배포하도록 수정함.
    - vcredist2010 download : https://www.microsoft.com/ko-kr/download/details.aspx?id=5555

<br/>
<br/>
<br/>

### winxp, win7 경우, MyMfcApp.exe에서 MyBridge.dll 의 로드실패
- MyMfcApp.exe 에서 LoadLibrary("MyBridge.dll") 하면 로드실패 되는 문제가 있었음.
- 윗문제와 마찬가지로 vcredist 미설치 문제로 이번에는 MyWpfApp팀 개발환경인 vc2015에 필요한 vcredist 2015를 설치해서 해결할 수 있는 문제임.
    - 참고로 msdn에서는 관련 dll을 직접배포하지 않고 vcredist 설치를 통해 side by side 기능으로 런타임 바인딩을 하는 것을 권장함.
        - side by side 소개 : https://msdn.microsoft.com/ko-kr/library/fdhkd3a5(v=vs.110).aspx
        - side by side 문제해결 가이드 : https://blogs.msdn.microsoft.com/dsvc/2008/08/07/part-1-troubleshooting-vc-side-by-side-problems/
- MyWpfApp설치프로그램이 vcredist2015를 배포, 설치하여 해결
    - vcredist2015 다운로드 : https://www.microsoft.com/en-us/download/details.aspx?id=53840
    - 엄밀히 따지자면 MyMfcApp이 사용하는 MyBridge.dll이 이를 배포, 설치하는 것이 맞지만 개발편의상 MyWpfApp설치프로그램이 대신하도록 함.

<br/>
<br/>
<br/>

### win7 경우, vcredist 2015 설치오류
- 이럴수가! winxp에는 vcredist2015가 문제 없이 설치되는데 일부 win7에서는 계속 설치실패됨.
- 이 문제는 vs2015+win10에서 빌드할때 종속되는 universalCRT라는 새로운 구조의 crt 종속성이 생겨서 발생한 이슈인데, universalCRT가 기존의 crt에 비해서 너무 변경이 커서 win7에서는 단순히 vcredist2015를 설치하는 것에 앞서서 win7 sp1으로 업데이트, kb-2999226(universalCRT변경이 포함된 윈도우 업데이트)를 선행해서 해 줘야 함. (OMG!!!)
    - 사실 이렇게 많은 업데이트를 pc에 설치하는 것은 시간적으로나 프로세스상으로나 큰 부담이 됨.
- 3가지 대안 솔루션이 있었음.
    1. universalCRT 관련 dll 강제 복사
        - WindowsSDK10 에 포함된 dll들을 모두 타겟환경에 배포하는 방식
        - 이전버전과 달리 universalCRT의 dll들은 용량도 크지만 갯수도 20~30개 정도나 되서 상당한 관리부담이 됨.
        - 또한 이많은 dll들이 MyBridge.dll과 같은 경로에 존재해야 하기 때문에 혹시  MyMfcApp가 배포시 dll끼리 이름충돌이 되서 배포사고로 이어질수도 있어서 MyWpfApp 디렉토리 만들어야 해서 관리부담이 가중됨.

```tex
파일 이름	파일 버전	파일 크기	날짜	시간	플랫폼
Api-ms-win-core-file-l1-2-0.dll	10.0.10240.16390	11,616	2015년 7월18일	13:08	x86
Api-ms-win-core-file-l2-1-0.dll	10.0.10240.16390	11,616	2015년 7월18일	13:08	x86
Api-ms-win-core-localization-l1-2-0.dll	10.0.10240.16390	14,176	2015년 7월18일	13:08	x86
Api-ms-win-core-processthreads-l1-1-1.dll	10.0.10240.16390	12,128	2015년 7월18일	13:08	x86
Api-ms-win-core-synch-l1-2-0.dll	10.0.10240.16390	12,128	2015년 7월18일	13:08	x86
Api-ms-win-core-timezone-l1-1-0.dll	10.0.10240.16390	11,616	2015년 7월18일	13:08	x86
Api-ms-win-core-xstate-l2-1-0.dll	10.0.10240.16390	11,616	2015년 7월18일	13:08	x86
Api-ms-win-crt-conio-l1-1-0.dll	10.0.10240.16390	12,640	2015년 7월18일	13:08	x86
Api-ms-win-crt-convert-l1-1-0.dll	10.0.10240.16390	15,712	2015년 7월18일	13:08	x86
Api-ms-win-crt-environment-l1-1-0.dll	10.0.10240.16390	12,128	2015년 7월18일	13:08	x86
Api-ms-win-crt-filesystem-l1-1-0.dll	10.0.10240.16390	13,664	2015년 7월18일	13:08	x86
Api-ms-win-crt-heap-l1-1-0.dll	10.0.10240.16390	12,640	2015년 7월18일	13:08	x86
Api-ms-win-crt-locale-l1-1-0.dll	10.0.10240.16390	12,128	2015년 7월18일	13:08	x86
Api-ms-win-crt-math-l1-1-0.dll	10.0.10240.16390	22,368	2015년 7월18일	13:08	x86
Api-ms-win-crt-multibyte-l1-1-0.dll	10.0.10240.16390	19,808	2015년 7월18일	13:08	x86
Api-ms-win-crt-private-l1-1-0.dll	10.0.10240.16390	66,400	2015년 7월18일	13:08	x86
Api-ms-win-crt-process-l1-1-0.dll	10.0.10240.16390	12,640	2015년 7월18일	13:08	x86
Api-ms-win-crt-runtime-l1-1-0.dll	10.0.10240.16390	16,224	2015년 7월18일	13:08	x86
Api-ms-win-crt-stdio-l1-1-0.dll	10.0.10240.16390	17,760	2015년 7월18일	13:08	x86
Api-ms-win-crt-string-l1-1-0.dll	10.0.10240.16390	17,760	2015년 7월18일	13:08	x86
Api-ms-win-crt-time-l1-1-0.dll	10.0.10240.16390	14,176	2015년 7월18일	13:08	x86
Api-ms-win-crt-utility-l1-1-0.dll	10.0.10240.16390	12,128	2015년 7월18일	13:08	x86
Api-ms-win-eventing-provider-l1-1-0.dll	10.0.10240.16390	11,616	2015년 7월18일	13:08	x86
Api-ms-win-core-file-l1-2-0.dll	10.0.10240.16390	11,616	2015년 7월18일	13:08	x86
Api-ms-win-core-file-l2-1-0.dll	10.0.10240.16390	11,616	2015년 7월18일	13:08	x86
Api-ms-win-core-localization-l1-2-0.dll	10.0.10240.16390	14,176	2015년 7월18일	13:08	x86
Api-ms-win-core-processthreads-l1-1-1.dll	10.0.10240.16390	12,128	2015년 7월18일	13:08	x86
Api-ms-win-core-synch-l1-2-0.dll	10.0.10240.16390	12,128	2015년 7월18일	13:08	x86
Api-ms-win-core-timezone-l1-1-0.dll	10.0.10240.16390	11,616	2015년 7월18일	13:08	x86
Api-ms-win-core-xstate-l2-1-0.dll	10.0.10240.16390	11,616	2015년 7월18일	13:08	x86
Api-ms-win-crt-conio-l1-1-0.dll	10.0.10240.16390	12,640	2015년 7월18일	13:08	x86
Api-ms-win-crt-convert-l1-1-0.dll	10.0.10240.16390	15,712	2015년 7월18일	13:08	x86
Api-ms-win-crt-environment-l1-1-0.dll	10.0.10240.16390	12,128	2015년 7월18일	13:08	x86
Api-ms-win-crt-filesystem-l1-1-0.dll	10.0.10240.16390	13,664	2015년 7월18일	13:08	x86
Api-ms-win-crt-heap-l1-1-0.dll	10.0.10240.16390	12,640	2015년 7월18일	13:08	x86
Api-ms-win-crt-locale-l1-1-0.dll	10.0.10240.16390	12,128	2015년 7월18일	13:08	x86
Api-ms-win-crt-math-l1-1-0.dll	10.0.10240.16390	22,368	2015년 7월18일	13:08	x86
Api-ms-win-crt-multibyte-l1-1-0.dll	10.0.10240.16390	19,808	2015년 7월18일	13:08	x86
Api-ms-win-crt-private-l1-1-0.dll	10.0.10240.16390	66,400	2015년 7월18일	13:08	x86
Api-ms-win-crt-process-l1-1-0.dll	10.0.10240.16390	12,640	2015년 7월18일	13:08	x86
Api-ms-win-crt-runtime-l1-1-0.dll	10.0.10240.16390	16,224	2015년 7월18일	13:08	x86
Api-ms-win-crt-stdio-l1-1-0.dll	10.0.10240.16390	17,760	2015년 7월18일	13:08	x86
Api-ms-win-crt-string-l1-1-0.dll	10.0.10240.16390	17,760	2015년 7월18일	13:08	x86
Api-ms-win-crt-time-l1-1-0.dll	10.0.10240.16390	14,176	2015년 7월18일	13:08	x86
Api-ms-win-crt-utility-l1-1-0.dll	10.0.10240.16390	12,128	2015년 7월18일	13:08	x86
Api-ms-win-eventing-provider-l1-1-0.dll	10.0.10240.16390	11,616	2015년 7월18일	13:08	x86
Ucrtbase.dll	10.0.10240.16390	901,264	2015년 7월18일	13:08	x86
Ucrtbase.dll	10.0.10240.16390	901,264	2015년 7월18일	13:08	x86
```

2. vc2013으로 빌드
    - 어차피 MyBridge.dll에서 사용하는 crt 함수들에서 최신기술을 사용하는게 없으니 MyWpfApp팀 개발환경을 일부 다운그레이드 시키고 vcredist2013을 배포하면 됨.
    - 다른 대안보다 낫긴한데 역시 MyWpfApp팀에 개발환경을 여러개 셋팅하는게 부담.

3. [최종선택] /MT(MultiThread) 빌드
- MyWpfApp팀에서 MyMfcApp에 배포하는 dll은 필수적으로는 2개로 MyBridge.dll, libzmq.dll 임.
- 이 dll들은 side by side 기능으로 런타임 바인딩을 하기위해서 /MD(MultiThread+Dll) 빌드를 했음.
    - msdn에서는 직접배포보다는 side by side 기능을 사용하여 윈도우 업데이트를 통한 crt의 보안 및 성능업데이트를 받도록 권장함.
        - 이 때문에 visual studio의 기본셋팅도 /MD로 되어 있음
        - 하지만 MyBridge.dll이 다양한 하위환경에 대한 호환성을 지켜야 하는 상황이라 결국 포기하기로 함.
        - MyWpfApp팀에서 배포하는 모든 dll exe 에 대한 crt 라이브러리 종속성을 모두 /MT(MultiThread) 빌드를 통해 제거하고 동시에 윈도우 업데이트 효과도 포기하기로 함.
            - MyBridge.dll 이 성능이나 보안보다는 설치/배포 편의가 더 중요하다는 판단!

- 참고자료
    - win7 sp1 업데이트 : https://www.microsoft.com/ko-KR/download/details.aspx?id=5842
    - 윈도우업데이트 kb-2999226 : https://support.microsoft.com/ko-kr/kb/2999226
    - win10 sdk 다운로드(universalCRT dll 포함) : https://developer.microsoft.com/en-us/windows/downloads/windows-10-sdk
    - universalCRT 소개, 배포가이드 : https://blogs.msdn.microsoft.com/vcblog/2015/03/03/introducing-the-universal-crt/
    - visual studio 링커옵션 /MT /MD ... : https://msdn.microsoft.com/ko-kr/library/2kzt1wy3.aspx

<br/>
<br/>
<br/>

### winxp 경우, MyMfcApp.exe에서 MyBridge.dll 로드성공이후 런타임크래시
- 이제 정말 종속성 문제는 깔끔하게 해결되어 MyMfcApp가 MyBridge.dll을 잘 로드하기 시작함.
- 하지만 winxp에서만 MyMfcApp쪽에서 런타임에 크래시 나는 문제가 발생됨.
- MyBridge.dll 내부에서 CString::Format 을 사용한 코드에서는 크래시가 발생했음.
    - 아직 정확한 원인은 찾지 못했음. 
    - 추정하는 원인으로는 MyMfcApp는 vs2015+mfc+mbcs 조합이고 MyBridge.dll은 vs2015+atl+unicode 조합인데, 이 환경사이의 충돌로 생각됨.
    - 더 자세히는 mfc와 atl의 shared class 에 CString 이 있는데 이 클래스 내부의 구현이 서로 다른데 하필 Format 함수의 구현이 충돌되지 않았나 생각됨.
    - 깊이 찾아보진 않았지만 비슷한 이슈가 있었고 해결은 못한 케이스가 있음.
        - http://stackoverflow.com/questions/4747256/cstring-format-problem-and-differences-in-windows-xp-and-7
- CString::Format대신 sprintf crt 함수로 대채해서 회피함. 향후 여유 있을때 깊이 파서 해결할 것.

<br/>
<br/>
<br/>

## 해결책 정리

```tex
1. 닷넷어플리케이션에서 에서 native dll이 파일 존재하는데 DllNotFoundException이 발생한다면,
-> 
Dependancy Walker 찍어보고 버전에 맞게 vcredist 설치해줌. 
모듈들을 nuget 으로 설치/업데이트 하기 때문에 바이너리 재빌드 하는 것은 소용없고 종속성 해결해 가면서 쓰는게 맞음.

2. 타겟os가 하위버전이면서 다양해서 종속성 챙기기 너무 고생스럽다면,
->
모든 dll과 exe를 /MT 링크옵션으로!

3. dll을 만들었는데 호스트프로세스가 vs2005+mfc 를 사용한 경우라면,
->
CString::Format 에서 크래시 되는지 확인후 sprintf 로 변경할것.
```

<br/>
<br/>
<br/>

## 디버깅 꿀팁(?)
- virtualbox snapshot
    - 타겟os의 상태를 단계별로 저장해 두고 반복적으로 테스트 할 수 있음.
    - vcredist, kb업데이트 등 한번 수행하면 깨끗히 지워서 재 테스트하기 힘든 경우가 많은데 아주 요긴함.
    - http://swkim-linux.blogspot.com/2013/05/virtual-box-1.html
- Dependancy Walker
    - 윈도우 개발 필수 유틸리티
    - 종속성이 깨진다 싶으면 여기서 부터 분석시작!
    - dependencywalker 다운로드 : http://www.dependencywalker.com/
    - vc++ 종속성의 이해 : https://msdn.microsoft.com/ko-kr/library/ms235265.aspx
- DebugView + OutputDebugString
    - 별도의 파일로 로그 남기기 간단치 않거나 귀찮은 상황
    - 파일로 로그를 남기면 타겟os에서 쉽게 실시간 로그를 확인하기 힘든데 DebugView + OutputDebugString 로 손쉽게 됨.
    - 커널로 로그를 보내는 OutputDebugString 함수 : https://msdn.microsoft.com/ko-kr/library/windows/desktop/aa363362(v=vs.85).aspx
    - debugview download : https://technet.microsoft.com/en-us/sysinternals/debugview.aspx
- dll version resource
    - 개발중 혹은 운영상황에서 dll 파일을 배포할때 버전관리 용도로 사용하면 명확하고 편리함.
    - 여러 속성중 FileDescription이 문자열항목이라 버전번호 이외에도 간단한 설명도 포함할수 있어서 더 편함.
        - ![](http://s18.postimg.org/9qbnnb3ft/screenshot_3.png)
        - ![](http://s18.postimg.org/w3jea44dl/screenshot_5.png)
    - MyWpfApp팀은 써드파티 개발자에게 배포할때 자동으로 날짜와 설명이 입력되어 빌드되게 스크립트 만들어 두었음.

```powershell
write "enter rcComment ..."
$rcComment = Read-Host "enter rcComment"
$rcContent = cat ..\MyBridge\MyBridge.rc.template
$rcContent = $rcContent -replace "<FileDescription/>", ("{0} {1}" -f (date).ToString("yyMMdd-HHmmss"), $rcComment)
$rcContent | Set-Content ..\MyBridge\MyBridge.rc
```

- 카카오톡 단톡방 파일전송
    - 타겟pc에 카톡설치해서 디버깅pc와 서로 파일, url, 로그, 캡쳐이미지 등 주고 받으면 디버깅과정을 공유하기도 좋고 이력도 남아서 좋음.
    - 계정이 한개 더 필요한것이 약간 문제...

<br/>
<br/>
<br/>