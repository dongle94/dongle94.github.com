---
title: "Layout: Header Image (linked & image file)"
header:
  image: https://live.staticflickr.com/8084/8396909762_813a2b1829_h.jpg
categories:
  - Manual
tags:
  - edge case
  - featured image
  - image
  - layout
  - sample post
  - readability
  - test
comments: false
hidden: true
---

This post should display a **header image**, if the theme supports it.

Featured image is an external asset and should load.

헤더이미지에 대한 외부 링크 연결
```yaml
header:
  image: https://live.staticflickr.com/8084/8396909762_813a2b1829_h.jpg
```

정사각형 이미지가 아니라면 스타일문제가 발생할 가능성은 있지만 일단 현재의 테마는 수평이미지를 지원한다.  
아래의 코드로 meta 데이터를 바꾸면 문제없이 적용가능. 해당 이미지의 픽셀 사이즈는 1600x586 이다.
```yaml
header:
  image: /assets/images/unsplash-image-1.jpg
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
```


아래의 이미지로 헤더이미지를 바꾸면 알 수 있겠지만. 헤더이미지의 크기가 클 경우 본문 텍스트 가독성에 방해가 될 수 있다.
`unsplash-image-4.jpg`의 픽셀 사이즈는 1600x869 이다.
```yaml
header:
  image: /assets/images/unsplash-image-4.jpg
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
```

아래의 헤더 이미지는 수직으로 긴 이미지이다. 포스팅의 본문 가독성에 매우 불편함을 줄 수 있다.
해당 이미지의 픽셀 사이즈는 1600x1720 이다.
```yaml
header:
  image: /assets/images/unsplash-image-6.jpg
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
```