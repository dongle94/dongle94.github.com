---
title: "[programmers]프로그래머스 고득점 kit 해시 - 전화번호(Python)"
date: 2021-02-14
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
문제링크: [전화번호](https://programmers.co.kr/learn/courses/30/lessons/42577)
![](/assets/images/coding-test/42577/problem.png){: width="100%" }

- INPUT: 각 사람의 전화번호가 문자열 형식(str)로 적힌 리스트(list) `phone_book`
- OUTPUT: 한 전화번호가 다른 전화번호의 접두어로 존재하는 지의 여부
- 예시:
  - `119`는 `11955224421`의 접두어가 맞다.
  - `123`는 `15820123771`에 포함은 되어있으나 접두어는 아니다.


## 첫 번째 접근
1. `phone_book`의 요소들을 순차적으로 접근(loop)
2. 접근 중인 번호를 제외한 나머지 번호들로 임시 전화번호부를 만든다.
3. 첫 루프 속에서 임시 전화번호부를 순회(loop)
4. 임시 전화번호부의 번호들이 첫 루프의 번호로 시작하는지를 체크한다.

### 코드구현
```python
def solution(phone_book):
    for index in range(len(phone_book)):
        tmp_book = phone_book.copy()
        tmp_book.remove(phone_book[index])
        for number in tmp_book:
            if number.startswith(phone_book[index]):
                return False
    return True
```
### 결과 및 분석
![](/assets/images/coding-test/42577/try1.png){: width="60%" }  
 
구현한 코드의 시간복잡도를 생각했을 때 최악의 경우에 `O(N^2)`라고 생각했다. `n`개를 `n-1`개의 원소가 있는 리스트를 만들어 일일히 비교하니 접두어에 해당하는 원소들이 없을 경우 결국 `n^2`개를 꼬박 다 세는 경우라고 생각했다. 그러나 일단은 효율성 테스트를 통과한 모습.

조금 더 효율적인 것이 없을까 생각해보고 두번째 접근.

## 두 번째 접근
1. `phone_book`의 요소들을 정수 형식으로 정렬
2. 가장 낮은 인덱스부터 나머지 뒤로만 접근해서 접두어 여부인지 확인

* n개의 번호가 있다했을 때 O(N^2)로 첫 번째 접근과 동일하다. 실제로는 N(N-1)/2지만 큰 차이는 없는 듯 하다.

### 코드구현
```python
def solution(phone_book):
    phone_book = [int(phone_number) for phone_number in phone_book]
    phone_book.sort()
    for cur in range(len(phone_book)):
        for compare in range(cur+1,len(phone_book)):
            if str(phone_book[compare]).startswith(str(phone_book[cur])):
                return False
    return True
```

### 결과 및 분석
![](/assets/images/coding-test/42577/try2.png){: width="60%" }  

이유는 알 수 없으나 랜덤접근이라 생각했던 첫 번째 접근보다 시간이 더 안나왔다. 아마 정렬 된 리스트를 만드는 부분에 시간이 좀 더 사용 된 듯 싶지만 `O(N^2)` 보다 좋아야 한다고 생각했는데 `O(NlogN)` 정도의 알고리즘은 구현하지 못하였다.

> 출처: 프로그래머스 코딩 테스트 연습, https://programmers.co.kr/learn/challenges