---
title: "[programmers]프로그래머스 고득점 kit 스택/큐 - 기능개발(Python)"
date: 2021-01-11
excerpt: "프로그래머스 스택/큐 파트 '기능개발' 문제 접근 및 파이썬 풀이"
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
문제링크: [기능개발](https://programmers.co.kr/learn/courses/30/lessons/42586)
![](/assets/images/coding-test/42586/problem.png){: width="100%" }

- INPUT: 각 기능의 개발 진도 현황(`progresses`), 개발 진도현황의 인덱스와 맞는 개발 속도(`speeds`) 
- OUTPUT: 하루에 개발 속도 만큼 각 개발이 이뤄질 때, 한 번에 릴리즈 되는 기능의 갯수 리스트
- 유의 점:
  - 기능의 진도는 100이 되면 완료되어 100에 도달한 당일 날에 배포가 이루어진다.
  - 낮은 인덱스의 기능보다 뒤에 위치한 기능이 먼저 진도가 100이 되더라도 앞 기능이 배포 되지 않는다면 먼저 배포되지 않는다.
  - 배포는 하루에 여러개를 할 수 있다.


## 접근
1. 하루 단위(1번)에 각 인덱스의 `progresses`에 `speeds`를 맞춰 더해준다.
2. `progresses`의 가장 낮은 인덱스부터 기능이 개발 완료되었는지를 검사한다.
3. 완료되었다면 `progresses` 리스트에서 해당 기능과 `speeds`에서도 해당 기능의 개발속도를 제거하고 배포 수를 1 올린다. 
4. `progresses`의 0번 인덱스가 여전히 개발 완료 상태라면 **3번**을 반복해준다.
5. 더 이상 배포가 가능한 기능이 없고 배포할 기능이 있다면 배포한 기능의 수가 0이 아니라면 `answer` 리스트에 추가한다.
6. **1번~5번**을 `progresses`가 남아있는 한 반복한다.

### 코드구현
```python
def solution(progresses, speeds):
  answer = []
  
  while progresses:
    for index, speed in enumerate(speeds):
      progresses[index] += speed

    daily_release = 0
    while progresses and progresses[0] >= 100:
      daily_release += 1
      progresses.pop(0)
      speeds.pop(0)
    if daily_release != 0:
      answer.append(daily_release)
    
    return answer
```
### 결과 및 분석
![](/assets/images/coding-test/42586/try.png){: width="60%" }  

중간에 있는 `while`문의 조건문 중 먼저나오는 `progresses`는 해당 리스트의 마지막 원소가 `pop(0)` 메소드를 통해 빠지더라도 `progresses[0] >= 100`을 검사하기 때문에 `and`의 특성 상 방어 조건으로써 앞에 넣어주었다. 즉, `progresses and`라는 첫 조건이 없다면 마지막 `progresses`의 마지막 원소가 빠지는 순간 `progresses[0]`이 존재하지 않아  `while`문에서 오류를 일으킨다.

> 출처: 프로그래머스 코딩 테스트 연습, https://programmers.co.kr/learn/challenges