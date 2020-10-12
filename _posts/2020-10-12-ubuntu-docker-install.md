---
title: "[Ubuntu] Ubuntu에 Docker 설치하기"
date: 2020-10-12
excerpt: "Docker-ce install in Ubuntu"
categories:
  - Ubuntu
tags:
  - Ubuntu
  - Docekr
  - Linux
header:
  <!--teaser: /assets/images/page-header-teaser.png-->
  <!--overlay_image: /assets/images/unsplash-image-1.jpg-->
  og_image: /assets/images/profile.png
classes: wide
published: true
---

## 설치 전 확인 사항
- 커널 버전 확인: 리눅스 커널이 최소 3.10 버전 이상이어야한다. 아래와 같이 확인 가능하다.
```shell
$ uname -r
>> 3.10.0-327.13.1.e17.x86_64
``` 

- `sudo` 권한 혹은 `root` 권한을 소유한 계정에서 설치 진행
- 최신 배포판 OS인지 확인.
  - 2020년 10월 기준 우분투 16.04, 18.04, 20.04 지원
- 64비트 운영체제 확인. 32비트에서는 돌아가지 않는다. 

## 설치
```shell
$ sudo apt-get install -y ca-certificates curl software-properties-common
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
$ sudo add-apt-repository \
  "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
$ sudo apt update
$ sudo apt docker-ce
```
- docker는 CE(Community Edition)과 EE(Enterprise Edition)으로 나뉜다. EE는 유료로 제공되는 기업용 솔루션이다.
  CE는 기술지원 및 서비스 단계 시 각종 지원이 없다. 개인 공부 및 개발 환경 사용이라면 CE를 설치하여 무료버전을 사용한다. 
  
## 설치 후 확인
```shell
$ docker info
>>
Client:
 Debug Mode: false

Server:
 Containers: 0
  Running: 0
  Paused: 0
  Stopped: 0
 Images: 1
 Server Version: 19.03.13
...
...
```