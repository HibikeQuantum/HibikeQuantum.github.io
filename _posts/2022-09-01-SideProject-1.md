---
layout: single
category: devlog
tags: SideProject
toc: true
date: 2022-09-01T09:35:20+09
last_modified_at: 2022-09-01T09:35:22+09
title: 베어노트를 더 스마트하게 쓰자 BearAutoPublisher (v1.0.0) 릴리즈
---

## 안내
BearAutoPublisher(이하 BAP)은 사이드 프로젝트로 만든 배치성 프로그램입니다. 본 포스트는 BAP의 기능을 소개하기 위해 쓰여졌고 프로그램 설치, 구체적인 정보조회, 문의는 Github [GitHub - HibikeQuantum/Bear-Auto-Publisher: Markdown export from Bear sqlite database and publishing at github repository](https://github.com/HibikeQuantum/Bear-Auto-Publisher) 이 링크에서 하실 수 있습니다.

## 왜 만들고 왜 써야했나
베어노트는 맥에서 저렴가격으로 쓸 수 있는 노트앱 중 유려한 UI와 합리적 인터페이스로 인정받는 프로그램입니다. 노트앱이란 기능에 충실한 대신  
유저의 사용경험을 피드백하고 다른 파일의 포맷으로의 변환(PRO에서 수동으로 할 수는 있습니다)이나 히스토리 추적, 자동화된 관리기능은 기대하기 어려웠습니다.  
BAP는 이런 니즈를 채우기 위한 배치성 프로그램입니다. Git으로 메모를 관리하는 분들은 유용하게 쓰실 수 있을거라 생각합니다.

## 기능
1. 베어앱의 노트를 markdown으로 변환하여 추출
2. git으로 관리되는 디렉토리로 추출된 파일을 이전하고 commit과 push를 수행합니다.
3. git의 변경 내용을 언어분석 패키지로 분석하여 변화된 내용을 그래프로 시각화합니다.
4. GitHub에서 제공하는 diff 페이지를 열고, 사용자가 Github에 올리지 않겠다고 선언한 문서들은 오프라인의 텍스트 에디터로 diff를 보여줍니다.
5. 이 모든 과정은 필요에 따라 on/off 할 수 있게 모듈화 해뒀습니다.

## 실제 사용 모습
![!!!](/assets/img/SCR-20220901-dro.png)
프로그램을 실행하여 추출을 실행하고 그 파일들을 Github에 업로드 할지 물어보는 단계
{: .caption}
  
![!!!](/assets/img/SCR-20220901-er4.png)
GitHub에 Push하고 프로세스 완료
{: .caption}
  
![!!!](/assets/img/SCR-20220901-etc.png)
변경된 내용을 웹에서 확인
{: .caption}
  
![!!!](/assets/img/SCR-20220901-j5q.png)
변경된 텍스트의 양을 보여주는 그래프, 마우스 오버시 상세 데이터와 자주 쓰인 키워드가 보입니다
{: .caption}
  
## 저의 사용방법
1) 저는 이렇게 만든 프로그램을 매일 일기장을 쓰기전에 한번 실행하고 있습니다. 메모에 뭐 썼는지 다 나오니까 무슨 일이 있었는지 한눈에 파악이 되죠. 
2) alias를 이용하면 더 간편하게 쓸 수 있습니다.
```
# ~/.zshrc
@ABP="sh /Users/kth/Code/Bear-Auto-Publisher/AutoPublish.sh"
```
3) 저는 Public Repository에서 메모장을 관리하고 있으나 github에 Private repository도 만들 수 있으니 관리하셔도 됩니다. 아니면 그냥 원격저장소 안쓰고 로컬에서만 쓰셔도 되구요. (BAP 의 config에서 앱의 동작들을 끄고 켤 수 있게 해뒀습니다.)