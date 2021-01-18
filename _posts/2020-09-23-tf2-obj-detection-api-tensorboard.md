---
title: "[TensorFlow 2] Object Detection API tensorboard 로그 파일 크기 조정"
date: 2020-09-23
excerpt: "TF Object Detection API 학습 시 텐서보드 이벤트 로그파일 크기를 감소시키는 방법"
categories:
  - TensorFlow
tags:
  - TensorFlow
  - TensorFlow 2
  - Object Detection
  - Training
  - TF API
  - Logging
header:
  <!--teaser: /assets/images/page-header-teaser.png-->
  <!--overlay_image: /assets/images/unsplash-image-1.jpg-->
  og_image: /assets/images/profile.png
toc: true
toc_sticky: true
toc_label: "On this page"
toc_icon: "smile"
published: true
---
## 도입
TF2 Object Detection API를 통해 객체감지 모델을 학습하다보면 Tensorboard 의 로그파일 사이즈가 매우 방대하게 커지는 경우가 있다.
이를 조절하는 방법에 대해 알아본다. TF2로 객체감지 모델을 학습 하다보면 기본적으로 1 step 마다 텐서보드 로그가 기록된다.
한 50만 steps, 100만 steps의 학습을 마친 뒤에 텐서보드 이벤트 로그파일을 보면 무식할 정도의 파일 용량을 차지한다.
본인의 경우엔 수백 기가바이트의 이벤트 파일이 만들어지는 모습을 봤다. 


