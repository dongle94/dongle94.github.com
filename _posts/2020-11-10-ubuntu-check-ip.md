---
title: "[Ubuntu] 우분투 IP 확인방법"
date: 2020-10-17
excerpt: "Ubuntu에서 ip정보를 확인할 수 있는 방법들에 대해 알아본다."
categories:
  - Ubuntu
tags:
  - Ubuntu
  - Linux
  - Debian
  - use
  - IP
  - check
header:
  teaser: /assets/images/logo/ubuntu-logo.png
  overlay_image: /assets/images/logo/ubuntu-logo-wide.png
  og_image: /assets/images/profile.png
classes: wide
published: true
toc: true
toc_sticky: true
toc_label: "On this page"  # basic is 'On this page'
toc_icon: "smile"
---
우분투 18.04 에서 일반적으로 자신이 사용하고 있는 머신의 IP정보가 필요할 때가 있다. 다음과 같이 3가지 방법을 제시한다.

## ifconfig
가장 일반적으로 쓰이는 방식이다. 터미널에서 다음과 같이 입력한다.
```
ifconfig
```
만약 ifconfig가 설치 되어있지 않으면 **'ifconfig: Command not found.'** 같은 에러 메시지가 뜰 것이다. 그때는 아래와 같은 커맨드를 통해 `net-tools`를 설치해주도록 하자
```
sudo apt install net-tools
```
추가적으로 네트워크 인터페이스 이름을 알고 있다면 (ex. eth0, eno0 등) 네트워크 인터페이스 이름을 통한 정보 조회를 할 수 있다.
```
ifconfig <inferface>
```
![](/assets/images/ubuntu-ip-check/ifconfig.png){: width="70%" }

## ip addr
다음은 터미널에서 `ip addr`을 입력하여 ip 주소를 확인할 수 있다.
```
ip addr
```
먼저의 **ifconfig**와 마찬가지로 네트워크 인터페이스와 ip주소의 바인딩 상태를 알 수 있다.
![](/assets/images/ubuntu-ip-check/ipaddr.png){: width="70%"}


## hostname -I
다음 방법으로 터미널에서 `hostname` 키워드를 사용하는 방법이 있다. 옵션은 `-I`를 주면된다. 이 방법은 위의 두 방법과는 다르게 현 host 기기가 사용하고 있는 ip 주소만 나온다. `-i` 와 `-I`의 옵션이 다름에 주의하자.
```
hostname -I
```
![](/assets/images/ubuntu-ip-check/hostname.png){: width="30%"}



> 해당 게시글은 다음 블로그 링크를 참조하여 작성하였다.  
>[https://vitux.com/3-ways-to-find-your-local-ip-address-in-debian-10/](https://vitux.com/3-ways-to-find-your-local-ip-address-in-debian-10/)
