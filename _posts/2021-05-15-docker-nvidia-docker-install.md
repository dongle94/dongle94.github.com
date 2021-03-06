---
title: "[Docker] Nvidia-docker install "
date: 2021-05-14
excerpt: "도커 컨테이너에서 GPU 자원을 사용하기 위한 Nvidia-docker 설치법을 알아본다."
categories:
  - Docker
tags:
  - Nvidia
  - Docekr
  - Linux
  - Install
  - GPU
header:
  teaser: /assets/images/logo/docker-logo.png
  overlay_image: /assets/images/logo/docker-logo-wide.png
  og_image: /assets/images/profile.png
classes: wide
published: true 
toc: true
toc_sticky: true
toc_label: "On this page"
toc_icon: "smile"
---
![](/assets/images/logo/docker-logo.png){: width="35%" } ![](/assets/images/logo/nvidia-logo.png){: width="55%" }

Docker 를 설치하고 나면 기본적으로 컨테이너에서 원하는 작업을 수행 할 수 있게 된다. 그러나 격리된 컨테이너에서 GPU를 사용하는 AI/ML/DL 관련 작업을 하려고 하면 `nvidia-smi` 를 터미널에서 입력해도 GPU 자원을 사용할 수 없기 때문에 작업을 진행 할 수 없다. 이를 해결 해 줄 수 있는 것이 `Nvidia-docker`다. 설치를 후 조금의 옵션을 추가하면 자신의 컨테이너에서 GPU를 사용할 수 있다.

## 설치 환경
- Docker Version: 20.10.5
- OS/Arch: linux - Ubuntu 18.04 / amd64
- GPU: Nvidia Geforce RTX 3090
- Nvidia-driver: 460.73.01
- CUDA: 11.1

## 요구 환경
- Linux x86_64 커널 버전 > 3.10
- Docker >= 19.03
- NVIDIA GPU with Architecture >= Kepler (or compute capability 3.0)
  - Kepler GPU 아키텍쳐는 GTX 600 시리즈 이상이면 된다.
- NVIDIA Linux drivers >= 418.81.07

## 도커 설치
도커 프로그램에 대한 설치는 이전에 포스팅했던 [게시글](https://dongle94.github.io/docker/docker-ubuntu-install/)를 참고한다.

## 설치
> 저장소 및 GPG 키 설정

```shell
$ distribution=$(. /etc/os-release;echo $ID$VERSION_ID) \
   && curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add - \
   && curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
```

> Nvidia-docker install

```
$ sudo apt-get update
$ sudo apt-get install -y nvidia-docker2
```

> docker 서비스 재시작

```
sudo systemctl restart docker
```

여기까지 실행하였다면 어떤 도커 이미지라도 컨테이너 실행하여서 제대로 설치가 되었는지 테스트를 해볼 수 있다.
나는 테스트로 `Ubuntu 18.04` 이미지를 컨테이너로 실행하여 테스트 하였다. GPU 사용을 위해 추가한 부분은 `--gpus all` 이다 
```
sudo docker run --rm --gpus all ubuntu:18.04 nvidia-smi
```

결과는 다음과 같이 확인할 수 있다.
```
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 460.73.01    Driver Version: 460.73.01    CUDA Version: 11.2     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|                               |                      |               MIG M. |
|===============================+======================+======================|
|   0  GeForce RTX 3090    Off  | 00000000:01:00.0  On |                  N/A |
| 48%   44C    P8    36W / 370W |    671MiB / 24267MiB |      4%      Default |
|                               |                      |                  N/A |
+-------------------------------+----------------------+----------------------+
                                                                               
+-----------------------------------------------------------------------------+
| Processes:                                                                  |
|  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
|        ID   ID                                                   Usage      |
|=============================================================================|
+-----------------------------------------------------------------------------+
```
  
## GPU 자원을 사용하며 도커 실행
Docker 를 실행할 때 GPU 자원을 사용하는 방식은 대략 2가지정도를 설명할 수 있다.
1. 첫 번째는 `--gpus` 옵션을 주는 것.
  - `--gpus` 사용
  사용 예제는 다음과 같다. 예제에 사용된 컨테이너 이미지는 `Ubuntu:18.04`로 한다.
  ```
  docker run --rm --gpus all ubuntu:18.04 nvidia-smi
  ```
  - 특정 gpu만을 지정하여 사용하려면 `all` 대신 디바이스 번호인 `0`, `1`등으로 지정해주면 된다.
  ```
  docker run --rm --gpus 0 ubuntu:18.04 nvidia-smi
   ```
2. 두 번째는 `--runtime`과 환경변수로 `-e NVIDIA_VISIBLE_DEVICES` 옵션을 주는 것.
  - `--runtime` 사용
  해당 옵션을 사용할 때는 환경변수 `NVIDIA_VISIBLE_DEVICES`를 같이 준다.
  ```
  docker run --rm --runtime=nvidia -e NVIDIA_VISIBLE_DEVICES=all ubuntu:18.04 nvidia-smi
  ```
  - 해당 옵션 또한 특정 GPU 디바이스를 지정할 수 있다.
  ```
  docker run --rm --runtime=nvidia -e NVIDIA_VISIBLE_DEVICES=0 ubuntu:18.04 nvidia-smi
  ```
