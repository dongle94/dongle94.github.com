---
title: "[Ubuntu] (Linux)SD카드 포맷하기"
date: 2021-06-13
last_modified_at: 2021-06-13
excerpt: "우분투(리눅스) 에서 명령어기반 SD카드 포맷 방법."
categories:
  - Ubuntu
tags:
  - Ubuntu
  - Linux
  - sdcard
  - OS
  - format
  - submemory
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

## 우분투에서 SD카드 포맷하기
우선 USB 아답터 등을 이용해서 PC가 인식할 수 있는 상태를 확인한다.  순서는 아래와 같다.  

   1. PC에 인식해서 장치 이름 확인
   2. 언마운트 작업
   3. 원하는 형식으로 포맷

### 1. PC에 연결 후 장치 이름 확인
터미널에 아래 스크립트 입력
```shell
sudo fdisk -l
```
결과 확인, `/dev/sdc<n>`, `/dev/sdb<n>` 등, PC에 마운트 되어있는 저장 장치의 상황에 따라서 `/dev/sdb` `dev/sdc` 등 다른 알파벳이 나올 수 있다.

![](/assets/images/sdcard_format/image01.png){: width="80%"}

실행해보니 내용물과는 관계없이 `/dev/sdc`인 것을 알 수 있다.

## 2. SD카드 연결 후 언마운트(Umount)
USB 아답터 등을 사용하여 PC인식을 확인했다면 1차례 언마운트 작업을 해준다. 후에 파티션 작업 시 사용중 에러를 일으키지 않기 위함이다.

```shell
sudo umonut /dev/sdc
```

----------------------------------------------------
## 3.1. 기존 SD카드 파티션 삭제
### 3.1.1. fdisk 진입(예시는 `/dev/sdc`로 진행한다.)
  ```
  sudo fdisk /dev/sdc
  ```
  - 결과  
    ![](/assets/images/sdcard_format/image02.png)  

### 3.1.2. Command 입력창이 뜨면 d(Delete 커맨드) 입력
  - `d` 입력 후에 나눠진 파티션 중에 몇번 파티션을 지울건지 입력한다.
    ![](/assets/images/sdcard_format/image03.png)
  - 14개를 다 지우기 위해 지속적으로 `d` 와 `enter`키를 눌러 다 지운다.

### 3.1.3. 여러개로 나뉘어진 SD카드 파티션 합치기
  - 3.1.에 이어서 `n` 커맨드를 이용하여 파티션을 합친다.
    ![](/assets/images/sdcard_format/image04.png)
  - `w` 커맨드 통해 해당 작업내역을 저장한다.
    ![](/assets/images/sdcard_format/image05.png)
  
## 3.2. 원하는 포맷 형식으로 포맷하기
- 예를들어 `exfat` 형식으로 포맷 한다.
```shell
sudo mkfs.exfat /dev/sdc
```  
  - `3.1.` 에서 sdc 이 아닌 다른 알파벳 혹은 파티션 번호를 주었다면 그에 맞게 입력하면 된다.  
    - exfat 에러 
      - `sudo: mkfs.exfat: command not found` 같은 에러가 발생한다면    
        ```
        sudo apt install exfat-utils
        ``` 
        위의 커맨들를 통해 모듈을 설치하여 해결할 수 있다.