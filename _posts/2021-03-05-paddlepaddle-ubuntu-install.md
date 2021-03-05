---
title: "[PaddlePaddle]Ubuntu 18.04 PaddlePaddle 2.0.0 Install"
date: 2021-03-05
excerpt: "우분투 18.04 paddlepaddle 프레임워크 설치"
categories:
  - PaddlePaddle
tags:
  - PaddlePaddle
  - Baidu
  - AI
  - ML
  - DL
  - Deep Learning
  - Framework
header:
  teaser: /assets/images/logo/paddlepaddle-logo.png
  overlay_image: /assets/images/logo/paddlepaddle-logo-wide.png
  og_image: /assets/images/profile.png
toc: true
toc_sticky: true
toc_label: "On this page"
toc_icon: "smile"
published: true
#classes: wide
---
## 계기
회사에서 PaddlePaddle 프레임워크를 다뤄볼 경험이 있어서 셋팅 방법에 대해 다뤄본다.
다뤄본 프로젝트는 [Paddle OCR](https://github.com/PaddlePaddle/PaddleOCR)이다.

## 환경
- OS: Ubuntu 18.04
- GPU: NVIDIA RTX 3090
- CUDA/CUDNN: CUDA 11.1 / CUDNN 8.0.5
- LANG: Python 3.6.9(Anaconda 3)

## 설치
PaddlePaddle는 현재 2.0.0 버전이 릴리즈 된 상태고 파이썬 휠 파일은 표준 CUDA 10.2 버전에 맞춰져 있다.
설치방법은 `pip`, `conda`, `docker` 등이 있고 로컬환경 구성을 위한 `pip`를 서술한다. 프로젝트 환경구성에 익숙하지 않은 
사용자라면 먼저 `Docker` 환경을 통해 경험을 하고 후에 고정된 환경을 `conda`나 `pip`로 구성하는 것을 권장한다.

### CUDA 11.1 / CUDA 11.0
```
python -m pip install paddlepaddle-gpu==2.0.0.post110 -f https://paddlepaddle.org.cn/whl/stable.html
```

### CUDA 10.2
```
python -m pip install paddlepaddle-gpu==2.0.0 -i https://mirror.baidu.com/pypi/simple
```

### CUDA 10.1
```
python -m pip install paddlepaddle-gpu==2.0.0.post101 -f https://paddlepaddle.org.cn/whl/stable.html
```

### CUDA 10.0
```
python -m pip install paddlepaddle-gpu==2.0.0.post100 -f https://paddlepaddle.org.cn/whl/stable.html
```

### CPU 버전
```
python -m pip install paddlepaddle -i https://mirror.baidu.com/pypi/simple
```


> 출처, 패들패들 공식 홈페이지(https://www.paddlepaddle.org.cn/)