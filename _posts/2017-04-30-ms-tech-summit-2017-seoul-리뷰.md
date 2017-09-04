---
layout: post
title: 170430 ms tech summit 2017 seoul 리뷰
comments: true
tags:
- study
- ms
- xamarin
- bot
- azure
- tech summit
- review
- 후기
- office 365
- luis
- remote app
- qna maker
- cloud pbx
---


<!-- TOC -->

- [ms tech summit 2017 seoul 페이지](#ms-tech-summit-2017-seoul-페이지)
- [office 365](#office-365)
- [Microsoft Bot Framework](#microsoft-bot-framework)
- [QnA Maker](#qna-maker)
- [Microsoft Congnitive Services](#microsoft-congnitive-services)
- [Desktop Bridge](#desktop-bridge)
- [Azure Cloud PBX](#azure-cloud-pbx)
- [Visual Studio 2017 New Features](#visual-studio-2017-new-features)
- [Azure Serverless Service - Functions !!!](#azure-serverless-service---functions-)
- [xamarin](#xamarin)
- [Azure RemoteApp](#azure-remoteapp)
- [컨퍼런스 사진모음](#컨퍼런스-사진모음)

<!-- /TOC -->

<br>
<br>
<br>

![](http://s23.postimg.org/uk3wwx217/screenshot_2017_05_02_at_11_00_29.png)

<br>
<br>
<br>


[toc]

<br>
<br>
<br>

## ms tech summit 2017 seoul 페이지
- https://www.microsoft.com/ko-kr/techsummit/seoul.aspx

<br>
<br>
<br>

## office 365
![](http://s23.postimg.org/ldlm9mwsr/screenshot_2017_05_02_at_11_03_00.png)
- 조직에 필요한 office 부터 메일, 일정, 문서관리, 협업, 온라인 회의서비스를 원스톱으로 제공하는 클라우드 서비스
- 세부기능
    - Office
        - 설치형 or 웹형
        - 월정액과금
    - Exchange Online
        - 모바일과 완벽하게 연동됨
        - 50GB 메일함 제공
        - 스펨기능 강화됨
    - Lync Online
        - 메신저, 음성/화상통화, 화면공유, 파워포인트 공유
        - 모바일과 완벽하게 연동됨
    - Sharepoint Online
        - 권한관리 기능이 있는 웹하드, 웹게시판
- 레퍼런스
    - http://seminar.eventservice.co.kr/Microsoft/office365/new_Office365_introduction.pdf
        - office 365 소개 문서
    - https://products.office.com/ko-kr/business/compare-more-office-365-for-business-plans
        - office 365 기업용 요금제 비교 사이트
- 의견
    - 기업에서 기존에 오피스 오프라인 라이센스를 구매해서 사용하던 기업인 경우는 office 365 를 월정액으로 사용하는것도 괜찮은 선택
    - 오프라인 오피스 어플리케이션 뿐만 아니라, 사내메신저, 파일공유, 게시판, 주소록, 메일, 화상회의, 사내전화망 등 도 함께 제공되는 풀 패키지.

<br>
<br>
<br>

## Microsoft Bot Framework
![](http://s23.postimg.org/jztzebxjf/screenshot_2017_05_02_at_11_19_11.png)
- 메인 페이지
    - https://dev.botframework.com/
- 소개
    - 챗봇을 구현하기 위한 ms의 봇 프레임워크
    - SDK 지원 프로그래밍 언어
        - .NET
        - Node.js
    - 자연어 처리를 위해 LUIS 통합제공
        - 자연어 처리를 위한 문장해석, 단어분석 들이 제공됨.
        - 최근에 한글도 지원되는데 자연어 처리 서비스중에 한글지원되는 것 거의 유일.
    - Cortana, Bing과 통합제공
    - 다양한 채널에 호스팅 가능
        - 기본 지원 채널 : Skype, Web Chat, Email, Facebook, Kik, Slqck, Telegram, SMS ...
        - 카톡과 라인은 기본 지원 안됨.
        - 기본 지원 안될때는 Direct Line API를 이용해서 브릿지 개발.
            - https://docs.botframework.com/en-us/restapi/directline3/#navtitle
    - SW 구조적 특징
        - Session
            - 각 채팅의 세션정보를 쉽게 유지해서 개발 가능.
        - Dialogs
            - 대화 자체를 모듈화 할 수 있어서 마이크로 챗봇들을 개발해서 모듈화 관리 개발 가능.
- 레퍼런스
    - https://github.com/KoreaEva/Bot
        - 발표자료 샘플코드 모음
        - 동영상강좌, 한글강좌 모음
- 의견
    - 챗봇을 만들일이 있다면 강력한 자연어 분석기능과 심지어 한글도 지원되는 LUIS를 사용할 수 있는 Microsoft Bot Framework를 이용해 구성하는 것이 좋아보임.
    - 함께 제공되는 로컬 채팅 에뮬레이터도 상당히 완성도 높아서 개발편의성이 좋을듯.

<br>
<br>
<br>

## QnA Maker
![](http://s23.postimg.org/yd17hvlor/screenshot_2017_05_02_at_13_17_55.png)
- 메인페이지
    - https://qnamaker.ai
- 소개
    - 각 기업의 QnA 페이지의 url을 입력으로 주면 그 내용을 바탕으로 즉시 기업용 QnA 봇을 생성해줌
    - QnA 봇을 IT 전문가 없이도 즉시 만들어서 서비스 할 수 있음.
    - 현장 데모로 한국 쉐보레 자동자 QnA 페이지를 이용해서 자동차 영업사원 봇을 자동생성했는데 상당히 인상적이었음.
- 의견
    - IT전문인력이 없는 기업이 QnA 봇을 생성할 일이 있으면 간단하게 만들어서 서비스 할수 있음.
    - 프랜차이즈 F&B 기업의 봇 정도라면 괜찮은 기능이 될수 있음.
    - 하지만 금융, 보험 등 QnA라도 좀더 전문성이 필요한 경우는 Microsoft Bot Framework 를 활용해서 자체 개발하는 것이 나을듯.

<br>
<br>
<br>

## Microsoft Congnitive Services
![](http://s23.postimg.org/5wghowjuz/screenshot_2017_05_02_at_14_18_27.png)
- 메인페이지
    - https://www.microsoft.com/cognitive-services
- 소개
    - 각종 인공지능 인식 서비스들을 통합 제공
        - 얼굴인식, 표정인식, 보이스인식, 번역, 필기인식, 이미지검색, 연관뉴스검색 ...
    - 모든 서비스들을 REST api 로 제공하여 사용하기 간단함.
- 의견
    - 서비스 구현상 각 인식서비스가 필요한 경우 Congnitive Services 에서 제공되는지 검토해보고 프로토타입 구현해 볼 만함.
    - 꼭 Azure를 사용하지 않더라도 REST api로 연결되기 때문에 통합의 문제는 적음.

<br>
<br>
<br>

## Desktop Bridge
![](http://s23.postimg.org/icd7ind6z/screenshot_2017_05_02_at_14_25_46.png)
- 메인페이지
    - https://developer.microsoft.com/ko-kr/windows/bridges/desktop
- 소개
    - Win32 어플리케이션 EXE, DLL, COM 들을 UWP 패키징 가능하도록 변경하는 기술
- 왜 Win32 Desktop 앱을 UWP 앱으로 변경해야 하는가?
    - 인증된 스토어를 통한 안전한 배포
    - UWP 샌드박싱 정책을 통한 격리환경에서 설치/사용/삭제
    - 스토어앱의 유료화 API 를 사용하여 수익창출 가능
- 의견
    - 일반적으로 Win32 어플리케이션이 쉽게 UWP로 변경되지 않기 때문에 일반적인 실효성에는 의문임.
    - 하지만 결제모듈등 중요한 레거시 시스템이 존재하고 새로만든 UWP앱에 꼭 연동을 해야 하는 경우에 이 Desktop Bridge와 함께 제공되는 변환툴이 격리환경에서 파일시스템, 레지스트리등 글로벌 리소스 변경을 복제해주는 기능이 있는데 이를 잘 이용하면 리거시 모듈을 안정적으로 연동하는데 장점이 있을듯.

<br>
<br>
<br>

## Azure Cloud PBX
![](http://s23.postimg.org/ihlk629vv/screenshot_2017_05_02_at_14_40_27.png)
- 메인페이지
    - https://products.office.com/ko-kr/skype-for-business/cloud-pbx
- 소개
    - Azure Cloud 기반의 사설 전화교환기(PBX) 서비스
    - Office 365 Enterprise E5 구독모델을 사용하는 경우 함께 사용가능
    - 기업용 내선전화, 해외지사 내선서비스, 발신자 표시, 착신전환 등 기본적인 전화기능 제공
    - 메일, 주소록 연동, 화상통화, 통화기록, 모바일지원 등 클라우드 부가기능들도 제공
- 의견
    - 기업에서 office 365를 구독하면서 사내 전화까지 필요한 경우 패키지로 고려하는것도 고려할 만 함.

<br>
<br>
<br>

## Visual Studio 2017 New Features
![](http://s23.postimg.org/c56ew86tn/screenshot_2017_05_02_at_14_48_26.png)
- 메인페이지
    - https://docs.microsoft.com/ko-kr/visualstudio/ide/whats-new-in-visual-studio
- 소개
    - 설치/삭제 경량화
        - 설치시 종속성 다운로드 방식이라 설치프로그램이 5Mb 보다 작음.
        - 전체 설치 기준으로 40분 정도로 경량화
        - 설치하면서 레지스트리등 글로벌 리소스를 변경하지 않고, 격리환경에서 설치/삭제 됨.
    - 경량 솔루션 로드
        - 솔루션이 전부 로딩되기전 파일편집 가능
        - 비주얼스튜디오 실행하자마자 파일편집이 가능함.
    - 노란전구 기능강화
        - 기존의 문법에러 교정기능 강화됨.
        - 개발팀의 코딩컨벤션 도 교정해주는 기능 생김.
    - 코드맵 기능
        - 콜스텍을 그래프 형태로 비주얼라이즈 해줌
        - 각종 보고서에 모듈 구성도를 그려야 하는 상황에서 요긴하게 활용가능함.
    - 리펙토링 기능강화
        - 전체 프로젝트에서 중복코드 부분을 모두 찾고 통계분석과 수정 가이드를 제공함.
    - 테스트 기능 강화
        - intelli test
            - 테스트 대상의 함수를 지정하면 생성 가능한 다양한 입력값을 자동생성해서 테스트 템플릿 코드를 생성해줌.
        - coded UI test
            - 키보드, 마우스 입력을 모두 녹화하여 테스트 코드로 자동생성해 주어 UI테스트를 쉽게 만들수 있게 함.
	- 폴더 열기 기능
		- 이제부터 일반 파일편집기와 마찬가지로 비주얼스튜디오에서도 폴더를 서브폴더 까지 잡아서 한번에 열기가 됨.
		- 가장 바라던 기능 !!!
		- 비주얼 스튜디오만 있다면 굳이 텍스트파일 편집기는 필요 없을듯 ...
		- ![](http://s18.postimg.org/3vww0nrah/screenshot_2017_05_04_at_15_11_54.png)
- 의견
    - 실제적인 불편사항들이 많이 개선되어서 Visual Stuio 2017은 더욱 개발 생산성이 올라갈것.
    - 특히 코드맵의 그래프표현기능, coded UI test 는 실제 프로젝트에 적용하여 업무개선을 크게 할 수 있을것.

<br>
<br>
<br>

## Azure Serverless Service - Functions !!!
![](http://s23.postimg.org/65inskm17/screenshot_2017_05_02_at_15_02_12.png)
- 메인페이지
    - https://azure.microsoft.com/ko-kr/services/functions/
- 소개
    - AWS lambda 대항마
    - 요즘 가장 핫한 서버사이드 기술 주제
    - 서버를 사용하지 않고 이벤트를 수신하면 연결된 함수가 실행되는 서비스
        - 이벤트가 HTTP request를 수신하여 처리하게 만들면 왠만한 웹서버는 Functions로 대치할 수 있음.
    - 연결된 함수가 실행하는 순간만 과금이 되며, 불필요하게 서버의 유휴시간이 전혀 생기지 않음.
    - Functions가 실행되는 호스트 머신은 기본적으로 트래픽에 유연하게 대응이 되기 때문에 서버스케일 문제에서 완전히 해방된다.
- 의견
    - AWS lambda, Azure Functions는 작은부분의 프로토타입이라도 만들어서 익숙해질 필요가 있는 주제임.
    - 아직 초기 기술이라 IDE와 로컬 테스트 환경이 완벽하지 않은듯함.
    - Functions가 실행되는 종속성을 호스트 머신에서 허가받는 과정이 일반 웹서버에서와 차이가 있는데 서비스에 운영하기 위해선 이 부분의 경험이 추가로 필요할 것.

<br>
<br>
<br>

## xamarin
![](http://s23.postimg.org/9chspxvuz/screenshot_2017_05_02_at_15_18_00.png)
- 메인페이지
    - https://www.xamarin.com/
- 소개
    - xamarin은 .NET을 이용하여 Android, iOS, UWP를 동시에 지원하기 위한 크로스 플랫폼 도구
    - Cordova, Facebook React 등과 같은 웹킷 기반 웹앱과 달리 빌드결과물이 native app 이기 때문에 성능, 사용성 면에서 경쟁솔루션들과 차별됨.
    - xamarin이 native app으로 빌드되기 때문에 다른 솔루션들과 달리 DI패턴으로 개별 플랫폼 구현을 해야 하는 부분들이 많았는데 이는 2015년 MS가 xamarin을 인수한 이래로 상당부분 개선됨.
    - 특히 xamarin.form 에 역점을 두어 file, network, sqlite, 로직들 뿐만아니라 간단한 공통 UI들도 공통으로 사용할 수 있게 되면서 95% 이상의 코드를 플랫폼 공통으로 사용 할 수 있게 됨.
    - 이번에 xamarin.form 전용을 UI design previewer가 vs2017에 연동되면서 UI의 개발생산성도 크게 증가됨.
- 의견
    - MS가 xamarin을 인수하고 많은 투자를 하고 시간도 꽤 지나서 이제는 쓸만해짐.
    - 플랫폼 종속성이 아주큰 앱이 아니라면 xamarin으로 개발하는 것을 고려해볼만한 시점.
- 샘플코드
    - https://github.com/xamarin/xamarin-forms-samples
        - xamarin.forms의 공식 샘플코드 모음
        - xamarin 중에서도 UI코드까지 공통사용하므로 ms가 요즘 힘주고 있는 솔루션.

<br>
<br>
<br>


## Azure RemoteApp
![](http://media.bestofmicro.com/5/E/435650/original/azure-remoteapp-hybrid-deployment.png)

- 메인페이지
    - https://docs.microsoft.com/ko-kr/azure/remoteapp/remoteapp-whatis
- 데모 동영상
    - https://youtu.be/D832RN1GzUY
    - https://azure.microsoft.com/en-us/resources/videos/azure-remoteapp-overview/
- 소개
    - VDI 서비스의 MS 자체 솔루션
    - MS Azure가 제공하는 가상 데스크톱 서비스로 별도의 VM을 구성하고 이 VM의 동작을 PC, 스마트폰, 태블릿 등의 장비로 원격데스크톱 서비스를 제공함.
- 가격
    - https://azure.microsoft.com/ko-kr/pricing/details/remoteapp/
    - https://www.youtube.com/watch?v=NLvs0OK_Q-Q&feature=youtu.be
- 장점
    - RDP(Remote Desktop Protocol)을 최대한 사용하여 네트워크나 영상처리자원의 낭비가 없음.
        - 기존의 화면공유 원격데스크톱과 달리 화면이미지부분과 마우스 이동등 메타데이타로 구분할 수 있는 부분을 효율적으로 분리하여 더 적은 하드웨어 자원으로도 고품질 서비스를 할 수 있음.
        - 모든 가상머신 OS에 범용적이라기 보단 윈도우 OS를 위해 최적화 된 모델임.
    - MS Azure 에서 제공하는 가상머신은 윈도우 라이센스가 이미 포함된 가격임.
    - 가격정책에서 상담을 통한 할인의 여지가 많음.
- 단점
    - 할인전 가격이 월 20,400으로 일반 가상머신 서비스에 비해 20%가량 비싼 수준임
- 의견

<br/>
<br/>
<br/>

## 컨퍼런스 사진모음
- https://1drv.ms/f/s!ArmnF-tPHOo4gp10ER6DoOYkXHWmXQ
