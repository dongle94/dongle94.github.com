---
title: "Post: Overlay Image with OpenGraph Override"
header:
  overlay_image: /assets/images/unsplash-image-1.jpg
  og_image: /assets/images/page-header-og-image.png
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
  actions:
    - label: "Learn more"
      url: "https://unsplash.com"
categories:
  - Layout
  - Uncategorized
tags:
  - edge case
  - image
  - layout
last_modified_at: 2017-10-26T15:12:19-04:00
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