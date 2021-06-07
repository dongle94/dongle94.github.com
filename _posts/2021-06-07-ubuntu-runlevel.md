---
title: "[Ubuntu] Runlevel 조정하기"
date: 2021-06-07
last_modified_at: 2021-06-07
excerpt: "우분투(리눅스) 에서 런레벨 조정 및 변경방법."
categories:
  - Ubuntu
tags:
  - Ubuntu
  - Linux
  - Runlevel
  - OS
  - X-window
  - Saved Model
  - frozen function
header:
  teaser: /assets/images/logo/ubuntu-logo.png
  overlay_image: /assets/images/logo/ubuntu-logo-wide.png
  og_image: /assets/images/profile.png
classes: wide
toc: true
toc_sticky: true
toc_label: "On this page"
toc_icon: "smile"
published: true
---

## 런레벨(runlevel)이란 
리눅스 시스템에는 **런레벨(runlevel)** 이라는 것이 있다. 일반적으로 유닉스 시스템에서 컴퓨터 OS의 작동모드이다. 일반적으로 0~6 까지의 7단계가 존재한다. 어떤 특정 OS는 `S`단계가 존재하기도 한다. OS 시작 시에 하나의 런레벨이 실행되며 하나가 지정되어 실행되고 나면 다른 모드가 실행되지는 않는다. 해당 런 레벨은 사용자가 수동으로 바꿀 수도 있다.  

또한 런레벨은 부팅 후 리눅스 머신의 상태이다. 우리가 일반적으로 부르는 싱글 유저모드(single-user mode), 멀티 유저모드(multi-user mode), 시스템 종료, 시스템 리부트 등이 있다. 이러한 세부 설정은 리눅스 배포판에 따라서 각기 다르다. 무언가 시스템 설정을 바꿔야 할 때는 싱글 유저모드에서 작업을 하기도 하고, 일반적인 GUI를 사용할 때는 디스플레이 매니저가 있는 상태인 `X-window` 환경으로 사용하기도 한다.

## 런레벨의 종류
런레벨의 기초가 되는 **표준 런레벨(Standard runlevels)** 이 있다.

|ID  | NAME |설명    |
|:---:|:---:|:-----:|
|0|Shutdown|시스템을 종료|
|1|Single-user mode|네트워크 구성과 데몬 사용 하지 않음|
|6|Reboot|시스템을 재부팅|

터미널을 열고 명령어로 `init 0` 을 입력한다면 런레벨 0, 즉 시스템은 종료를 하게 된다. 이와 마찬가지로 `init 6` 을 입력한다면 시스템은 재부팅을 하게 된다. 또한 사용자가 특정 다른 값을 커널 부트 파라미터로 지정하지 않으면 시스템은 기본 실행 레벨을 시작한다. 일반적으로 GUI환경을 사용한다면 `5` 의 값을 가진다..

### Linux Standard Base specification
사용자 층이 어느정도 두터운 우분투 OS의 경우 [LSB(Linux Standard Base)](https://en.wikipedia.org/wiki/Linux_Standard_Base)를 준수한다. LSB의 시스템이 가장 기본이 되기 때문에 따로 특별한 모드를 사용하는 런레벨 시스템이 아니라면 대부분이 아래와 같이 런레벨을 따른다.

|ID  | NAME |Target|설명    |
|:---:|:---:|:---:|:-----:|
|0|Halt|poweroff.target|시스템을 종료|
|1|Single-user mode|rescue.target|관리작업 모드. 주로 시스템 설정을 바꿀 때 사용|
|2|Multi-user mode|multi-user.target|네트워크 서비스를 사용하지 않은 CLI 모드|
|3|Multi-user mode with networking|multi-user.target|CLI 모드에서 시스템을 정상적으로 사용|
|4|Not used/user-definable|multi-user.target|일반적으로 사용되지 않으나 유저가 정의 가능|
|5|Start system normally with GUI|graphical.target|런레벨 3 + GUI 모드|
|6|Reboot|reboot.target|시스템을 재부팅|

## 런레벨 변경
현재 시스템의 런레벨은 다음과 같이 알 수 있다. 터미널을 열고 아래의 커맨드를 입력한다.
```shell
sudo systemctl get-default
```

일반적으로 사용하는 모드는 `graphical.target`, 즉 런레벨 5다. 가끔 그래픽 드라이버를 설치할 때나, 다른 그래픽 인터페이스 환경이 아닌 커맨드기반 환경에서 작업해야할 때 런레벨 3으로 변경하는 일이 있다. 이 때는 아래와 같은 방법으로 변경하면 된다.

```shell
sudo systemctl set-default multi-user.target
```
같은 방법으로 런레벨 1로 변경하고 싶다면 아래와 같이 바꿀 수 있다.
```shell
sudo systemctl set-default rescue.tartget
```

일반적으로 GPU 장치를 사용하는 딥러닝 서버들에서 그래픽 드라이버 설치 전후에 GPU 메모리가 GUI에 불필요하게 소모되는 것을 막기 위해 런레벨 3으로 운영한다.

> 참고, [Runlevel wikipedia](https://en.wikipedia.org/wiki/Runlevel)