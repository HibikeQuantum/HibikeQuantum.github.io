---
layout: single
title: "Linux — 정확하고 신속하게 패스워드 바꾸고 흔적 지워버리기 (Change passwords and erase traces accurately and quickly)"
date: 2022-06-29
toc: true
category: Unix-Linux
tags: tip bash
---
매니페스토를 이용하지 않고, 손으로 하는 환경에서 사용하기 위한 스크립트. 실수를 예방할 수 있는 대신 평문으로 입력되는 만큼 히스토리를 없애기 위한 작업도 포함한다.
(Scripts for use in hand-to-hand environments without using manifestos. Instead of preventing mistakes, there are processing tasks to eliminate history as they are entered in plain text)

## command
```bash
$ echo "CurrentPW1\nNewpassWord2\nNewpassWord2" | passwd  
$ for i in {1..3} ; do history -d $(expr $(history 1| awk '{print $1}') - 3); 
```