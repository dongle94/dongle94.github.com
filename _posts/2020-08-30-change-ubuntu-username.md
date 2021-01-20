---
title: "[Ubuntu] 원격 User 생성/수정/삭제"
date: 2020-08-30
excerpt: "Ubuntu에서 새로운 계정을 생성하고 수정 및 삭제 방법에 대해 알아본다."
categories:
  - Ubuntu
tags:
  - Ubuntu
  - Ubuntu 18.04
  - account
  - User
  - Remote
header:
  teaser: /assets/images/logo/ubuntu-logo.png
  overlay_image: /assets/images/logo/ubuntu-logo-wide.png
  og_image: /assets/images/profile.png
published: true
toc: true
toc_sticky: true
toc_label: "On this page"  # basic is 'On this page'
toc_icon: "smile"
---

원격으로 우분투 서버등의 작업을 할 시에 새로운 유저를 만들거나 기존 유저관련 내용을 수정할 필요가 있을 수 있다. 현재의 우분투 계정명을 바꾸고 싶을 때 아래의 방법을 이용하여 작업을 할 수 있다. 아래 방식은 **새로운 임시의 유저를 만들어 해당 계정으로 로그인 후, 기존의 바꾸려는 계정에 대한 내용을 수정하고 앞서 만든 임시계정을 다시 지우는 방식**으로 작업이 이루어진다. 이 과정 속에서 새로운 **유저 생성, 유저정보 수정, 유저계정 삭제**가 이루어지게 된다.

## 새로운 유저 생성
현재 `ssh`로 로그인되어 있는 계정정보를 수정하기에 앞서 새로운 유저를 만들어야 한다. 새로 만들 유저 계정에서 수퍼유저의 권한을 이용하여 기존의 바꾸려는 계정을 조정 할 수 있다. 

나는 `tmpuser` 라는 이름의 임시 계정을 만들고 sudo 권한을 부여하겠다.  
  ```shell
  $ sudo adduser tmpuser  
  $ sudo adduser tmpuser sudo
  ```
`tmpuser`의 계정 정보를 입력하는 칸에서는 `enter`를 눌러 기본값으로 설정해준다. `Y`를 입력하여 계정을 만들어주고 수퍼유저 권한 설정까지 마쳐준다. 결과는 아래 이미지와 같다.  
![](/assets/images/change_ubuntu_username/image00.png){: width="80%"}  
로그 아웃 후에 다시 `tmpuser` 계정으로 로그인한다. 

## root 계정으로 변경 후 사용자 이름 변경
root 계정으로 들어간다.  
```shell
$ su root
```
새로운 사용자 이름으로 바꾼다. 새로 바꿔줄 <new_username> 과 <old_username> 위치에 유의한다.
```shell
$ usermod -l <new_username> <old_username>
$ usermod -m -d /home/<new_username> <new_username>
```  
> $ usermod -l dyseo tx2  
> $ usermod -m -d /home/tx2 dyseo

### root 계정 비밀번호
처음의 `su root` 시 루트 계정의 비밀번호를 입력해야하는데 처음 실행 시에는 루트계정의 비밀번호가 생성이 안되어 있을 수 있다. 이 때는 아래의 명령어를 입력해서 루트 계정의 비밀번호를 생성해 준다.  
```shell
$ sudo passwd
```

## 소유권한 설정
해당 상태에서 `/home` 디렉토리로 가서 있는 `ls -l` 명령어를 통해 홈 디렉토리들을 자세히 살펴보면 뭔가 이상함을 알 수 있다. `tmpuser` 는 소유자에 개인과 그룹 모두 tmpuser로 잘 적혀있는데 기존 `tx2`계정과 새로만든 `dyseo` 계정의 소유권한이 섞여있는 것을 볼 수 있다.  
![](/assets/images/change_ubuntu_username/image02.png)  
그렇기 때문에 이어서 그룹명도 변경한다.  
```
$ groupmod -n <new_username> <old_username>
```
> $ groupmod -n dyseo tx2


## 계정 확인
수퍼유저를 `exit` 명령어로 나와서 바꾼 게정을 확인한다. 다시 `ls -l` 명령어를 통해 할 수 있다. 또한 `vi /etc/passwd` 명령어를 통해 해당 passwd 파일을 열어보면 최하단부에 현재 있는 계정들의 정보를 확인할 수 있다.  
```
vi /etc/passwd
````
![](/assets/images/change_ubuntu_username/image03.png)
현재까지 한 내용을 정리하면 `tmpuser`로 로그인 되어 있고 기존 `tx2` 계정을 `dyseo` 계정으로 바꿔주었다.


## 계정 삭제
임시유저 계정을 삭제하기 위해서는 다시 본래의 계정으로 로그인해야 한다. 여기서 본래의 계정이란 `tx2`에서 `dyseo`로 바꾸어준 계정이다. 기존과 동일하게 `ssh` 로그인 방식으로 로그인 하면 된다.  
수퍼유저 권한으로 `userdel` 명령어를 써서 임시계정을 삭제한다. 또한 `-rf` 옵션을 추가적으로 주어 홈 디렉토리도 같이 지워준다.  
```shell
sudo userdel -rf <임시유저이름>
```
> sudo userdel -rf tmpuser

`/etc/passwd` 파일을 다시 `vi` 나 `gedit`으로 열어 확인해보면 우리가 임시로 만들었던 `tmpuser` 가 없어진 것을 확인할 수 있다.

---
>해당 글은 아래 링크를 참조하였다.  
>[https://xevolts.tistory.com/10](https://xevolts.tistory.com/10)
