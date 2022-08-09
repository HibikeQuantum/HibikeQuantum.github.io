---
layout:	single
title:	"CloudFront 배포 후 Access-Denied 증상"
date:	2019-10-23
category: AWS
tags: [S3, Checklist]
excerpt: 
---
  
[AWS DOC - Amazon S3 객체에 대한 액세스 권한을 관리하는 방법을 설명합니다](https://docs.aws.amazon.com/ko_kr/AmazonS3/latest/user-guide/set-object-permissions.html)
  
S3 객체를 만들때 allow 해야할 항목이 워낙 많다보니 확인해볼 체크리스트를 만들었다.

1. S3 버킷 정책, get-object 항목
2. S3 액세스리스트
3. AWS 계정 레벨의 S3 권한 체크 (개별 버킷보다 우선한다)
4. 버킷에 담긴 오브젝트 권한 체크
5. Cloud-Front General → Default Root Object → Add ‘index.html’
![](/assets/img/1*9P8PGuxL9Tp2-jo6NVTryw.png)  