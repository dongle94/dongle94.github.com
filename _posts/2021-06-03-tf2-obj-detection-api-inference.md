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
published: true
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

------
## Wrapped Graph(Frozen Graph) Inference
`saved_model`까지 추출한 그래프 모델에서 `signatures`를 이용해 그래프 펑션을 뽑아내었고 해당 그래프를 `convert_variables_to_constant_v2()`
함수를 이용해서 frozen 한 형상으로 만들어주었다. 해당 인스턴스는 우리가 `tf.io.write_graph` 함수를 사용해서 확장자가 `.pb` 인 파일로 만들어주었다. 

구조는 다음과 같다
```
.
├── test_image.jpg
└── tf2_frozen_inference_graph.pb
```

해당 모델을 가지고 추론을 수행하는 방식에는 wrapped 된 그래프 모델을 `tf.compat.v1` 라이브러리를 일부 이용해서 동결된 그래프를 
`v1.wrap_function`를 사용해 `concrete_function`으로 변환한다. 이 방식은 `tf1`의 frozen_graph에도 일단은 적용이 가능하다.

단, 좀 까다로운 점은 input과 output 노드 이름을 정확히 알아야한다. 여기가 많이 까다롭다. 전 포스팅에서 `frozen_func` 인스턴스를 가지고
아래 코드를 돌린다. 혹은 기존 saved model을 로드한 `model` 인스턴스로 확인해서 output 노드의 이름을 찾아낸다. 
```python
print(frozen_func.outputs)
print(model.signatures['serving_default'].outputs)
```

어느정도 삽질을 한 끝에 아래와 같이 output들이 매칭된다는 것을 알아내었다.
```python
'Identity_1:0' - detection_boxes   
'Identity_2:0' - detection_classes
'Identity_4:0' - detection_scores  
'Identity_5:0' - num_detections
```
코드를 돌려서 일단은 
### Code

1. Import Library
```python
import numpy as np
import tensorflow as tf
import skimage.io
```

2. unwrapp function 정의
```python
def wrap_frozen_graph(graph_def, inputs, outputs):
    def _imports_graph_def():
        tf.compat.v1.import_graph_def(graph_def, name="")

    wrapped_import = tf.compat.v1.wrap_function(_imports_graph_def, [])
    import_graph = wrapped_import.graph

    return wrapped_import.prune(
        tf.nest.map_structure(import_graph.as_graph_element, inputs),
        tf.nest.map_structure(import_graph.as_graph_element, outputs))
```
 
3. Model Load
```python
with tf.io.gfile.GFile(args.input_graph, "rb") as f:
    graph_def = tf.compat.v1.GraphDef()
    loaded = graph_def.ParseFromString(f.read())

frozen_func = wrap_frozen_graph(graph_def=graph_def,
                                inputs=["input_tensor:0"],
                                outputs=["Identity_1:0", "Identity_2:0",
                                         "Identity_4:0", "Identity_5:0"])
```

4. Input load & inference
```python
image = skimage.io.imread(args.image_file, as_gray=False)
image_np_expanded = np.expand_dims(image, axis=0)

if args.input_type == "uint":
    input_type = tf.uint8
elif args.input_type == "float":
    input_type = tf.float32
output = frozen_func(tf.convert_to_tensor(image_np_expanded, dtype=input_type))
```
`saved model` 을 추출할 때 input의 데이터 타입을 정해준 것에 따라 `frozen_func`에 들어가는 텐서의 데이터타입을 맞춰줘야한다

5. Post Processing
```python
boxes = np.squeeze(output[0])
classes = np.squeeze(output[1])
scores = np.squeeze(output[2])
total_count = int(output[3])
```

어느정도 중간에 삽질 및 어려움을 겪을 구간이 보이지만 한번 잘 설정해놓으면 코드의 효율적 재사용을 할 수 있도록 할 수 있다.

> 참고, [텐서플로 TF1에서 TF2로 이전 도큐먼트](https://www.tensorflow.org/guide/migrate?hl=ko#graphpb_%EB%98%90%EB%8A%94_graphpbtxt)