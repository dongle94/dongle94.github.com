---
title: "[TensorFlow 2] Object Detection API 학습 모델 추출하기"
date: 2020-10-05
last_modified_at: 2021-01-24
excerpt: "TF 2 Object Detection API 학습 된 체크포인트 saved model & frozen graph 추출"
categories:
  - TensorFlow
tags:
  - TensorFlow
  - TensorFlow 2
  - Object Detection
  - Export
  - TF API
  - Saved Model
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

## 도입
TF 2는 TF 1의 형상과는 다르게 공식적으로 Checkpoint / Saved Model / Frozen Graph 구조를 제공하지는 않는다. 약간의 다른 코드들을 조합해서 하나의 고정된 pb파일의 형태로 Frozen Graph의 형태처럼 변환은 가능하다. 또한 TF2는 내부적으로 `Keras` 라이브러리의 도입을 통해 .`h5` 파일 형식으로 네트워크 모델을 저장하지만 Object Detection API는 `Estimator`를 통해 학습을 하기 때문에 `h5`는 지원하지 않는다.

## Checkpoint to Saved Model
체크포인트를 Saved Model로 변환하기 위해 `research/object_detection` 경로 안에 있는 `exporter_main_v2.py` 모듈을 사용한다. 파라미터 설명을 위해 `--help` 키워드와 함께 먼저 실행해보거 다음과 같은 파라미터들을 입력해준다.
```python
$ python exporter_main_v2.py --help
```
`config_override`, `input_type`, `output_directory`, `pipeline_config_path`, `trained_checkpoint_dir` 이렇게 5가지의 파라미터를 요구함을 알 수 있고, 우리가 지정해줘야 할 부분은 다음과 같다.
- `input_type` : Object Detection 네트워크 모델의 입력 타입을 지정해준다. [`image_tensor`, `encoded_image_string_tensor`,
 `tf_example`, `float_image_tensor`] 중에 입력하는데 우리의 학습 모델은 대부분 `image_tensor` 혹은 `float_image_tensor`를 사용,
  입력한다. 두개의 차이는 모델에 입력 이미지를 넣어줄 때 이미지 배열의 데이터 타입이 실수형인지, 8비트 unsigned int형인지에 대한 차이이다. 일반적으로는 `image_tensor`로 사용한다.
  실수 형의 입력을 받는 `float_image_tensor`는 `uint8`을 데이터 형식을 지원안하는 최적화 라이브러리 등을 위해 존재하는 것 같다. ex. `TensorRT` 
### Parameters
- `pipeline_config_path` : 네트워크 모델 학습 시에 입력해줬던 `pipeline.config` 파일의 경로를 입력해준다.
- `trained_checkpoint_dir` : 네트워크 모델이 학습되면서 체크포인트 파일들이 저장되어 있는 경로에 보면 `checkpoint`라 명명 된 파일이 존재한다. 해당 파일을 텍스트 에디터로 열어보면 `model_checkpoint_path` 등의 내부 체크포인트 관련 항목들이 있다. 해당 파일이 존재하는 상위 디렉토리 경로를 적어준다.
- `output_directory` : Saved Model이 저장될 경로를 지정한다. 해당 경로에는 model을 zoo에서 처음 받아왔을 때 처럼 `checkpoint`, `saved_model/`, `pipeline.config`가 저장된다. Export 완료 시의 `checkpoint/`에 있는 체크포인트들은 실제로 `trained_checkpoint_dir`에 입력해준 경로에 있는 `model_checkpoint_path`에 있는 체크포인트들이다.   

실행코드는 다음과 같다.
```python
# current_dir: /tensorflow/mdoels/research/object_detection/
$ python exporter_main_v2.py \
  --input_type=image_tensor | float_image_tensor \
  --pipeline_config_path=<path>/<to>/<your>/<trained_model>/pineline.config \
  --trained_checkpoint_dir=<path>/<to>/<your>/<trained_model> \
  --output_directory=<path>/<to>/<your>/<trained_model>/saved/
```

코드가 문제없이 정상적으로 실행되었다면 `--output_directory`로 지정해놓은 경로 내부에 `saved_model` 디렉토리가 생기며, 해당 디렉토리 안에 `saved_model.pb`를 확인할 수 있다. inference 시에는 `saved_model` 디렉토리 단위로 파라미터를 지정해야한다. inference 관련 된 부분은 다른 포스팅에서 다룬다.  


## Checkpoint to Frozen Graph
Frozen Graph 형태의 Export는 위의 `Saved Model`을 Export하는 작업이 선행되어야한다. Frozen Graph는 Network를 구성하는 Weights를 상수화하여 고정시키고 Network Architectrue와
Weights를 하나의 파일 안에 합쳐놓은 형상이다. 방식은 다음과 같다.
```
saved model 불러오기 -> 네트워크 Inference 함수 추출 -> 고정 그래프 변환 -> 파일로 저장
```
inference에 대한 부분은 다른 포스팅에서 다룰 것이니 변환 코드만 언급한다. 해당 내용은 세부 모듈은 스택 오버플로우나 Github issue 등에서 코드들을 모아 작성한 것이다. 텐서플로우에서 TF2에 대해 고정된 그래프 작성법을 따로 메뉴얼을 제공하지는 않는 듯 하다. 

### Saved Model to Frozen Graph
```python
from tensorflow.python.framework.convert_to_constants import convert_variables_to_constants_v2

dirname = './tf2_frozen_graph_dir/'
filename = 'tf2_frozen_inference_graph.pb'

model = tf.saved_model.load(saved_input_dir)
graph_func = model.signatures['serving_default']
frozen_func = convert_variables_to_constants_v2(graph_func)
frozen_func.graph.as_graph_def()

tf.io.write_graph(graph_or_graph_def=frozen_func.graph,
                  logdir=dirname,
                  name=filename,
                  as_text=True)
```
1. 우선 `saved model` 저장 후 `tf.saved_model.load()`를 이용하여 모델을 불러온다.
2. 네트워크 모델의 시그니쳐 키를 이용하여 그래프 함수를 추출한다.
3. `convert_variables_to_constants_v2` 모듈을 이용해서 고정된 그래프로 변환한다. 
4. graph를 graph_def로 변환 및 `tf.io.write_graph()`를 통해서 frozen graph를 저장한다. 
5. `tf.io.write_graph()` 메소드 파라미터 중 `logdir`엔 Output이 저장될 경로의 디렉토리를 `name`에는 파일 명을 적어준다. 

여기서 유의할 점은 모델의 시그니쳐 키인 `serving_default`는 기본값이다. 네트워크 모델을 학습 시에 모델의 시그니쳐 키를 수정하거나 변경했다면해당 값을 찾아서 넣어주어야 한다. 또한 이렇게 생성된 `pb` 파일은 inference 시에 input과 output 레이어 명을 파라미터로 wrapping 함수를 적용해주어야 한다. Inference 관련 포스팅에서 자세한 설명을 이어간다.