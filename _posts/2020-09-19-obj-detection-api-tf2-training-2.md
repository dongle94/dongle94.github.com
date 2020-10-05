---
title: "[TensorFlow 2] Object Detection API training 2"
date: 2020-09-19
categories:
  - TensorFlow2
tags:
  - TensorFlow
  - TensorFlow 2
  - Object Detection
  - Training
  - TF API
toc: true
toc_sticky: true
toc_label: "On this page"  # basic is 'On this page'
toc_icon: "smile"
---

해당 포스팅은 앞선 1편에 이어 TensorFlow 2 MODEL API를 통한 Efficient Net Training 방법을 다룬다.
순서는 다음과 같다.  
1. TensorFlow model API 설치
2. 데이터셋 준비  
    2.1. 원하는 네트워크 모델 다운로드.
3. Training 진행
4. Evaluation 진행


## Train parameter setting
추가적으로 우리가 학습할 네트워크 모델이 저장될 디렉토리를 만들어준다. 여기서는 `tensorflow/train`디렉토리를 새롭게 만들겠다. 추가적으로 우리가 
학습할 모델이 저장될 폴더도 `train` 안에 하나 만들고 `model_zoo`에  Efficientdet d0 디렉토리 안에 있는 `pipeline.config` 파일도 복사해서
안에 넣어준다. 여기서는 `tensorflow/train/effidet_d0`를 만들고 `model_zoo/tf2/efficientdet_d0_coco17_tpu-32` 디렉토리 아래에 있던
`pipeline.config`를 복사해서 가져왔다.
```shell
$ mkdir -p train/effidet_d0 
$ cp model_zoo/tf2/efficientdet_d0_cooo17_tpu-32/pipeline.config ./train/effidet_d0/
```
<br>

여기까지 되었다면 디렉토리 구조는 아래와 같다.
```shell
tensorflow
├─ data/
│  └─ ...
├─ models/
│  └─ ...
├─ model_zoo/
│  └─ ...
└─ train/
   └─ effidet_d0
      └─ pipeline.config     
```
<br>

`pipeline.config`파일에는 특별히 수정해줘야할 부분들이 있다. 해당 내용은 `vi`나 `gedit`등으로 파일을 열어 수정해준다.
- model
  - `num_classes`: 우리가 학습시켜줄 class들의 수 이다. 즉, `label_map.txt`의 클래스 개수
