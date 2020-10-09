---
title: "Post: Teaser, Overlay Image with OpenGraph Override, etc"
header:
  teaser: /assets/images/page-header-teaser.png
  overlay_image: /assets/images/manual_image/unsplash-image-1.jpg
  og_image: /assets/images/profile.png
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
  actions:
    - label: "Learn more"
      url: "https://unsplash.com"
excerpt: "This post has a header image with an OpenGraph override."
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

이 포스팅은 오버레이 헤더이미지, 티저 이미지, 오픈그래프 이미지를 가지고 있다. 적용 시 아래와 같은 이미지로 헤더가 보인다. 
>```yaml
header:
  overlay_image: /assets/images/manual_image/unsplash-image-1.jpg 
  og_image: /assets/images/page-header-og-image.png    
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
  actions:
    - label: "Learn more"
      url: "https://unsplash.com"
```
![](/assets/images/manual_image/manual_capture/header_overlay_image.jpg)


제목 텍스트를 헤더이미지에 오버레이 시키지 않은 채 헤더 이미지만 주려면 아래와 같이 헤더를 작성한다. 
>```yaml
header:
  image: /assets/images/page-header-image.png        # 헤더 이미지 파일
```
![](/assets/images/manual_image/manual_capture/only_header_image.jpg)

하단부 추천 게시글에 미리보기 이미지로 나오는 것이 **Teaser Image**이다. 티저 이미지는 같은 header에 아래 내용을 추가함으로써 적용할 수 있다.
>```yaml
header:
  teaser: /assets/images/page-header-teaser.png  # 티저 이미지 파일
```
![](/assets/images/manual_image/manual_capture/teaser_image.jpg)

오픈그래프 이미지는 다음과 같이 헤더를 작성하며 링크 미리보기 혹은 링크를 클릭하기 전에 보여질 수 있는 이미지를 담당한다.
>```yaml
header:
  og_image: /assests/image/opengraph_image.png
```
![](/assets/images/manual_image/manual_capture/opengraph_image.jpg)


결론적으로 일반적인 포스팅은 meta data의 **header** 속성의 `overlay_image`, `teaser` 이미지를 적용하고 다른 메뉴얼 문서에 있는  `excerpt`까지 적용시키면 깔끔한 포스팅의 형태를 취할 수 있다.
  - 게시글 미리 보이는 텍스트 부분 정리
  - 게시글 본문 내의 제목 부분 셋팅
  - 게시글 본문 내에서 오버레이 이미지에 적히는 텍스트 조절
  - 타 게시글에서 추천 게시글  목록의 티저 이미지 출력
  - 오픈 그래프 이미지를 통한 링크 미리보기