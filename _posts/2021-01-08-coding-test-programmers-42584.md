---
title: "[programmers]프로그래머스 고득점 kit 스택/큐 - 주식가격(python)"
date: 2021-01-08
excerpt: "프로그래머스 스택/큐 파트 '주식가격' 문제 접근 및 파이썬 풀이"
categories:
  - Coding Test
tags:
  - Coding Test
  - Programmers
  - algorithms
  - data structure
  - stack
  - queue
  - python
header:
  #teaser: /assets/images/logo/tensorflow_1.png
  overlay_image: /assets/images/logo/programmers-logo-wide.png
  og_image: /assets/images/profile.png
toc: true
toc_sticky: true
toc_label: "On this page"
toc_icon: "smile"
published: true
classes: wide
---
## 문제
문제링크: [주식가격](https://programmers.co.kr/learn/courses/30/lessons/42584)
![](/assets/images/coding-test/42584/problem.png){: width="100%" }

- INPUT: 초 단위로 기록한 주식 가격(`prices`)
- OUTPUT: 가격이 떨어지지 않은 시간, 리스트 형식으로
- 유의 점:
  - 바로 다음 시간에 가격이 떨어지는 주식은 1초간 가격이 떨어지지 않은 것으로 본다.
  - 마지막 시점의 주식은 가격이 떨어지지 않은 0초로 본다.


## 접근
1. `prices`의 요소를 앞에서 부터 하나 씩 순회한다.
2. 루프 내에서 현재의 주식 가격과 미래의 주식을 하나씩 비교해가며 가격의 하락 여부를 판단한다. -> 리스트에 추가할 변수 생성(주식이 떨어지지 않은 시간)
3. 바로 다음 날 주식가격이 떨어져도 주식이 떨어지지 않은 시간은 1임에 유의하며 **2.**에서 만드는 변수를 1부터 시작한다.
4. 마지막 날 이라면 **2.**에서 만드는 변수에 0이 들어간다.

### 코드구현
```python
def solution(prices):
  answer = []
  for index in range(len(prices)):
    price = prices[index]
    good_day = 0
    for other_day in range(index+1, len(prices)):
      good_day += 1   
      if price > prices[other_day]:
        break
    answer.append(good_day)
  return answer
```
### 결과 및 분석
![](/assets/images/coding-test/42584/try.png){: width="60%" }  

앞선 접근에서의 마지막 날의 경우는 두번째 `for`문에 걸리지 않기에 선언해준 `good_day`가 0의 값을 가진 채로 바로 `answer`에 들어간다.

> 출처: 프로그래머스 코딩 테스트 연습, https://programmers.co.kr/learn/challenges