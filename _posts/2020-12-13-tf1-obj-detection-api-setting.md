---
title: "[TensorFlow 1] Object Detection API Install"
date: 2021-01-05
excerpt: "TF 1 Object Detection API 학습 환경 셋팅"
categories:
  - TensorFlow
tags:
  - TensorFlow 1
  - Frozen Graph
  - Object Detection
  - Export
  - TF API
  - Install
header:
  teaser: /assets/images/logo/tensorflow-logo.png
  overlay_image: /assets/images/logo/tensorflow-logo-wide.png
  og_image: /assets/images/profile.png
toc: true
toc_sticky: true
toc_label: "On this page"
toc_icon: "smile"
published: true
---
TensorFlow 1 MODELS 리포지토리의 Object Detection API 사용 환경 방법을 알아본다.

## Tensorflow/models 리포지토리 다운로드
일반적으로 [models 1.13 브랜치](https://github.com/tensorflow/models/tree/r1.13.0)를 내려받는다.
해당 링크를 타고 zip파일로 받아서 압축을 풀어도 되고 터미널 명령어로 수행하려면 다음과 같다.
```
git clone -b r1.13.0 https://github.com/tensorflow/models.git
```
다운로드 된 경로를 <path_to_tensorflow> 라고 하겠다. 현재 `<path_to_tensorflow>/models` 형태로 리포지토리가 내려 받아진 상태이다.

## Tensorflow 설치
```
# cpu
pip install tensorflow==1.x.x
# gpu
pip install tensorflow-gpu==1.x.x
```
NVIDIA AMPERE 시리즈 GPU 장치를 쓰는 PC는 바로 pip를 이용한 tensorflow가 호환성 문제로 작동을 안할 수 있다. 구글링을 해보면 `tf-nightly` 혹은 `tf-nightly-gpu`를 사용하는 블로그들이 있는데 이는 Tensorflow 2에 해당하고 1에는 해당하지 않는다. 추후 Nvidia Ampere 디바이스에서 Tensorflow 1 사용하는 방법 포스팅 예정

### Dependency 라이브러리 설치
```
sudo apt install protobuf-compiler python-pil python-lxml python-tk
pip install Cython
pip install contextlib2
pip install jupyter
pip install matplotlib
pip install pillow
pip install ㅣxml
```

## COCO API 설치
네트워크 모델을 Object Detection API를 이용하여 학습할 때, COCO Metric을 이용해 Evaluation 하기 위해서 COCO Metric 을 설치해준다. 해당 `pycocotools` 디렉토리를 `<path_to_tensorflow>/models/research` 하위로 위치시켜준다.
```
git clone https://github.com/cocodataset/cocoapi.git
cd cocoapi / PythonAPI 
make 
cp -r pycocotools <path_to_tensorflow>/models/research/
```

## Protobuf 컴파일
Object Detection API를 사용하여 모델을 학습하기 전에 Protobuf를 컴파일하여 모델 학습에 필요한 라이브러리들을 구성한다. `<path_to_tensorflow>/models/research` 경로에서 실행하면 된다.
```
# From tensorflow/models/research/
protoc object_detection/protos/*.proto --python_out=.
```

## PYTHONPATH 환경변수 추가
`PYTHONPATH` 환경변수에 `research` 와 `slim`의 경로를 추가해줘야한다 간단하게는 `<path_to_tensorflow>/models/research` 해당 경로에서 export로 선언을 해줘도 된다.
```
# From tensorflow/models/research/
export PYTHONPATH=$PYTHONPATH:`pwd`:`pwd`/slim
```
근데 모델 학습을 위해 매번 터미널에서 export 해주기엔 너무 번거로울 수 있다. 그래서 터미널 내에서 전역변수 처럼 쓰기 위해서 `~/.bashrc` 파일 수정 혹은 아나콘다 환경의 `anaconda3/envs/<환경명>/etc/conda/activate.d/` 경로에 쉘 스크립트를 만들어 주면 된다. **vi**나 **gedit**을 이용해서 최하단에 내용을 똑같이 추가해주면 된다. **아나콘다 acitvate 스크립트를 만들 때는 `deactivate` 시에 적용될 스크립트도 잊지 말고 만들어준다.**
```
export PYTHONPATH=$PYTHONPATH:<path_to_tensorflow>/models/research:<path_to_tensorflow>/models/research/slim
```

## 설치 완료 테스트
제대로 Tensorflow 1 Object Detection API 이 설치 완료되었다면 아래의 명령어를 실행 시켜서 결과를 체크해보자. `<path_to_tensorflow>/models/research` 경로에서 실행해보면 된다.
```
python object_detection/builders/model_builder_test.py
```
결과는 다음과 같다.
![](/assets/images/tf1-obj-detection-api/model_builder_test.png){: width="80%"}


---
> 텐서플로 공식 GITHUB 문서를 참고하여 해당 포스팅을 작성하였다.  
> [https://github.com/tensorflow/models/blob/r1.13.0/research/object_detection/g3doc/installation.md](https://github.com/tensorflow/models/blob/r1.13.0/research/object_detection/g3doc/installation.md)