---
title: "[TensorFlow 2] Object Detection API 학습 모델 추출하기"
date: 2020-10-05
excerpt: "TF 2 Object Detection API 학습 된 체크포인트 saved model & frozen graph 추출"
categories:
  - TensorFlow
tags:
  - TensorFlow
  - TensorFlow 2
  - Object Detection
  - Export
  - TF API
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
TF 2는 TF 1의 형상과는 다르게 Checkpoint / Saved Model / Frozen Graph 구조는 아니다. Frozen Graph를 TF 2안에 있는 v1 모듈을 이용해서 
변환은 가능하다. 공식적으로는 `Keras` 라이브러리의 도입과 더불어 functional 한 saved model의 구조를 지향한다. 학습 시킨 체크포인트들을 객체 감지 
추론에 사용하기 위해서는 saved model 혹은 frozen graph의 형태로 바꿔줘야한다. 

## Checkpoint to Saved Model
체크포인트를 Saved Model로 변환하기 위해 `research/object_detection` 경로 안에 있는 `exporter_main_v2.py` 모듈을 사용한다. 실행 시의
파라미터 사용을 보기 위해 `--help` 키워드와 함께 먼저 실행해보면 다음 파라미터들을 줘야함을 알 수 있다.
```python
$ python exporter_main_v2.py --help
```
해당 커맨드를 입력하면 `config_override`, `input_type`, `output_directory`, `pipeline_config_path`, `trained_checkpoint_dir`
이렇게 5가지의 파라미터를 요구함을 알 수 있고, 우리가 지정해줘야 할 부분은 다음과 같다.
- `input_type` : Object Detection 네트워크 모델의 입력 타입을 지정해준다. [`image_tensor`, `encoded_image_string_tensor`,
 `tf_example`, `float_image_tensor`] 중에 입력해 줄 수 있는데 우리의 학습 모델은 대부분 image_tensor 혹은 float_image_tensor를 사용,
  입력한다. 두개의 차이는 모델에 입력 이미지를 넣어줄 때 이미지 배열의 데이터 타입이 실수형인지, 8비트 unsigned int형인지에 대한 차이이다.
  실수 형의 입력을 받는 `float_image_tensor`는 아마 `TensorRT` 같은 하드웨어 가속화 라이브러리 등에서 `uint8`을 지원이 안됨을 염두에 둔 듯하다.
- `pipeline_config_path` : 네트워크 모델 학습 시에 입력해줬던 `pipeline.config` 파일의 경로를 입력해준다.
- `trained_checkpoint_dir` : 네트워크 모델이 학습되면서 체크포인트 파일들이 저장되어 있는 경로에 보면 `checkpoint`라 명명 된 파일이 존재한다.
  해당 파일을 텍스트 에디터로 열어보면 `model_checkpoint_path` 등의 체크포인트 관련 지표들이 있다. 해당 파일이 존재하는 상위 디렉토리까지 적어준다.
- `output_directory` : Export 될 Saved Model이 저장될 경로를 지정한다. 해당 경로에는 model을 zoo에서 처음 받아왔을 때 처럼 `checkpoint`,
`saved_model/`, `pipeline.config`가 저장된다. Export 시의 `checkpoint/`에 있는 체크포인트들은 실제로 `trained_checkpoint_dir`에
입력해준 경로에 있는 `model_checkpoint_path`에 있는 체크포인트들이다.   

실행코드는 다음과 같다.
```python
# current_dir: <path>/<to>/<tensorflow>/research/object_detection/
$ python exporter_main_v2.py \
  --input_type=image_tensor | float_image_tensor \
  --pipeline_config_path=<path>/<to>/<your>/<trained_model>/pineline.config \
  --trained_checkpoint_dir=<path>/<to>/<your>/<trained_model> \
  --output_directory=<path>/<to>/<your>/<trained_model>/saved/
```

코드가 문제없이 정상적으로 실행되었다면 `--output_directory`로 지정해놓은 경로 내부에 `saved_model가 생기며, 해당 디렉토리 안에 saved_model.pb
를 확인할 수 있다. inference 시에는 saved_model 디렉토리 단위로 움직여야한다. inference 관련 된 부분은 다른 포스팅에서 다룬다.  


## Checkpoint to Frozen Graph
Frozen Graph 형태의 Export는 `Saved Model` 형태가 선행되어야한다. Frozen 그래프는 네트워크 구조의 가중치를 상수화하여 고정시키고 모델 구조와
가중치를 하나의 파일 안에 합쳐놓은 형상이다. 방식은 다음과 같다.
```
saved model 불러오기 -> 네트워크 그래프 함수 추출 -> 고정 그래프 변환 -> 파일로 저장
```
inference에 대한 부분은 다른 포스팅에서 다룰 것이니 변환 코드만 언급한다. 해당 내용은 세부 모듈은 스택 오버플로우나 Github issue 등에서 코드 
스니펫을 모아 작성한 것이다. 텐서플로우에서 TF2에 대해 고정된 그래프 작성법을 따로 메뉴얼을 제공하지는 않는 듯 하다. 
```python
from tensorflow.python.framework.convert_to_constants import convert_variables_to_constants_v2

model = tf.saved_model.load(saved_input_dir)
graph_func = model.signatures['serving_default']
frozen_func = convert_variables_to_constants_v2(graph_func)
frozen_func.graph.as_graph_def()

tf.io.write_graph(graph_or_graph_def=frozen_func.graph,
                  logdir=dirname,
                  name=filename,
                  as_text=args.as_text)
```
- 우선 `saved model` 저장 후 `tf.saved_model.load()`를 이용하여 모델을 불러온다.
- 네트워크 모델의 시그니쳐 키를 이용하여 그래프 추론 함수를 추출한다.
- `convert_variables_to_constants_v2` 모듈을 이용해서 고정된 그래프로 변환한다. 
- graph를 graph_def로 변환 및 `tf.io.write_graph()`를 통해서 frozen graph를 저장한다. 

여기서 유의할 점은 모델의 시그니쳐 키인 `serving_default`는 기본값이다. 네트워크 모델을 학습 시에 모델의 시그니쳐 키를 수정하거나 변경했다면
해당 값을 찾아서 넣어주어야 한다. 