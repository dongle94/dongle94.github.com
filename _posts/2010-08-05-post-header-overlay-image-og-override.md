---
title: "Post: Teaser, Overlay Image with OpenGraph Override,"
header:
  teaser: /assets/images/page-header-teaser.png
  overlay_image: /assets/images/unsplash-image-1.jpg
  og_image: /assets/images/profile.png
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
  actions:
    - label: "Learn more"
      url: "https://unsplash.com"
categories:
  - Manual
tags:
  - edge case
  - image
  - layout
last_modified_at: 2017-10-26T15:12:19-04:00
comments: false
hidden: true
---

This post has a header image with an OpenGraph override.

```yaml
header:
  overlay_image: /assets/images/unsplash-image-1.jpg
  og_image: /assets/images/page-header-og-image.png
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
  actions:
    - label: "Learn more"
      url: "https://unsplash.com"
```

그냥 제목텍스트를 오버레이 시키지 않은 채 헤더 이미지만 주려면 아래와 같이 적는다.
```yaml
header:
  image: /assets/images/page-header-image.png
  og_image: /assets/images/page-header-og-image.png
```

추가적으로 related post(하단부 추천 게시글)에 나오는 티져 이미지는 같은 header에 아래 내용을 추가함으로써 적용할 수 있다.
```yaml
header:
  teaser: /assets/images/page-header-teaser.png
```

일반적인 게시글은 `overlay_image`, `teaser`, `excerpt` 를 적용시켜서
  1. 게시글 미리 보이는 텍스트 부분 정리
  2. 게시글 본문 내의 제목 꾸미기
  3. 게시글 본문 내에서 오버레이 이미지에 적히는 텍스트 조절
  4. 타 게시글에서 티저 이미지 출력  
  
까지 연출한다.