---
title: "[TensorFlow 2] Object Detection API tensorboard "
date: 2020-09-19
categories:
  - TensorFlow 2
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
published: false
---

## Tensorboard 이벤트 로그 크기 조절
지금까지의 방식으로 학습을 진행하면 training에 대한 tensorboard 이벤트 로그의 용량이 방대하게 커지는 경우가 있다. 앞선 포스팅의 방식으로 TF 2의
Object Detection API를 설치했다면 자신의 설치된 `PIP` 모듈 중에 `object-detectio (version: 0.1)`이 설치되어있다. 실제로 코드의 흐름을
따라가면 우리의 `model_main_tf2.py`를 실행 후 내부의 `import` 문이 site-package에 설치
된 `object-detection` 모듈을 참조하게 된다.

기존 방식은 `models/research/object_detection/model_lib_v2.py`를 수정해서 텐서보드 로그 파일의 출력 빈도를 조정할 수 있을 것이라 생각했는데
파이썬 패키지 형태로 설치 됨으로써 내부 모듈을 수정해줘야한다. `global_step` 변수를 이용해 출력의 빈도를 조정할 수 있다. 수정할 부분은 다음과 같다. 
- `model_lib_v2.py` 에서
  - line 258
  - line 269
  - line 553  