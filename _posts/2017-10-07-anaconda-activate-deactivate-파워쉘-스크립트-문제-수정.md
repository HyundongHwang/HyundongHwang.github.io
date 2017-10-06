---
layout: post
title: 171007 anaconda activate/deactivate 파워쉘 스크립트 문제 수정
comments: true
tags:
- anconda
- conda
- python
- powershell
- bat
- ps1
- PR
---

<!-- TOC -->


<!-- /TOC -->


<br>
<br>
<br>

오랜만에 PR를 한개 올렸습니다. <br>
anconda를 설치해서 python을 사용하는 경우 activate/deactivate 기능이 윈도우쉘에서는 잘 되는데 파워쉘에서는 작동 안되는 이슈입니다. <br>
삽질을 좀 했는데, bat script 한개로 윈도우쉘, 파웨쉘 호환되게 할 수는 없었네요 <br>
그래서 아래와 같이 파워쉘을 사용할때는 activate/deactivate 명령어가 .bat 이 아닌 .ps1으로 연결되도록 새 스크립트를 추가해서 해결했습니다. <br>
부디 이 PR이 채택되서 다음 anaconda 배포판에 이 파워쉘 스크립트도 탑재되길 바래봅니다. <br>

<script src="https://htmlpartitionsync.azurewebsites.net/api/PartitionJs?url=https%3A%2F%2Fgithub.com%2Fconda%2Fconda%2Fpull%2F6090&xpath=%2F%2Fdiv%5B%40class%3D%22repository-content%22%5D"></script>