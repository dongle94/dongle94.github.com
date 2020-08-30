---
title: "[Ubuntu] 원격 User 생성/수정/삭제"
categories:
  - Ubuntu
tags:
  - Ubuntu
  - User
  - Remote
---

해당 글은 아래 링크를 참조하였습니다.  
참고한 블로그: [xevolts 블로그](https://xevolts.tistory.com/10)

현재의 우분투 계정명을 바꾸고 싶을 때 아래의 방법을 이용하여 작업을 할 수 있습니다.
해당 방식은 새로운 임시의 유저를 만들어 해당 계정으로 로그인 후 기존의 바꾸려는 계정에 대한 내용을 수정하고 앞서 만든 임시계정을 다시 지우는 방식으로 작업이 이루어 집니다.  
**해당 포스팅은 `ssh`등을 이용하여 원격으로 작업할 때를 전재로 작성되었습니다.**

## 새로운 임시 유저 생성
현재 `ssh`로 로그인되어 있는 계정을 바꾼다 그러면 새로운 임시유저를 만들어야 합니다. 해당 유저 계정에서 수퍼유저의 권한을 이용하여 기존의 바꾸려는 계정을 조정 할 수 있습니다. 핵심은 수정의 대상이 되는 계정에 로그인 되어있지 않은 것입니다.
- `tmpuser` 라는 이름의 임시 계정을 만들고 sudo 권한을 부여하겠습니다.  
  ```shell
  $ sudo adduser tmpuser  
  $ sudo adduser tmpuser sudo
  ```
- 아래와 같이 결과가 나옵니다. 현재 저의 우분투 계정명은 `tx2`로 설정되어 있습니다.
  ![](/assets/images/change_ubuntu_username/image00.png/)  
- 로그 아웃 후에 다시 `tmpuser` 계정으로 로그인 합니다. 

## root 계정으로 변경 후 사용자 이름 변경
- root 계정으로 들어가줍니다.  
  ```shell
  $ su root
  ```
- 새로운 사용자 이름으로 바꾸어줍니다.  
  ```shell
  $ usermod -l <new_username> <old_username>
  $ usermod -m -d /home/<new_username> <new_username>
  ``` 
![](/assets/images/change_ubuntu_username/image01.png)  
- 처음의 `su root` 시 루트 계정의 비밀번호를 입력해야합니다. 처음 실행 시에는 루트계정의 비밀번호가 생성이 안되어 있을 수 있습니다. 이 때는 아래의 명령어를 입력해서 루트 계정의 비밀번호를 입력해줍니다.  
  ```shell
  $ sudo passwd
  ```

## 소유권한 정리
해당 상태에서 `/home` 디렉토리로 가서 있는 `ls -l` 명령어를 통해 홈 디렉토리들을 자세히 살펴보면 뭔가 이상합니다. `tmpuser` 는 소유자에 개인과 그룹 모두 tmpuser로 잘 적혀있는데 기존 `tx2`계정과 새로만든 `dyseo` 계정의 소유권한이 섞여있는 것을 볼 수 있습니다.  
![](/assets/images/change_ubuntu_username/image02.png)  
- 그룹명도 변경해줍니다.  
```
$ groupmod -n <new_username> <old_username>
```
> $ groupmod -n dyseo tx2


## 계정 확인
- 수퍼유저를 `exit` 명령어로 나와서 바꾼 게정을 확인합니다. 다시 `ls -l` 명령어를 통해 할 수 있습니다.
- `vi /etc/passwd` 명령어를 통해 해당 passwd 파일을 열어보면 최하단부에 현재 있는 계정들의 정보를 확인할 수 있습니다.  
  ![](/assets/images/change_ubuntu_username/image03.png)
  현재 `tmpuser`로 로그인 되어 있고 기존 `tx2` 계정을 `dyseo` 계정으로 바꿔주었습니다.


## 임시 계정 삭제
- 임시유저 계정을 삭제하기 위해서는 다시 본래의 계정으로 로그인합니다. 여기서 본래의 계정이란 `tx2`에서 `dyseo`로 바꾸어준 계정입니다. 기존 `ssh` 로그인 방식으로 로그인 하면 됩니다.
- 수퍼유저 권한으로 `userdel` 명령어를 써서 임시계정을 삭제합니다.
  - `-rf` 옵션을 추가적으로 주어 홈 디렉토리도 같이 지워줍니다.  
    ```shell
    sudo userdel -rf <임시유저이름>
    ```
    > sudo userdel -rf tmpuser
- `/etc/passwd` 파일을 다시 `vi` 나 `gedit`으로 열어 확인해보면 
우리가 임시로 만들었던 `tmpuser` 가 없어진 것을 확인할 수 있습니다.

