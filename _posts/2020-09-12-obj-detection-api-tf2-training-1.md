---
title: "[TensorFlow 2] Object Detection API training 1"
categories:
  - TensorFlow 2
tags:
  - TensorFlow
  - TensorFlow 2
  - Object Detection
  - Training
  - TF API
---

해당 포스팅은 TensorFlow 2 MODEL API를 통한 Efficient Net Training 방법을 다룬다.
순서는 다음과 같다.  
1. TensorFlow model API 설치
2. 데이터셋 준비  
    2.1. 원하는 네트워크 모델 다운로드.
3. Training 진행
4. Evaluation 진행

## TensorFlow API 설치 
우선 앞으로 api 관련 데이터들이 들어갈 `tensorflow` 디렉토리를 하나 만든다. 
앞으로 다루기 쉽게 `models` API 리포지토리와 각종 학습의 내용물들이 저장될 곳이다.
```shell script
$ mkdir tensorflow
```
<br>

TensorFlow github [models Repository](https://github.com/tensorflow/models )를 내려받는다.
```shell script
$ cd tensorflow
$ git clone https://github.com/tensorflow/models.git
``` 
<br>

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
<br>  
  
이어서 `research` 디렉토리에서 관련 파이썬 패키지들을 설치해준다. 만약 `protoc` 커맨드가 없어서 해당 명령어가 작동하지 않는다면 우선적으로 
프로토버프 컴파일러를 설치해줘야 한다. 해당 라이브러리는 `sudo apt install protobuf-compiler` 을 통해 설치할 수 있다. 아래 명령어들을 
통해 Object Detection API에 필요한 파이썬 패키지들을 설치해주자.
```shell script
$ cd models/research

# Compile protos.
$ protoc object_detection/protos/*.proto --python_out=.

# Install TensorFlow Object Detection API.
$ cp object_detection/packages/tf2/setup.py .
$ python -m pip install .
```
<br>

위의 명령어를 실행하면 Tensorflow 2버전의 최신버전이 자동으로 설치가 된다. 나는 설치 된 `tensorflow`를 지우고 `tensorflow-gpu==2.2.0` 을 설치했다. 
```shell script
# Remove tensorflow.
$ pip uninstall tensorflow

# Install tensorflow gpu version.
$ pip install tensorflow-gpu==2.2.0
```
<br>

제대로 설치 되었는지 확인하려면 `models/research/object_detection` 디렉토리 안에 있는 빌더 테스트 모듈을 통해 할 수 있다.
```shell script
# Test the installation. (tensorflow/models/research/)
$ python object_detection/builders/model_builder_tf2_test.py
```
<br>

설치에 문제가 없이 작동시 아래와 같이 출력된다
```shell script
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
<br>  

## 데이터셋 준비 
데이터셋은 어노테이션 작업을 통해 tfrecord 로 만들어준다. 어노테이션 작업은 [Labelimg](https://github.com/tzutalin/labelImg )등의 툴을
다운로드 받아서 작업하고, TFrecord로 변환할 수 있다. 일단은 TFrecord 제작에 대한 자세한 설명은 넘어간다. train set 과 validation set을
나누어주고 만들어주고 위치는 `tensorflow/data/<데이터명>`으로 생성`train`과 `val` 디렉토리로 나누어 보관한다. `<데이터명>`에 대한 이름은 자신의
데이터셋이 어떤 Object에 대한 데이터인지 간략히 적어준다. 예를 들면 `Cars`나 `person_face_emotion`같이 이름을 보면 어떤 데이터셋인지 알 수 
있게 한다.<br>

해당 데이터 셋에 대한 `label_map.txt`도 만들어 준다. 해당 파일에는 우리가 학습시키고자 하는 class정보가 딕셔너리 형태로 `id` 와 `name`으로
 나뉘어 저장되어 있다. `label_map.txt` 파일의 구조는 아래와 같다. 파일의 위치는 `train`, `val` 디렉토리와 같은 위치에 배치해준다.
```shell script
item {
  id: 1
  name: 'car'
}

item {
  id: 2
  name: 'person'
}

item {
  id: 3
  name: 'handbag'
}
...

```
<br>

여기까지 진행했다면 디렉토리 구조는 아래와 같을 것이다.
```shell script
tensorflow
├─ models/
│  └─ ...
└─ data/
   ├─ train/
   ├─ val/
   └─ label_map.txt
```

## Network Model zoo Download
이후 우리가 학습시킬 Backbone Network Model을 가져와야한다. 여기서는 [Tensorflow model zoo 2](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/tf2_detection_zoo.md )에
있는 목록 중에서 모델을 클릭해 다운로드 하면 된다. 나는 하나의 예시로 EfficientDet D0 버전을 가져온다. EfficientDet은 이미지 분류인 
Efficient Net을 기반으로 객체 감지에 맞게 변형한 딥러닝 네트워크 모델이다.   
이에 대한 내용은 논문을 참조하자.
 - [EfficientNet paper](https://arxiv.org/abs/1905.11946)
 - [EfficientDet paper](https://arxiv.org/abs/1911.09070)

여기서는 `tensorflow/model_zoo/tf2` 디렉토리를 만들어서 아래에 해당 pretrained model을 다운로드하고 압축해제 해서 배치하였다. 디렉토리 구조는
아래와 같다. 
```shell script
tensorflow
├─ data/
│  └─ ...
├─ models/
│  └─ ...
└─ model_zoo/
   └─ tf2/
      └─ efficientdet_d0_coco17_tpu-32
```

2에 이어서 포스팅한다. 