- train_config
  - `batch_size`: model_zoo의 pretrained 모델들은 tpu등의 엄청난 고사양에서 학습을 진행해서 배치사이즈가 높게 설정되어있다. RTX 2080TI나
   기타 단일 GPU를 사용하는 일반적인 워크스테이션은 낮춰야한다. 일반적으로 `bacth size` 가 높으면 OOM(Out Of Memory) 에러가 발생한다. 
  - `optimizer/.../cosine_decay_learning_rate`: 러닝레이트는 cosine decay가 적용되어 있다.
   [cosine decay learning rate](https://hoya012.github.io/blog/Bag-of-Tricks-for-Image-Classification-with-Convolutional-Neural-Networks-Review/) 는 링크 참조.
   이에 따른 `base rate`와 `total step`을 설정한다. `warm_up`에 대해서는 처음 치솟는 러닝레이트에 대한 통제 설정이다.
  - `fine_tune_checkpoint`: transfer learning의 핵심인 파인 튜닝 체크포인트 경로이다. 위 상황과 같은 설정에서는 
    `path/to/tensorflow/model_zoo/tf2/efficientdet_d0_coco17_tpu-32/checkpoint/ckpt-0` 을 가진다. 절대 경로를 입력해준다.
  - `num_steps`: 몇 steps을 학습할 것 인가에 대한 설정. `batch_size`와 자신의 데이터셋에 있는 학습 이미지들의 수를 고려해야한다. 
    - `batch_size * num_step = 실제 학습한 데이터셋의 이미지 수` 이므로 너무 적게해서 학습이 안되거나 너무 학습을 많이 돌려서 Overfitting이
      나지 않도록 주의한다.
  - `fine_tune_checkpoint_type`: 분명 Object Detection 모델을 학습하는데 일부 모델은 값이 `classification`으로 되어있다. 
    이 값을 `detection`으로 바꿔준다.
- train_input_reader
  - `label_map_path`: 우리가 저장해준 label_map.txt 경로를 입력. `ex) path/to/tensorflow/data/label_map.txt`
  - `tf_record_input_reader/input_path`: 학습 데이터 셋의 경로이다. 1개면 해당 파일만 적어주면 되고 여러개라면 전부 포함되도록 적어준다.
    `ex) <homepath>/tensorflow/data/train/*.record`
- eval_input_reader
  - `label_map_path`: train_input_reader 의 내용과 같다.
  - `tf_record_input_reader/input_path`: 검증(Evaluation) 데이터 셋의 경로이다. `ex) path/to/tensorflow/data/val/*.record`

여기까지 진행했으면 네트워크 모델 학습 준비는 마쳤다.

## 네트워크 모델 학습(Training) 
TensorFlow 2에서의 Object Detection API를 통한 네트워크 모델 학습은 `models/research/object_detection/model_main_tf2.py`를
 통해서 한다. 실행 명령어는 아래와 같다.
```shell
$ cd ~/tensorflow/models/research/object_detection

$ export PYTHONPATH=/data/tensorflow/models/research:/data/tensorflow/models/research/slim

$ CUDA_VISIBLE_DEVICES=0 python model_main_tf2.py \
--pipeline_config_path=path/to/tensorflow/train/effidet_d0/pipeline.config \
--model_dir=path/to/tensorflow/train/effidet_d0 \
--checkpoint_every_n = 5000 \
--alsologtostderr
```
- `PYTHONPATH`는 `models`리포지토리의 `research`와 `research/slim` 경로를 export 해준다. 여러번 학습할 일이 있다면 bashrc에 명령어를
  추가해줘도 된다. `~/.bashrc`를 열어 아래 내용을 추가한다.
  ```
  $ vi ~/.bashrc
  
  # 제일 아래에 해당 내용 추가.
  export PYTHONPATH=$PYTHONPATH:/data/tensorflow/models/research:/data/tensorflow/models/research/slim
  ```
- `CUDA_VISIBLE_DEVICES`파라미터를 이용하면 GPU가 2개 이상 설치되어 있을 시 GPU를 직접 지정해서 학습을 진행할 수 있다. 첫번째 GPU는 0번 이다.
- `--pipeline_config_path`는 `pipeline.config`파일의 위치 경로를 적어준다. `--model_dir`은 학습 시에 체크 포인트 및 학습될 모델이 저장될
경로를 적는다. `--checkpoint_every_n`은 학습 중 단계를 저장할 체크포인트를 몇 Step마다 저장하는 지에 대한 파라미터이다.

- 추가적으로 옵션 파라미터를 더 줄 수 있다. 
  `--train_steps`을 직접 명시해주면 해당 스텝 수로 학습하게 된다.<br>

- 또한 체크포인트가 저장되는 최대 개수는 직접 Tensorflow models 라이브러리 코드를 수정해서 바꿀 수 있다. 
  `tensorflow/models/research/object_detection/model_lib_v2.py` 의 `train_loop()` 함수의 파라미터인 `checkpoint_max_to_keep`을
  기존 값 7에서 10, 20, 50 등의 값으로 바꿀 수 있다.

## 네트워크 모델 검증(Evaluation) 
`models` 리포지토리는 학습 중인 모델의 Evaluation 기능을 제공한다. 학습 중에 **체크포인트 생성 시**에 해당 체크포인트를 검증하는 작업이기 때문에 
모델 학습과 **동시**에 Evaluation을 돌려주는 것이 좋다. 체크포인트가 생길 때마다 Evaluation Data를 이용하여 loss, mAP 수치 등을 확인할
 수 있다. 사용하는 모듈은 같으나 파라미터가 다름에 유의한다. 아래와 같은 커맨드로 실행한다.
```shell
CUDA_VISIBLE_DEVICES="" python model_main_tf2.py \
--pipeline_config_path=path/to/tensorflow/train/effidet_d0/pipeline.config \
--model_dir=path/to/tensorflow/train/effidet_d0 \
--checkpoint_dir=path/to/tensorflow/train/effidet_d0 \
--sample_1_of_n_eval_examples=1 \
--alsologtostderr
```
- 해당 실행 커맨드에서는 `CUDA_VISIBLE_DEVICES` 값이 `""`이다. 0이나 1이 아닌 아무 값도 주지않음(null)으로써 GPU가 아닌 CPU를 사용하는 것 이다.
- training 때와 같이 `--pipeline_config_path`와 `--model_dir`을 입력한다. `pipeline.config`파일 안에는 Evaulation 파라미터가 
  들어있고 `model_dir`을 통해 학습 중인 모델의 체크포인트 저장 경로를 제공한다. `--checkpint_dir` 파라미터를 선언해줌으로써 Evaluation 
  과정임을 코드가 인지한다. 학습이 진행됨에 따라 새로운 체크포인트가 생기고, 새로운 체크포인트가 생길 때마다 Evaluation 과정을 거친다.
  `--sample_1_of_n_eval_examples` 파라미터를 1로 주면 1분의 1. 즉, 전체 Evaluation 데이터셋을 전부 확인한다. 예를 들어 값을 5로 준다면
  Evaluation Set의 5분의 1. 즉, 20%만 이용한다.
  
## `nohup` 이용해서 백그라운드로 실행
`nohup` 커맨드를 이용하면 지금 활성화 되어있는 터미널 창과 관련없이 백그라운드에서 프로그램을 실행 할 수 있다. 위의 train 커맨드를 기반으로 수정된
실행 커맨드는 아래와 같다.
```shell
$ CUDA_VISIBLE_DEVICES=0 nohup python object_detection/model_main_tf2.py \
--pipeline_config_path=path/to/tensorflow/train/effidet_d0/pipeline.config \
--model_dir=path/to/tensorflow/train/effidet_d0 \
--checkpoint_every_n = 5000 \
--alsologtostderr > train.log & 
```
기존 train 코드 실행 커맨드에 비해 첫 줄과 마지막 줄에 추가된 내용이 있다. 
- `python` 프로세스를 실행 시키기 전에 `nohup` 커맨드를 추가 시킴으로써 터미널을 종료해도 계속 프로세스는 실행되고 있게 할 수 있다.
- 마지막 줄에 `> train.log &` 는 `train.log` 라는 파일을 만들어 터미널의 실행 결과를 텍스트로 저장하고 `&` 마크를 붙여야 백그라운드로 실행한다.

Evaulation도 마찬가지로 `CUDA_VISIBLE_DEVICES=""` 다음에 `nohup`을 넣어주고, 마지막 줄에 `> eval.log &` 내용을 추가 함으로써 적용할 수 있다.
