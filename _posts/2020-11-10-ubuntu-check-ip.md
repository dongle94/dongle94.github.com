---
title: "[Docker] Docker 주요 명령어 정리"
date: 2020-10-17
excerpt: "CLI에서 활용할 수 있는 Docker의 주요 커맨드 사용법을 알아본다."
categories:
  - Docker
tags:
  - Ubuntu
  - Docekr
  - Linux
  - use
header:
  teaser: /assets/images/logo/docker-logo.png
  overlay_image: /assets/images/logo/docker-logo-wide.png
  og_image: /assets/images/profile.png
//classes: wide
published: true
toc: true
toc_sticky: true
toc_label: "On this page"  # basic is 'On this page'
toc_icon: "smile"
---
![](/assets/images/logo/docker-logo.png){: width="50%" }
## 도커(docker) 설치 관련
도커 설치 관련하여서는 아래 포스팅을 참조한다.  
- [ubuntu에 도커 설치하기](https://dongle94.github.io/ubuntu/ubuntu-docker-install/)

## 컨테이너 내려받기
> $ docker pull [link]

우분투 16.04 버전의 도커 이미지를 다운로드한다.
```
docker pull ubuntu:16.04
```

## 컨테이너 생성  
> docker run [option] [image]:[tag]

### 포어그라운드(fore-ground) 실행
ubuntu 16.04 버전의 컨테이너를 `-i`, `-t`,`--name` 옵션을 주어 해당 이름으로 생성하고 해당 컨테이너의 **COMMAND**(/bin/bash)를 실행한다. 
```shell
$ docker run -i -t --name myubuntu ubuntu:16.04 
```
### 백그라운드(back-ground) 실행
mysql 컨테이너를 `-d`,`--name` 옵션을 주어 이름을 부여하고 detach 모드(백그라운드)로 실행하는 예제이다.
```shell
$ docker run -d --name mysql mysql:5.7
```

## 컨테이너 종료, 나오기
포어그라운드 모드에 있는 컨테이너는 해당 터미널에서 `exit`를 통해 종료시킨다. 
```
root@f1ab5a48834b:#/ exit
```
컨테이너를 종료 시키지않고 포어그라운드에서 백그라운드로 전환하고 싶을 땐 아래 명령어를 입력하면 호스트로 나올 수 있다.
```
Ctrl + P,Q
```

## 컨테이너 실행
> docker start [container]  
> docker attach [container]

`run` 했다가 종료 시킨 컨테이너를 다시 실행 시킨다. `-d` 옵션을 주어 백그라운드로 돌리는 컨테이너면 여기까지 수행.
```
$ docker start myubutu
```

`start` 한 컨테이너에 내부에 `attach`를 통해 붙는다. 
```
$ docker attach myubutu
```

## 컨테이너 목록 확인
> docker ps [option]  

### 전체 컨테이너 목록 출력
위의 명령어는 실행 중인 컨테이너 목록만 출력한다. 전체 컨테이너 목록은 다음과 같이 출력한다.
```shell
$ docker ps -a
```
출력 결과가 다음과 같이 `CONTAINER_ID IMAGE COMMAND CREATED STATUS PORTS NAMES` 나온다. 여기서 `COMMAND`에 해당하는 명령어가 컨테이너
생성과 동시에 실행된다. 

## 컨테이너 자세한 정보 출력
> docker inspect [container]

위의 예제코드에서 백그라운드로 생성한 `myubuntu` 컨테이너의 자세한 정보를 출력
```
$ docker inspect myubuntu
```
## 컨테이너 정지, 삭제
> docker stop [container]  
> docker rm [option] [container]

컨테이너를 일반적으로 삭제하기 위해 먼저 해당 컨테이너의 가동을 정지 시킨다.
```
$ docker stop mysql
$ docker rm mysql
```
가동 되고 있는 컨테이너를 한번에 정지 및 삭제를 할 수 있다. 아래 명령어는 실행 중인 `mysql` 컨테이너를 삭제한다.
```
$ docker rm -f mysql 
```
### 모든 컨테이너 삭제
```
$ docker container prune
```

## 컨테이너 포트 포워딩
> docker run [-p port:port]

생성 시에 `-p` 옵션 부여, 여러개를 노출 시키려면 `-p`를 여러개 사용하면 된다.
```
$ docker run -i -t --name myubuntu -p 80:80 -p 3306:3306 ubuntu:16.04
```

## 컨테이너 이미지 출력 
```
docker images
```

## 컨테이너 이미지 삭제
> docker rmi [image name]

```
docker rmi ubuntu:16.04
```