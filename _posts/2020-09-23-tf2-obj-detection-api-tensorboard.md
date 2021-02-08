---
title: "[TensorFlow 2] Object Detection API tensorboard 로그 파일 크기 조정"
date: 2020-09-23
last_modified_at: 2021-01-19
excerpt: "TF2 Object Detection API 학습 시 텐서보드 이벤트 로그파일 크기를 감소시키는 방법"
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
  teaser: /assets/images/logo/tensorflow-logo.png
  overlay_image: /assets/images/logo/tensorflow-logo-wide.png
  og_image: /assets/images/profile.png
toc: true
toc_sticky: true
toc_label: "On this page"
toc_icon: "smile"
published: true
---
## 문제점
TF2 Object Detection API를 통해 객체감지 모델을 학습하다보면 Tensorboard 의 로그파일 사이즈가 매우 방대하게 커지는 경우가 있다. 수십 GB에서 
많게는 수백 GB까지 커지게 되고, 텐서보드 로그를 계속 가지고 모델 간의 결과 비교를 해야하는데 용량이 너무 크면 난감하기 그지없다. 
TF2로 객체감지 모델을 학습 시에 텐서보드 로그의 기록 주기는 기본적으로 1 step이다. 적게는 10만 steps에서 100만 steps 까지 학습이 완료되면 
로그 파일의 용량은 불러 오는 것도 오래걸릴 수 있다.



## TF 1과 비교
TF 1과 Object Detection API가 사용하는 코드가 다르다. Tensorflow Github의 models Repo에서 [1.13 브랜치](https://github.com/tensorflow/models/tree/r1.13.0 )를
내려받고 `model_main.py` 코드로 학습한다. 해당 브랜치 디렉토리에서는 `model_main.py`의 [estimator config 부분](https://github.com/tensorflow/models/blob/r1.13.0/research/object_detection/model_main.py#L62 )만
수정해주면 된다.  
```
config = tf.estimator.RunConfig(model_dir=FLAGS.model_dir)
```
해당 부분을 사용자의 필요에 맞게 수정하면 된다. 

### 사용 예
```python
config = tf.estimator.RunConfig(model_dir=FLAGS.model_dir,
                                save_summary_steps=500,
                                save_checkpoints_steps=5000,
                                save_checkpoints_secs=None,
                                keep_checkpoint_max=50,
                                log_step_count_steps=500)
```
`estimator.RunConfig` 클래스의 `save_summary_steps` 파라미터 기본값은 100이었고 이 것을 500으로 바꿔주었다. 나머지 파라미터는 여기서 서술하진 않는다.
[TF1 로그 조절]() 참고
<!--- `save_summary_steps`와 `log_step_count_steps` 파라미터를 통해서 `tf.summary` 즉, 텐서보드에 기록하는 빈도를 조절할 수 있다. 로그 이벤트
파일이 작은 빈도로 기록되면 용량이 방대해 질 수 있는데 이를 막기 위함이다.  
- `save_checkpoints_steps`와 `save_checkpoint_secs` 는 각각 체크포인트를 몇 스텝마다 혹은 몇 초마다 기록하겠다 하는 파라미터이다. 둘 중에
하나만 지정할 수 있으며 만약 둘 다 값을 지정할 시 오류가 발생한다. 
- `keep_checkpoint_max` 는 체크포인트를 저장할 최대 개수이다. `10`으로 지정한다면 10개가 생긴 이후 다음에 저장되는 체크포인트는 가장 오래된 
체크포인트를 지우고 저장된다. 

TF 2는 `PYTHONPATH` 관련해서 이용하는 파이썬 패키지의 경로에 유의해야한다. 아래를 참고하자.-->  

## TF 2의 경우 
[TF2 Object Detection API 설치](https://dongle94.github.io/tensorflow/tf2-obj-detection-api-setting )의 방식으로 TF 2의
Object Detection API를 설치했다면 `pip list`의 결과 모듈 중에 `object-detectio (version: 0.1)`이 설치되어있다. 
```shell
$ pip list

> ...
  object-detection      0.1
  ...
```
이는 `models/research/object_detection` 이 파이썬 패키지 형태로 환경에 설치된 것이다. [PYTHONPATH 환경변수 설정](https://dongle94.github.io/tensorflow/tf2-obj-detection-api-setting/#pythonpath-환경변수-설정 )
을 했다면 `model_main_tf2.py` 는 `object-detection` 모듈이 아닌 내려받은 브랜치를 참조하게 되고 우리가 수정할 코드가 정상적으로 적용된다.

수정 방식은 `models/research/object_detection/model_lib_v2.py`를 수정해서 텐서보드 로그 파일의 출력 빈도를 조정할 수 있다.
`global_step` 변수가 사용되는 부분을 이용해 출력의 빈도를 조정하면 된다. 기존은 매 step 마다 이벤트 로그가 기록되도록 설정되어 있다.
이에 대해 수정할 부분은 다음과 같다.  

### 코드 수정 내용
`models/research/object_detection/model_lib_v2.py` 에서 `tf.compat.v2.summary.record_if` 이 있는 부분을 수정해주면 된다.
수정 내용은 아래와 같다.
  - [line 558](https://github.com/tensorflow/models/blob/master/research/object_detection/model_lib_v2.py#L558)  
    ```python
    with tf.compat.v2.summary.record_if(
      lambda: global_step % 500 == 0): # num_steps_per_iteration only
        # Load a fine-tuning checkpoint.
        if train_config.fine_tune_checkpoint:
    ```
    - `tf.compat.v2.summary`가 `global_step` 500번 마다 기록하도록 `record_if`의 람다 조건을 수정한다.
    - 기존은 `num_steps_per_iteration` 으로 나눈 나머지가 0일 때마다 기록하는데 해당 변수의 값은 TPU 학습 옵션이 켜져있지 않다면 `1`이다. 즉,
    매 step마다 로그를 기록한다.
    
## 결론 
위와 같은 수정을 마치고 TF2 Object Detection API로 다시 학습을 실행해보면 실제로 텐서보드 로그가 기록되는 `train` 디렉토리 용량이 기존에 비해 상당이 작게 나오는 것을
알 수 있다. 너무 적은 빈도로 텐서보드 로그를 찍으면 오히려 학습의 경과를 정확히 파악하지 못할 수도 있다. 본인의 모델의 학습 steps 수에 맞게 
학습의 경과를 적당히 파악할 수 있는 것이 중요하다.