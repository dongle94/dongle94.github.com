---
title: "Layout: Reading Time, Comments, Social Sharing Links, and Related Posts Disabled"
read_time: true
comments: false
share: false
related: false
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

This post has reading time, comments, social sharing links, and related posts disabled.

Reading time, comments, social sharing and related post links should not appear.

댓글 기능을 on/off조절할 수 있다. meta 정보에서 다음과 같이 작성한다.  
기본값은 `_config.yml` 에서 조정 가능하다. 현재 기본값은 `true`, 게시글은 `false`  
기본으로 설정되어 있는 옵션이 먼저 적용되고 포스팅 meta에 들어가있는 옵션이 덮어 씌워져서 적용된다. 

```yaml
comments: true
comments: false
``` 


읽기 시간 옵션을 on/off 할 수 있다. 게시글 제목 아래에 게시글을 지금까지 읽은 시간 출력 기능이다. meta 정보에서
다음과 같이 작성한다.  
기본값은 `_config.yml` 에서 조정 가능하다. 현재 기본값은 `false`, 게시글은 `true`  
기본으로 설정되어 있는 옵션이 먼저 적용되고 포스팅 meta에 들어가있는 옵션이 덮어 씌워져서 적용된다. 

```yaml
read_time: true
read_time: false
```

관련 게시글 옵션을 on/off 할 수 있다. 포스트 하단에 게시글 추천을 끄고 켤 수 있다. meta 정보에서 다음과 같이 작성한다.
기본값은 `_config.yml` 에서 조정 가능하다. 현재 기본값은 `true`, 게시글은 `false`  
기본으로 설정되어 있는 옵션이 먼저 적용되고 포스팅 meta에 들어가있는 옵션이 덮어 씌워져서 적용된다. 
```yaml
related: true
related: false
```

게시글 공 옵션을 on/off 할 수 있다. meta 정보에서 다음과 같이 작성한다.
기본값은 `_config.yml` 에서 조정 가능하다. 현재 기본값은 `true`, 게시글은 `false`  
기본으로 설정되어 있는 옵션이 먼저 적용되고 포스팅 meta에 들어가있는 옵션이 덮어 씌워져서 적용된다. 
```yaml
share: true
share: false
```