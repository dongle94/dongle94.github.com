---
title: "[TensorFlow 2] Object Detection API 모델로 추론하기"
date: 2021-06-03
last_modified_at: 2021-06-03
excerpt: "TF 2 Object Detection API Saved Model과 Wrapped Graph를 inference 하는 방법에 대해 알아본다."
categories:
  - TensorFlow
tags:
  - TensorFlow
  - TensorFlow 2
  - Object Detection
  - Inference
  - TF API
  - Saved Model
  - frozen function
header:
  teaser: /assets/images/logo/tensorflow-logo.png
  overlay_image: /assets/images/logo/tensorflow-logo-wide.png
  og_image: /assets/images/profile.png
toc: true
toc_sticky: true
toc_label: "On this page"
toc_icon: "smile"
published: false
---

## 도입
지난 포스팅 [Object Detection API 학습모델 추출하기](https://dongle94.github.io/tensorflow/tf2-obj-detection-api-export/) 에서 두 가지
TF2로 학습한 모델을 Saved Model과 Wrapped Graph로 추출하는 작업을 했었다. 이 모델을 이제 우리가 원하는 Object Detection 모델로 학습했는지 
추론을 실행해서 성능을 확인해봐야한다.

## Saved Model Inference
Saved Model 을 저장하고 나면 생성된 모델의 구조는 다음과 같다. 예시로 든 모델은 [Efificient Det D0 512x512](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/tf2_detection_zoo.md)
모델이다. 뭐 이름만 빌려서 표현한 것이고 실제 내용물은 관계없다.
```
.
├── test_image.jpg
└── Efficient_det_d0_210603
    ├── checkpoint
    │   ├── checkpoint
    │   ├── ckpt-0.data-00000-of-00001
    │   └── ckpt-0.index
    ├── pipeline.config
    └── saved_model
        ├── assets
        ├── saved_model.pb
        └── variables
            ├── variables.data-00000-of-00001
            └── variables.index

```

실제 저 해당 트리구조에서 우리가 추론에 사용하기 위해 필요한 경로는 `saved_model` 디렉토리이다. `TF2`가 `Keras`의 API에서 상당히 많은 것을
적용함을 이해한다면 코드를 쉽게 짤 수 있다.

### Code
1. Import Library
```python
import numpy as np
import tensorflow as tf
import skimage.io
```

1. Model Load
```python
model_path = './Efficient_det_d0_210603/saved_model'

loaded_model = tf.saved model(model_path)
```
2. Prepare Input data
```python
IMAGE_PATH = './test_image.jpg'
image = skimage.io.imread(IMAGE_PATH, as_gray=False)
image_expanded = np.expand_dims(image, axis=0)
```
3. Inference
```python
output = loaded_model(image_np_expanded)
```

4. Post Process
```python
boxes = np.squeeze(output['detection_boxes'])
scores = np.squeeze(output['detection_scores'])
classes = np.squeeze(output['detection_classes'])
total_count = int(output['num_detections'][0])
```

여기까지 하면 최종적으로 출력된 `boxes`, `scores`, `classes`, `total_count`는 `TF1`의 그것과 동일하게 보인다.


## Wrapped Graph Inference
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