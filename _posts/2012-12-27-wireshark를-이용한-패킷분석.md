---
layout: post
title: 121227 wireshark를 이용한 패킷분석
comments: true
tags:
- wireshark
- tcp
- udp
- ip
- network
- osi
- study
---

<!-- TOC -->

- [OSI 7 계층](#osi-7-계층)
- [클라이언트와 서버간의 데이타 캡슐화](#클라이언트와-서버간의-데이타-캡슐화)
- [wireshark 설치](#wireshark-설치)
- [wireshark 환경설정](#wireshark-환경설정)
- [실시간패킷분석](#실시간패킷분석)
- [HTTP 패킷분석](#http-패킷분석)
- [TCP 패킷분석](#tcp-패킷분석)

<!-- /TOC -->


<br>
<br>
<br>

# OSI 7 계층
| 번호 | 이름 | 역할 | 프로토콜, 네트워크장비 |
|---|---|---|---|
| 7 | 응용 | 사용자에게 네트워크 자원제공 | HTTP, SMTP, FTP, TELNET |
| 6 | 표현 | 데이타의 인코딩/디코딩, 암호화/복호화 | ASCII, MPEG, JPEG, MIDI |
| 5 | 세션 | 세션관리, 연결유지/종료/방향관리 | NetBIOS |
| 4 | 전송 | 신뢰성있는 전송, 흐름제어/분할/재조립/오류관리 | TCP, UDP <br>방화벽, 프록시서버 | 
| 3 | 네트워크 | 네트워크사이의 라우팅, 호스트 주소 관리 | IP <br> 라우터 |
| 2 | 데이터링크 | 물리장비의 주소식별 | 브리지, 스위치 <br> Ethernet, Token Ring, FDDI |
| 1 | 물리 | 물리적 매개체 |   |


# 클라이언트와 서버간의 데이타 캡슐화
- ![](https://s26.postimg.org/mi37q5emx/screenshot_76.png)

# wireshark 설치
- http://www.wireshark.org/download.html

# wireshark 환경설정
- 핵심적인정보만 표시하도록 컬럼추가/삭제
    - Edit > Preferences > User Interface > Columns
    - 아래와 같이 핵심정보만 표시되도록 컬럼추가삭제
- ![](https://s26.postimg.org/bjry7yq1l/screenshot_76.png)

- Coloring Rules 편집
    - View > Coloring Rules
    - 송/수신 패킷을 구분하기 쉽도록 아래와 같이 컬러링룰을 추가
    - ![](https://s26.postimg.org/3tl62tnq1/screenshot_76.png)

# 실시간패킷분석
- 패킷캡쳐준비/시작
    - Capture > Interfaces or 툴바의 인터페이스 캡쳐버튼을 클릭
    - ![](https://s26.postimg.org/scn7k4s49/screenshot_76.png)
    - 캡쳐할 인터페이스 선택 ( 제 경우는 노트북의 무선랜카드를 선택했습니다. )
    - ![](https://s26.postimg.org/ao02695qx/screenshot_76.png)
    - Start 버튼 클릭
- 패킷을 캡쳐하는 동안 네트워크 액션을 수행함.
    - 캡쳐중에 크롬웹브라우져로 네이버뉴스를 몇개 클릭해봄
- 패킷캡쳐종료
    - ![](https://s26.postimg.org/r0a3vzk2h/screenshot_76.png)
- 패킷캡쳐파일저장
    - File > Save as 를 이용하여 test.pcapng 으로 저장
- 컬러링필터 적용
    - 아래와 같이 {my ip address}를 컬러링룰에 적용하여 송신/수신을 구분
    - 파란색이 수신, 빨간색이 송신임.
    - ![](https://s26.postimg.org/b3bbz9ro9/screenshot_76.png)
- 디스플레이 필터 적용
    - ip.addr == {my ip address} 를 적용하여 현재 ip로 송수신된 패킷이 아닌것들을 제거함.
    - ![](https://s26.postimg.org/4qw6pfom1/screenshot_76.png)
- HTTP 요청/응답 확인
    - #66 패킷에서 뉴스기사에 대한 GET 요청확인
    - ![](https://s26.postimg.org/qel4zvp09/screenshot_76.png)
- #68 패킷에서 HTTP 200 OK 응답확인
    - ![](https://s26.postimg.org/5vq8utb2x/screenshot_76.png)


# HTTP 패킷분석
- HTTP 패킷캡쳐
    - 기존에 네이버뉴스기사를 보면서 캡쳐해 두었던 test.pcapng 파일 로드
- HTTP 패킷에 대한 디스플레이 필터를 추가
    - ip.addr == 192.168.6.226 && http
    - http 를 필터조건식에 추가하여 프로토콜이 http만 필터링 되도록함.
- ![](https://s26.postimg.org/hm46c73vd/screenshot_76.png)
- TCP 스트림 시퀀스를 통해 HTTP 요청/응답을 확인
    - 가장 처음으로 나타난 TCP 스트림 확인
        - ![](https://s26.postimg.org/5mso4vyah/screenshot_76.png)
        - ![](https://s26.postimg.org/6qcsguixl/screenshot_76.png)
    - 두번째 스트림 확인
        - tcp.steam eq 1 로 시퀀스번호가 1씩 증가된 것을 확인할 수 있음.
        - ![](https://s26.postimg.org/gb1h0uel5/screenshot_76.png)
        - 위와 같이 tcp.stream 번호를 증가시켜서 순차적인 HTTP 세션을 확인해 나갈수 있음.
    



# TCP 패킷분석
- HTTP 패킷캡쳐
    - 기존에 네이버뉴스기사를 보면서 캡쳐해 두었던 test.pcapng 파일 로드
- TCP 헤더분석
    - ![](https://s26.postimg.org/sqy6ul7x5/screenshot_76.png)
    - #66 패킷에 대한 분석
    - ![](https://s26.postimg.org/wolgjzuqh/screenshot_76.png)

| . | . |
|---|---|
| 발신지포트 | 10941 |
| 목적지포트 | 80 |
| 일련번호 | 1 |
| 확인응답번호 | 1 |
| 플래그 | PSH, ACK | 
| 윈도우크기 | 64240 bytes |
| 체크섬 | 0x4862 |

- HTTP 요청, 응답의 TCP 패킷 시퀀스 분석
    - tcp 패킷만 필터링
    - ![](https://s26.postimg.org/s3za52b15/screenshot_76.png)
- #SEQ, #ACK을 이용해서 전송제어를 계산하는 방법
    - #SEQ = 최근발신패킷의 #SEQ + data length
    - #ACK = 최근수신패킷의 #SEQ + data length
- #SEQ, #ACK 계산과정


<iframe src="https://onedrive.live.com/embed?cid=38EA1C4FEB17A7B9&resid=38EA1C4FEB17A7B9%2137191&authkey=AMqjDpdJNH8IJ0w&em=2" width="402" height="346" frameborder="0" scrolling="no"></iframe>