---
title: "Layout: Excerpt (Generated)"
excerpt_separator: "<!--more-->"
categories:
  - Layout
  - Uncategorized
tags:
  - content
  - excerpt
  - layout
---

해당 내용은 보여지는 부분입니다. excerpt_separator 를 이용하여 게시글 미리보기에서 보여지는 부분을 조정할 수 있습니다.

<!--more-->
총 2가지를 적용할 수 있습니다.
해당 게시글은 `excerpt_separator`를 이용하였습니다. 해당 게시글의 meta 부분에 
```yaml
excerpt_separator: "<!--more-->"
을 적은 후
<!--more-->
를 사용합니다.
```

이렇게도 표현할 수 있습니다.
```yaml
excerpt: "실제 포스팅에는 해당 내용이 미리보기로 게시글 목록에서 보여집니다."
```
위와 같이 meta 데이터에 적용하면 해당 글 내용만 미리보기로 제시되고 게시글 본론은 보여지지 않습니다.