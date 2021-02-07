---
title: "Layout: Excerpt (Separator, Script)"
excerpt_separator: "<!--more-->"
categories:
  - Jekyll
tags:
  - content
  - excerpt
  - layout
header:
  overlay_color: "#555"
  caption: "Manual Docunment"
comments: false
hidden: true
---

해당 내용은 게시글 미리보기에 대한 설정이다. 실제로 지금 이 문단만 미리보기에 보여진다.현재는 `excerpt_separator`를 이용해 미리보기를 설정 중이다.

<!--more-->

## Excerpt Separator
2가지를 이용가능한데 먼저 `excerpt_separator`를 살펴본다. 해당 포스팅의 `yaml` 부분에 아래 내용을 추가한다. 실제로 포스팅을 작성하는 마크다운 내에 `<!--more-->` 을 명시한 부분 상단까지가 미리보기로 보여진다.
```yaml
excerpt_separator: "<!--more-->"
을 적은 후
<!--more-->
를 사용합니다.
```

## Excerpt
내용을 직접 입력하여 미리보기를 설정하려면 아래와 같이 사용할 수 있다.
```yaml
excerpt: "실제 포스팅에는 해당 내용이 미리보기로 게시글 목록에서 보여집니다."
```
위와 같이 포스팅의 `yaml`에 작성하면 해당 글 내용만 미리보기로 제시되고 게시글 본론은 보여지지 않는다.