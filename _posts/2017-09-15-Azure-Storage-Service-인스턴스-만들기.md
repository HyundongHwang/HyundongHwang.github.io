---
layout: post
title: 170915 Azure Storage Service 인스턴스 만들기
comments: true
tags:
- azure
- storage
- table
- blob
- tutorail
- study
- blackbox
---

<!-- TOC -->

- [Azure 계정 만들기](#azure-계정-만들기)
- [Azure Storage Service 인스턴스 생성](#azure-storage-service-인스턴스-생성)
- [Azure Storage Service Aceess Key 조회](#azure-storage-service-aceess-key-조회)
- [Azure Storage Service 요금조회](#azure-storage-service-요금조회)

<!-- /TOC -->

<br>
<br>
<br>

# Azure 계정 만들기
- https://portal.azure.com 에서 가입, 로그인, 신용카드 연결 순서로 진행...

<br>
<br>
<br>

# Azure Storage Service 인스턴스 생성
- 새로만들기 > Storage Account 선택
    - ![](https://s26.postimg.org/o65qdga0p/screenshot_78.png)
- 속성입력
    - ![](https://s26.postimg.org/m2vb5sa7t/screenshot_79.png)
    - 이름
        - 기존의 이름이 겹치지 않게 정해야 함.
    - 배포모델
        - `Resource Manager` 가 관리가 편리함.
    - 계정종류
        - `일반` 을 선택해야 Table, Blob, Queue 을 모두 사용할 수 있음.
    - Replication
        - `LRS` 가 쓰기가 많고 조회가 적은경우 일반적임.
            - 단일 데이터 센터 내에 여러 개의 동기 데이터 복사본을 만듭니다.
    - 리소스그룹
        - 다른 인스턴스와 묶지 않으려면 새로 만드는것이 좋음.
    - 지역
        - `한국중부` 가 좋지 않을까?    

<br>
<br>
<br>

# Azure Storage Service Aceess Key 조회
- 모든리소스 > hhdstoragetest > Access Keys > Connection String
- ![](https://s26.postimg.org/a4tqrw6gp/screenshot_80.png)

<br>
<br>
<br>

# Azure Storage Service 요금조회
- https://azure.microsoft.com/ko-kr/pricing/details/storage/blobs/

<br>
<br>
<br>
