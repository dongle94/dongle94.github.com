---
title: "[programmers]프로그래머스 고득점 kit 해시 - 완주하지 못한 선수(python)"
date: 2021-02-07
excerpt: "프로그래머스 Hash 파트 '완주하지 못한 선수' 문제 접근 및 파이썬 풀이"
categories:
  - Coding Test
tags:
  - Coding Test
  - Programmers
  - algorithms
  - data structure
  - hash
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
문제링크: [완주하지 못한 선수](https://programmers.co.kr/learn/courses/30/lessons/42576)
![](/assets/images/coding-test/42576/problem.png){: width="100%" }

- INPUT: 리스트 형식의 마라톤 참여 선수 이름들(`participant`), 리스트 형식의 마라톤 완주 선수 이름들(`completion`)
- OUTPUT: 마라톤을 완주하지 못한 **1명**의 이름
- 유의 점:
  - 참여 선수 이름은 동명이인이 있을 수 있다.
  - 완주하지 못한 선수는 1명이다.


## 첫 번째 시도(실패)
1. `completion`이 `participant` 보다 크기가 1 작으므로 `completion`의 요소들을 하나 순회하며 `participant` 리스트에서 제거해준다.
2. `participant` 리스트에는 원소가 1개 남을 것이고 그것이 정답이다.

### 코드구현
```python
def solution(participant, completion):
  for person in completion:
    participant.remove(person)
  return participant[0]
```
### 결과 및 분석
![](/assets/images/coding-test/42576/try1.png){: width="60%" }  

제출에서 시간초과가 뜬 모습.  
구현한 코드의 시간복잡도를 생각했을 때 `for` 문 자체는 `O(N)`라고 확신했고 `participant.remove(person)`도 `O(N)`라고 추측하였다. 왜냐하면 리스트에서 원소를 지우기 위한 알고리즘이 내부적으로 순차검색으로 짜여있지 않을까 했기 때문이다.

그래서 내가 생각한 나의 코드의 시간복잡도는 `O(N^2)` 이고 아마 문제가 요구하는 정답은 `O(NlogN)`은 되어야하는가 싶었다.

## 두 번째 시도(정답)
1. 입력으로 주어진 두개의 리스트를 정렬한다. 
  - 파이썬의 내장함수 `sorted()` 혹은 리스트 객체의 정렬 메소드인 `.sort()`는 정렬 알고리즘의 시간복잡도가 `O(NlogN)`을 쓸거라 생각
2. `completion`의 크기만큼 두 리스트를 순차적으로 같은 인덱스의 원소를 비교해가며 동일한지를 판단.
3. 다른 부분이 나오면 완주하지 못한 `participant` 이름 리턴
4. `for` 문이 끝났다면 `participant` 리스트의 가장 마지막에 있는 사람 일 것이다.

### 코드구현
```python
def solution(participant, completion):
    participant.sort()
    completion.sort()
    for index in range(len(completion)):
        if participant[index] != completion[index]:
            return participant[index]      
    return participant[-1]
```

### 결과 및 분석
![](/assets/images/coding-test/42576/try2.png){: width="60%" }  

정렬 문에서 `O(NlogN)`의 시간복잡도로 정렬을 수행하고 `for` 문에서 `O(N)`의 시간복잡도로 일치 여부를 판단해서 최종 `O(NlogN)`의 효율성으로 통과한 듯 싶다.

> 출처: 프로그래머스 코딩 테스트 연습, https://programmers.co.kr/learn/challenges