---
title: "[TensorFlow 2] Object Detection API Setting"
date: 2020-09-12
last_modified_at: 2021-01-18
excerpt: "TF 2 Object Detection API 학습 환경 셋팅"
categories:
  - TensorFlow
tags:
  - TensorFlow
  - TensorFlow 2
  - Object Detection
  - Install
  - Training
  - TF API
header:
  teaser: /assets/images/logo/tensorflow_2.png
  overlay_image: /assets/images/logo/tensorflow_2_wide.png
  og_image: /assets/images/profile.png
toc: true
toc_sticky: true
toc_label: "On this page"  # basic is 'On this page'
toc_icon: "smile"
published: true
---
TensorFlow 2 MODEL API를 통한 Model Zoo Detection Model Training 방법을 다룬다.
순서는 다음과 같다.  
1. **TensorFlow model API 설치**
2. 데이터셋 준비  
3. Training 진행
4. Evaluation 진행

## Tensorflow 디렉토리 생성
우선 앞으로 API 관련 데이터들이 들어갈 `tensorflow` 디렉토리를 하나 만든다. 
앞으로 다루기 쉽게 `models` API 리포지토리와 각종 학습의 내용물들이 저장될 곳이다. 루트 디렉토리에 만들기 위해서는 `sudo` 권한이 필요하지만 편의상 루트 디렉토리에 만들었다고 하겠다. 실제로는 자신이 편한 경로에 만들어주면 된다.
```shell
$ mkdir /tensorflow
```

## Models Repository Download
TensorFlow Github의 [models Repository](https://github.com/tensorflow/models )를 내려받는다.
```shell
$ cd /tensorflow
$ git clone https://github.com/tensorflow/models.git
``` 

해당 디렉토리 구조는 아래와 같이 된다.
```
tensorflow
└─ models/
   ├─ community/
   ├─ official/
   ├─ orbit/
   ├─ research/
   └─ ...
```

## Protobuf Compile
이어서 `research` 디렉토리에서 관련 파이썬 패키지들을 설치해준다. 만약 `protoc` 커맨드가 없다고 결과가 출력된다면 `protobuf`를 설치해줘야한다.
```shell
sudo apt install protobuf-compiler
```
아래 명령어들을 통해 Object Detection API에 필요한 파이썬 패키지들을 컴파일해주자.
```shell
$ cd models/research

# Compile protos.
$ protoc object_detection/protos/*.proto --python_out=.

# Install TensorFlow Object Detection API.
$ cp object_detection/packages/tf2/setup.py .
$ python -m pip install .
```


## Object Detection API Install
위의 명령어를 실행하면 일단 `pip list`를 했을 때 `object_detection` 등의 라이브러리가 설치되어있다. 
```shell
$ pip list

>>
object-detection              0.1
tensorboard                   2.4.0
tensorboard-plugin-wit        1.7.0
tensorflow                    2.4.0
tensorflow-addons             0.12.0
tensorflow-datasets           4.2.0
tensorflow-estimator          2.4.0
tensorflow-hub                0.11.0
tensorflow-metadata           0.26.0
tensorflow-model-optimization 0.5.0
tf-models-official            2.4.0
tf-slim                       1.1.0
```
이 정도 설치가 확인되었다면 된다.

위 결과처럼 Tensorflow 2.x버전의 최신버전이 자동으로 설치가 된다. TF2 버전은 사용자의 필요에 맞게 원하는 버전으로 설치하면 된다. 나는 아래와 같이 설치 된 `tensorflow`를 지우고 `tensorflow-gpu==2.4.0` 을 설치했다. 
```shell
# Remove tensorflow.
$ pip uninstall tensorflow

# Install tensorflow gpu version.
$ pip install tensorflow-gpu==2.4.0
```

## Installation Check
제대로 설치 되었는지 확인하려면 `models/research/object_detection` 디렉토리 안에 있는 빌더 테스트 모듈을 통해 할 수 있다.
```shell
# Test the installation. (tensorflow/models/research/)
$ python object_detection/builders/model_builder_tf2_test.py
```

설치에 문제가 없이 작동시 아래와 같이 출력된다
```shell
...
[       OK ] ModelBuilderTF2Test.test_create_ssd_models_from_config
[ RUN      ] ModelBuilderTF2Test.test_invalid_faster_rcnn_batchnorm_update
[       OK ] ModelBuilderTF2Test.test_invalid_faster_rcnn_batchnorm_update
[ RUN      ] ModelBuilderTF2Test.test_invalid_first_stage_nms_iou_threshold
[       OK ] ModelBuilderTF2Test.test_invalid_first_stage_nms_iou_threshold
[ RUN      ] ModelBuilderTF2Test.test_invalid_model_config_proto
[       OK ] ModelBuilderTF2Test.test_invalid_model_config_proto
[ RUN      ] ModelBuilderTF2Test.test_invalid_second_stage_batch_size
[       OK ] ModelBuilderTF2Test.test_invalid_second_stage_batch_size
[ RUN      ] ModelBuilderTF2Test.test_session
[  SKIPPED ] ModelBuilderTF2Test.test_session
[ RUN      ] ModelBuilderTF2Test.test_unknown_faster_rcnn_feature_extractor
[       OK ] ModelBuilderTF2Test.test_unknown_faster_rcnn_feature_extractor
[ RUN      ] ModelBuilderTF2Test.test_unknown_meta_architecture
[       OK ] ModelBuilderTF2Test.test_unknown_meta_architecture
[ RUN      ] ModelBuilderTF2Test.test_unknown_ssd_feature_extractor
[       OK ] ModelBuilderTF2Test.test_unknown_ssd_feature_extractor
----------------------------------------------------------------------
Ran 20 tests in 38.233s

OK (skipped=1)
```


---
> 해당 포스팅은 TensorFlow가 제공하는 공식 가이드 문서를 참고하였다.  
> [TensorFlow 2 Object Detection API Tutorial](https://tensorflow-object-detection-api-tutorial.readthedocs.io/en/latest/index.html)