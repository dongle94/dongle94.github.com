---
title: "[TensorFlow 2] Object Detection API training"
date: 2020-09-19
last_modified_at: 2021-01-18
excerpt: "TF 2 Object Detection API 모델 학습"
categories:
  - TensorFlow
tags:
  - TensorFlow
  - TensorFlow 2
  - Object Detection
  - Training
  - TF API
header:
  teaser: /assets/images/logo/tensorflow-logo.png
  overlay_image: /assets/images/logo/tensorflow-logo-wide.png
  og_image: /assets/images/profile.png
toc: true
toc_sticky: true
toc_label: "On this page"  # basic is 'On this page'
toc_icon: "smile"
published: true
---
앞선 [Object Detection API Setting 포스팅](https://dongle94.github.io/tensorflow/tf2-obj-detection-api-setting/) 이어 TensorFlow 2 MODEL API를 통한 Model Zoo 네트워크 모델의 학습 방법을 다룬다.
순서는 다음과 같다.  
1. TensorFlow model API 설치
2. **데이터셋 준비**  
3. **Training 진행**
4. **Evaluation 진행**

  
## 데이터셋 준비
### TF Record
데이터셋은 어노테이션 작업을 통해 tfrecord 로 만들어준다. 어노테이션 작업은 [Labelimg](https://github.com/tzutalin/labelImg ), [Vatic](http://www.cs.columbia.edu/~vondrick/vatic/)등의 툴을 통해 작업하고, TFrecord로 변환할 수 있다. TFrecord 제작에 대한 설명은 여기선 언급하지 않는다. Train set 과 Validation set을
나누어주고 만들어주고 위치는 `tensorflow/data/<데이터명>`으로 생성`train`과 `val` 디렉토리로 나누어 보관한다.  
`<데이터명>`은 자신의 데이터셋이 어떤 Object에 대한 데이터인지 간략히 적어준다. 예를 들면 `Cars`나 `person_face`같이 이름을 보면 어떤 데이터셋인지 알 수 있게 한다.<br>

### Label_map.txt
해당 데이터 셋에 대한 `label_map.txt`도 만들어 준다. 해당 파일에는 우리가 학습시키고자 하는 class정보가 딕셔너리 형태로 `id` 와 `name`으로 나뉘어 저장되어 있다. `label_map.txt` 파일의 구조는 아래와 같다. 파일의 위치는 `train`, `val` 디렉토리와 같은 위치에 배치해준다.
```yaml
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
...
```
<br>

여기까지 진행했다면 디렉토리 구조는 아래와 같을 것이다.
```shell
tensorflow
├─ models/
│  └─ ...
└─ data/
   └─<data_name>
      ├─ train/
      ├─ val/
      └─ label_map.txt
```

### Network Model zoo Download
이후 우리가 학습시킬 Network Model을 가져와야한다. 여기서는 [Tensorflow model zoo 2](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/tf2_detection_zoo.md )에
있는 목록 중에서 모델을 클릭해 다운로드 하면 된다. 나는 하나의 예시로 EfficientDet D0 버전을 가져왔다.  
`EfficientDet`은 이미지 분류인 `Efficient Net`을 기반으로 객체 감지에 맞게 변형한 딥러닝 네트워크 모델이다.   이에 대한 자세한 내용은 논문을 참조하자.
 - [EfficientNet paper](https://arxiv.org/abs/1905.11946)
 - [EfficientDet paper](https://arxiv.org/abs/1911.09070)

여기서는 `tensorflow/model_zoo/tf2` 디렉토리를 만들어서 아래에 해당 pretrained model을 다운로드하고 압축해제 해서 배치하였다. 디렉토리 구조는
아래와 같다.

```shell
tensorflow
├─ data/
│  └─ ...
├─ models/
│  └─ ...
└─ model_zoo/
   └─ tf2/
      └─ efficientdet_d0_coco17_tpu-32
```

### Train parameter setting
우리가 학습할 네트워크 모델이 저장될 디렉토리를 만든다. 여기서는 `tensorflow/train` 디렉토리로 추가한다. `train` 디렉토리 안에 우리가 학습할 모델명으로 폴더를 만들고 `tensorflow/model_zoo`에 저장해놓은 네트워크 모델 디렉토리에 있는 `pipeline.config` 파일을 `tensorflow/train/`안에 있는 모델 경로로 옮긴다.  
여기서는 `tensorflow/train/effidet_d0`를 만들고 `model_zoo/tf2/efficientdet_d0_coco17_tpu-32` 디렉토리 아래에 있던 `pipeline.config`를 복사해서 가져왔다.
```shell
# from /tensorflow/
$ mkdir -p /tensorflow/train/tf2/effidet_d0 
$ cp model_zoo/tf2/efficientdet_d0_cooo17_tpu-32/pipeline.config train/tf2/effidet_d0/
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
   └─ tf2
       ├─ effidet_d0
       └─ pipeline.config     
```
<br>

### pipeline.config 수정
`pipeline.config`파일에는 꼭 수정해줘야할 부분들이 있다. 
- model
  - `num_classes`: 우리가 학습시켜줄 class들의 수 이다. 즉, `label_map.txt`의 클래스 개수
- train_config
  - `batch_size`: model_zoo의 pretrained 모델들은 tpu를 통한 학습 등, 고사양에서 학습을 진행되었기에 배치사이즈가 높게 설정되어있다. 일반적인 Geforce 계열 그래픽카드 등의 GPU를 사용하는 일반적인 데스크탑 PC는 기본값 보다 낮춰야한다. `bacth size` 가 높으면 OOM(Out Of Memory) 에러가 발생할 수 있다. 
  - `optimizer/.../cosine_decay_learning_rate`: 러닝레이트는 cosine decay가 적용되어 있다.
   [cosine decay learning rate](https://hoya012.github.io/blog/Bag-of-Tricks-for-Image-Classification-with-Convolutional-Neural-Networks-Review/) 는 링크 참조.
   이에 따른 `base rate`와 `total step`을 설정한다. `warmup_steps`에 대해서는 처음 시작 러닝레이트에 대한 설정이다.
  - `fine_tune_checkpoint`: transfer learning의 핵심인 파인 튜닝 체크포인트 경로이다. pretrain 될 체크포인트의 경로를 설정한다. 위 상황과 같은 설정에서는 `/tensorflow/model_zoo/tf2/efficientdet_d0_coco17_tpu-32/checkpoint/ckpt-0` 을 가진다. 절대 경로를 입력해준다.
  - `num_steps`: 몇 steps을 학습할 것 인가에 대한 설정. `batch_size`와 자신의 데이터셋에 있는 학습 이미지들의 수를 고려해야한다. 
    - `batch_size * num_step = 실제 학습한 데이터셋의 이미지 수` 이므로 배치사이즈를 증가시켰다면 오버피팅이 나지 않도록 적당히 스텝을 줄이는 과정도 어느정도 필요하다.
  - `fine_tune_checkpoint_type`: 분명 Object Detection 모델을 학습하는데 일부 모델은 값이 `classification`으로 되어있다. 
    이 값을 `detection`으로 바꿔준다.
- train_input_reader
  - `label_map_path`: 우리가 저장해준 label_map.txt 경로를 입력. `ex) tensorflow/data/label_map.txt`
  - `tf_record_input_reader/input_path`: 학습 데이터 셋의 경로이다. 1개면 해당 파일만 적어주면 되고 여러개라면 전부 포함되도록 적어준다.
    `ex) <homepath>/tensorflow/data/train/*.record`
- eval_input_reader
  - `label_map_path`: train_input_reader 의 내용과 같다.
  - `tf_record_input_reader/input_path`: 검증(Evaluation) 데이터 셋의 경로이다. `ex) path/to/tensorflow/data/val/*.record`

그 외에도 수정을 해주면 좋은 파라미터들이 있지만 여기선 언급하지 않는다. 

## 네트워크 모델 학습(Training) 
TensorFlow 2에서의 Object Detection API를 통한 네트워크 모델 학습은 `models/research/object_detection/model_main_tf2.py`를 실행한다. 실행 명령어는 아래와 같다.
```shell
$ cd ~/tensorflow/models/research/object_detection

$ export PYTHONPATH=/data/tensorflow/models/research:/data/tensorflow/models/research/slim

$ CUDA_VISIBLE_DEVICES=0 python model_main_tf2.py \
--pipeline_config_path=path/to/tensorflow/train/effidet_d0/pipeline.config \
--model_dir=path/to/tensorflow/train/effidet_d0 \
--checkpoint_every_n=5000 \
--alsologtostderr
```
- `PYTHONPATH`는 `models`리포지토리의 `research`와 `research/slim` 경로를 export 해준다. 여러번 학습할 일이 있다면 bashrc 혹은 아나콘다 activate 쉘 스크립트에 명령어를 추가해도 된다. `~/.bashrc`를 열어 아래 내용을 추가하면 터미널 실행 시 자동 적용된다..
  ```shell
  $ vi ~/.bashrc
  
  # 제일 아래에 해당 내용 추가. <tensorflow> 경로에 유의
  export PYTHONPATH=$PYTHONPATH:/tensorflow/models/research:/tensorflow/models/research/slim
  ```
- `CUDA_VISIBLE_DEVICES`파라미터를 이용하면 GPU가 2개 이상 설치되어 있을 시 GPU를 직접 지정해서 학습을 진행할 수 있다. 첫번째 GPU는 0번 이다.
### Parameters 
- `--pipeline_config_path`는 `pipeline.config`파일의 위치 경로를 적어준다. 
- `--model_dir`은 학습 시에 체크 포인트 및 학습될 모델이 저장될 경로를 적는다. 
- `--checkpoint_every_n`은 학습 중 단계를 저장할 체크포인트를 몇 Step마다 저장하는 지에 대한 파라미터이다. 기본 값이 1000으로 되어있어서 10만 steps 이상의 학습을 진행 시에는 일반적으로 올리는 것이 좋다. 개인적으로는 3,000 혹은 5,000 정도를 사용한다.

추가적으로 옵션 파라미터를 더 줄 수 있다.  `--train_steps`을 직접 명시해주면 해당 스텝 수로 학습하게 되지만 `pipeline.config`에 `num_steps`를 적어놓았기에 따로 사용은 잘 안하는 편이다.

### checkpoint_max_to_keep
또한 체크포인트가 저장되는 최대 개수는 직접 Tensorflow models 라이브러리 코드를 수정해서 바꿀 수 있다. 
  `tensorflow/models/research/object_detection/model_main_tf2.py` 의 하단부 `train_loop()` 함수에 `checkpoint_max_to_keep`를 추가해준다. 기본값은 `model_lib_v2.py`에 7로 정의되어있으며  10, 20, 50 등의 값으로 바꿀 수 있다.

## 네트워크 모델 검증(Evaluation) 
TF 1버전과는 달리 TF 2.x 버전의 Evaluation 과정은 학습과 동시에 제공되지 않는다. TF1 에서는 학습을 진행하다가 체크포인트가 생성되면 체크포인트 Evaluation을 수행 뒤 다시 학습을 재개한다.  
그래서 TF2의 Evaluation 학습 중에 **체크포인트 생성 시**에 해당 체크포인트를 검증하는 작업이기 때문에 
모델 학습과 **동시**에 Evaluation을 돌려야 한다. 체크포인트가 생길 때마다 Evaluation Data를 이용하여 loss, mAP 수치 등을 확인할 수 있다. 사용하는 코드는 같으나 파라미터를 다름 설정해야 함에 유의한다. 아래와 같은 명령어로 실행한다.
```shell
CUDA_VISIBLE_DEVICES="" python model_main_tf2.py \
--pipeline_config_path=path/to/tensorflow/train/effidet_d0/pipeline.config \
--model_dir=path/to/tensorflow/train/effidet_d0 \
--checkpoint_dir=path/to/tensorflow/train/effidet_d0 \
--sample_1_of_n_eval_examples=1 \
--alsologtostderr
```
- 해당 실행 커맨드에서는 `CUDA_VISIBLE_DEVICES` 값이 `""`이다. 0이나 1이 아닌 아무 값도 주지않음(null)으로써 GPU가 아닌 CPU를 사용하는 것 이다.
### Parameters 
- training 때와 같이 `--pipeline_config_path`와 `--model_dir`을 입력한다. `pipeline.config`파일 안에는 `eval_config` 항목이 설정 되어있고 `model_dir`을 통해 학습 중인 모델의 체크포인트 저장 경로를 제공한다.
- `--checkpint_dir` 파라미터를 선언해줌으로써 Evaluation 과정임을 코드가 인지한다. 학습이 진행됨에 따라 새로운 체크포인트가 생기고, 새로운 체크포인트가 생길 때마다 Evaluation 과정을 거친다.
- `--sample_1_of_n_eval_examples` 파라미터를 1로 주면 1분의 1. 즉, 전체 Evaluation 데이터셋을 전부 확인한다. 예를 들어 값을 5로 준다면 Evaluation Set의 5분의 1. 즉, 20%만 이용한다.
  

---
> 해당 포스팅은 TensorFlow가 제공하는 공식 가이드 문서를 참고하였다.  
> [TensorFlow 2 Object Detection API Tutorial](https://tensorflow-object-detection-api-tutorial.readthedocs.io/en/latest/index.html)