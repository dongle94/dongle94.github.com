---
title: "Layout: Reading Time, Comments, Social Sharing Links, and Related Posts Disabled"
read_time: true
comments: false
share: false
related: false
excerpt: "포스팅의 댓글, 공유 등의 부가 기능의 On/Off에 대해서 알아본다."
header:
  overlay_color: "#555"
  caption: "Manual Docunment"
categories:
  - Manual
tags:
  - related posts
  - social
  - comments
  - layout
  - read time
hidden: true
---

본 게시글은 읽기 시간, 댓글, 공유링크, 관련된 게시글 기능을 이용할 수 없다.

읽기 시간, 댓글, SNS 공유, 관련 포스트 링크기능이 존재하지 않는다.

### Comments
첫 번째로 댓글 기능을 on/off조절할 수 있다. 기본값은 `_config.yml` 에서 조정 가능하다. 게시글 별로는 포스팅의 최상단 `yaml`에서 조정 가능하다.  현재 기본값은 `true`, 게시글은 `false` 로 설정되어 있다. 적용 순서는 포스팅 `yaml`에 들어가있는 옵션이 먼저 적용되고 명시되어 있지 않다면 `_config.yml`에 있는 설정이 적용된다. 

```yaml
comments: true
comments: false
``` 

### Reading Time
다음으로 읽기 시간 옵션을 on/off 할 수 있다. 해당. 포스팅의 `yaml`에서 아래와 같이 작성한다.  기본값은 `_config.yml` 에서 조정 가능하다. 현재 기본값은 `false`, 게시글은 `true` 로 적용되어 있다. 적용 순서는 포스팅 `yaml`에 들어가있는 옵션이 먼저 적용되고 명시되어 있지 않다면 `_config.yml`에 있는 설정이 적용된다. 

```yaml
read_time: true
read_time: false
```

### Relative Post
다음으로는 관련 게시글 옵션을 on/off 할 수 있다. 포스트 하단부에 게시글 추천 항목을이 보이는 부분에 대한 것이다.포스팅의 `yaml`에서 아래와 같이 작성한다.
기본값은 `_config.yml` 에서 조정 가능하다. 현재 기본값은 `true`, 게시글은 `false` 로 설정 되어 있다. 적용 순서는 포스팅 `yaml`에 들어가있는 옵션이 먼저 적용되고 명시되어 있지 않다면 `_config.yml`에 있는 설정이 적용된다. 

```yaml
related: true
related: false
```

### Sharing
마지막으로 게시글 공유 옵션을 on/off 할 수 있다. 해당 포스팅을 페이스북. 트위터 등의 SNS로 공유하는 옵션에 대한 것이다. 포스팅의 `yaml`에서 아래와 같이 작성한다. 기본값은 `_config.yml` 에서 조정 가능하다. 현재 기본값은 `true`, 게시글은 `false` 로 설정 되어 있다.적용 순서는 포스팅 `yaml`에 들어가있는 옵션이 먼저 적용되고 명시되어 있지 않다면 `_config.yml`에 있는 설정이 적용된다. 

```yaml
share: true
share: false
```