## TF 1 의 경우
TF 1에서 Object Detection API를 사용하면 Object Detecton 모듈의 내부가 조금 다른데 Tensorflow Github의 models 리포지토리에서
[1.13 브랜치](https://github.com/tensorflow/models/tree/r1.13.0 )를 설치해보면 알 수 있다. 마스터 브랜치나 2.x 대의 브랜치랑은 다르게
`/research/object_detection/` 경로에 `_v2.py` 파일들이 없다. 즉, 텐서플로 2가 나오기 전 1 버전대의 모듈을 이용한다는 전제하에 설계되어서 해당
브랜치에서는 `model_main.py`의 [estimator config 부분](https://github.com/tensorflow/models/blob/r1.13.0/research/object_detection/model_main.py#L62 )만
수정해주면 된다. 기존방식은 다음과 같다.  
```
config = tf.estimator.RunConfig(model_dir=FLAGS.model_dir)
```
해당 부분을 사용자의 요구에 맞게 수정하면 된다. 나는 다음과 같이 수정하였다.

### 사용 예
```python
config = tf.estimator.RunConfig(model_dir=FLAGS.model_dir,
                                save_summary_steps=500,
                                save_checkpoints_steps=5000,
                                save_checkpoints_secs=None,
                                keep_checkpoint_max=50,
                                log_step_count_steps=500)
```
- `save_summary_steps`와 `log_step_count_steps` 파라미터를 통해서 `tf.summary` 즉, 텐서보드에 기록하는 빈도를 조절할 수 있다. 로그 이벤트
파일이 작은 빈도로 기록되면 용량이 방대해 질 수 있는데 이를 막기 위함이다.  
- `save_checkpoints_steps`와 `save_checkpoint_secs` 는 각각 체크포인트를 몇 스텝마다 혹은 몇 초마다 기록하겠다 하는 파라미터이다. 둘 중에
하나만 지정할 수 있으며 만약 둘 다 값을 지정할 시 오류가 발생한다. 
- `keep_checkpoint_max` 는 체크포인트를 저장할 최대 개수이다. `10`으로 지정한다면 10개가 생긴 이후 다음에 저장되는 체크포인트는 가장 오래된 
체크포인트를 지우고 저장된다. 

TF 2는 `PYTHONPATH` 관련해서 이용하는 파이썬 패키지의 경로에 유의해야한다. 아래를 참고하자.  

## TF 2의 경우 
[앞선 포스팅(TF2OD API 설치)](https://dongle94.github.io/tensorflow/obj-detection-api-tf2-training-1/ )의 방식으로 TF 2의
Object Detection API를 설치했다면 설치된 환경의 `PIP` 모듈 중에 `object-detectio (version: 0.1)`이 설치되어있다. 
```shell
$ pip list
> ...
  object-detection      0.1
  ...
```
이는 `models/research/object_detection` 이 파이썬 패키지 형태로 환경에 설치된 것이다. 일반적으로 다른 셋팅 없이 `model_main_tf2.py` 를 
실행한다면 해당 코드는 파이썬 환경의 `site-package`에 설치되어있는 `object-detection` 모듈을 참조하게 된다. 그래서 해당 리포지토리만을 이용하여
작업하려면 `PYTHONPATH`를 지정해줘야한다. 일반적으로는 다음과 같이 지정한다. 
```shell
export PYTHONPATH=<tensorflow_path>/models/research:<tensorflow_path>/models/research/slim
```
꼭 굳이 위와같이 하지 않아도 상관은 없다. API를 학습하기 쉽도록 사용자의 편의에 맞게 셋팅만 하면된다. 여러가지 방식으로 셋팅가능하다.
- `PYTHONPATH`를 지정 후 models 리포지토리의 코드를 수정
- 실행하고자하는 파이썬 파일만 심볼릭 링크로 위치 조절 후 `object-detection` 모듈 코드를 수정
- 처음부터 `object-detection` 모듈 파일만 사용 

기존 방식은 `models/research/object_detection/model_lib_v2.py`를 수정해서 텐서보드 로그 파일의 출력 빈도를 조정할 수 있다.
`global_step` 변수가 사용되는 부분을 이용해 출력의 빈도를 조정할 수 있다. 기존은 매 step 마다 이벤트 로그가 기록되도록 설정되어 있다.
이에 대해 수정할 부분은 다음과 같다.  

### 사용 예 
`models/research/object_detection/model_lib_v2.py` 에서
  - [line 262](https://github.com/tensorflow/models/blob/master/research/object_detection/model_lib_v2.py#L262)
    ```python
    if global_step % 500 == 0:   # 해당 내용 추가
      for loss_type in losses_dict:
        tf.compat.v2.summary.scalar(
          loss_type, losses_dict[loss_type], step=global_step)
    ```
    - loss에 대한 기록 주기를 `global_step` 기준 500번 마다로 수정한다.
  - [line 272](https://github.com/tensorflow/models/blob/master/research/object_detection/model_lib_v2.py#L272)
    ```python
    if global_step % 500 == 0:
      tf.compat.v2.summary.scalar('learning_rate', learning_rate, step=global_step)
      tf.compat.v2.summary.image(
          name='train_input_images',
          step=global_step,
          data=features[fields.InputDataFields.image],
          max_outputs=10)
    ```
    - `learning_rate` 기록 주기를 `global_step` 기준 500번 마다로 수정한다.
    - image 저장 개수를 `max_outputs` 파라미터를 이용해 `10`으로 수정한다.
  - [line 561](https://github.com/tensorflow/models/blob/master/research/object_detection/model_lib_v2.py#L561)  
    ```python
    with tf.compat.v2.summary.record_if(
      lambda: global_step % 500 == 0): # num_steps_per_iteration only
        # Load a fine-tuning checkpoint.
        if train_config.fine_tune_checkpoint:
    ```
    - `tf.compat.v2.summary`가 `global_step` 500번 마다 기록하도록 `record_if`의 파라미터 조건을 수정한다.

## 결론 
위와 같은 수정을 마치고 TF2OD API로 학습을 실행해보면 실제로 `train` 디렉토리 안의 텐서보드 로그 파일의 용량이 기존에 비해 상당이 작게 나오는 것을
알 수 있다. 적은 빈도로 이벤트 로그를 찍는 것 만이 좋은 것은 아니다. 본인의 모델의 학습 steps 수에 맞게 학습이 잘 되고 있는 지를 알아 볼 수 있고,
해당 정보를 제대로 전달받을 수 있도록 하는 것이 중요하다.