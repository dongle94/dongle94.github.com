---
title: "Post: Gallery"
categories:
  - Manual
tags:
  - gallery
  - Post Formats
  - tiled
comments: false
header:
  overlay_color: "#555"
  caption: "Manual Docunment"
hidden: true
gallery:
  - url: /assets/images/manual_image/unsplash-gallery-image-1.jpg
    image_path: /assets/images/manual_image/unsplash-gallery-image-1-th.jpg
    alt: "placeholder image 1"
    title: "Image 1 title caption"
  - url: /assets/images/manual_image/unsplash-gallery-image-2.jpg
    image_path: /assets/images/manual_image/unsplash-gallery-image-2-th.jpg
    alt: "placeholder image 2"
    title: "Image 2 title caption"
  - url: /assets/images/manual_image/unsplash-gallery-image-3.jpg
    image_path: /assets/images/manual_image/unsplash-gallery-image-3-th.jpg
    alt: "placeholder image 3"
    title: "Image 3 title caption"
  - url: /assets/images/manual_image/unsplash-gallery-image-1.jpg
    image_path: /assets/images/manual_image/unsplash-gallery-image-1-th.jpg
    alt: "placeholder image 4"
    title: "Image 4 title caption"
  - url: /assets/images/manual_image/unsplash-gallery-image-2.jpg
    image_path: /assets/images/manual_image/unsplash-gallery-image-2-th.jpg
    alt: "placeholder image 5"
    title: "Image 5 title caption"
  - url: /assets/images/manual_image/unsplash-gallery-image-3.jpg
    image_path: /assets/images/manual_image/unsplash-gallery-image-3-th.jpg
    alt: "placeholder image 6"
    title: "Image 6 title caption"
  - url: /assets/images/manual_image/unsplash-gallery-image-1.jpg
    image_path: /assets/images/manual_image/unsplash-gallery-image-1-th.jpg
    alt: "placeholder image 7"
    title: "Image 7 title caption"
  - url: /assets/images/manual_image/unsplash-gallery-image-2.jpg
    image_path: /assets/images/manual_image/unsplash-gallery-image-2-th.jpg
    alt: "placeholder image 8"
    title: "Image 8 title caption"
  - url: /assets/images/manual_image/unsplash-gallery-image-3.jpg
    image_path: /assets/images/manual_image/unsplash-gallery-image-3-th.jpg
    alt: "placeholder image 9"
    title: "Image 9 title caption"
  - url: /assets/images/manual_image/unsplash-gallery-image-1.jpg
    image_path: /assets/images/manual_image/unsplash-gallery-image-1-th.jpg
    alt: "placeholder image 10"
    title: "Image 10 title caption"
  - url: /assets/images/manual_image/unsplash-gallery-image-2.jpg
    image_path: /assets/images/manual_image/unsplash-gallery-image-2-th.jpg
    alt: "placeholder image 11"
    title: "Image 11 title caption"
  - url: /assets/images/manual_image/unsplash-gallery-image-3.jpg
    image_path: /assets/images/manual_image/unsplash-gallery-image-3-th.jpg
    alt: "placeholder image 12"
    title: "Image 12 title caption"
gallery2:
  - url: https://flic.kr/p/8a6Ven
    image_path: https://farm2.staticflickr.com/1272/4697500467_8294dac099_q.jpg
    alt: "Black and grays with a hint of green"
  - url: https://flic.kr/p/8a738X
    image_path: https://farm5.staticflickr.com/4029/4697523701_249e93ba23_q.jpg
    alt: "Made for open text placement"
  - url: https://flic.kr/p/8a6VXP
    image_path: https://farm5.staticflickr.com/4046/4697502929_72c612c636_q.jpg
    alt: "Fog in the trees"
gallery3:
  - image_path: /assets/images/manual_image/unsplash-gallery-image-2-th.jpg
    alt: "placeholder image 2"
  - image_path: /assets/images/manual_image/unsplash-gallery-image-4-th.jpg
    alt: "placeholder image 4"
---

해당 메뉴얼 포스팅은 `<figure>` 요소로 감싸진 이미지의 `gallery` 기능 테스트이다.
`gallery`를 추가하기 위해 YAML에 아래의 내용을 추가하면 된다.

```yaml
gallery:
  - url: /assets/images/manual_image/unsplash-gallery-image-1.jpg
    image_path: /assets/images/manual_image/unsplash-gallery-image-1-th.jpg
    alt: "placeholder image 1"
    title: "Image 1 title caption"
  - url: /assets/images/manual_image/unsplash-gallery-image-2.jpg
    image_path: /assets/images/manual_image/unsplash-gallery-image-2-th.jpg
    alt: "placeholder image 2"
    title: "Image 2 title caption"
  - url: /assets/images/manual_image/unsplash-gallery-image-3.jpg
    image_path: /assets/images/manual_image/unsplash-gallery-image-3-th.jpg
    alt: "placeholder image 3"
    title: "Image 3 title caption"
  - url: /assets/images/manual_image/unsplash-gallery-image-4.jpg
    image_path: /assets/images/manual_image/unsplash-gallery-image-4-th.jpg
    alt: "placeholder image 4"
    title: "Image 4 title caption"
```

그리고 `gallery`를 **liquid** 형식의 **include** 안에 배치한다. `caption` 속성은 선택적으로 추가 할 수 있다.
```liquid
{% raw %}{% include gallery caption="This is a sample gallery with **Markdown support**." %}{% endraw %}
```

{% include gallery caption="This is a sample gallery with **Markdown support**." %}
> 기본 갤러리 형식은 위의 이미지와 같이 나옴을 알 수 있다.

다른 형식의 `gallery`도 지원한다. YAML 형식의 두번째 갤러리의 해시와 일치하도록 `id` 속성의 값을 지정한다.

```yaml
gallery2:
  - url: https://flic.kr/p/8a6Ven
    image_path: https://farm2.staticflickr.com/1272/4697500467_8294dac099_q.jpg
    alt: "Black and grays with a hint of green"
  - url: https://flic.kr/p/8a738X
    image_path: https://farm5.staticflickr.com/4029/4697523701_249e93ba23_q.jpg
    alt: "Made for open text placement"
  - url: https://flic.kr/p/8a6VXP
    image_path: https://farm5.staticflickr.com/4046/4697502929_72c612c636_q.jpg
    alt: "Fog in the trees"
```

그리고 다음 내용과 같이 적어서 적용한다. `id`를 `"gallery2"`로 설정하였다.

```liquid
{% raw %}{% include gallery id="gallery2" caption="This is a second gallery example with images hosted externally." %}{% endraw %}
```

{% include gallery id="gallery2" caption="This is a second gallery example with images hosted externally." %}

갤러리에 추가적인 옵션을 줄 수 있다. 페이지의 컨텐트 컨테이너를 더욱 넓게 채우기 위해 `class="full"` 속성을 추가 할 수 있다.  적용된 갤러리 이미지는 아래와 같다.

```liquid
{% raw %}{% include gallery id="gallery" class="full" caption="This is a third gallery example with two images and fills the entire content container." %}{% endraw %}
```

{% include gallery id="gallery3" class="full" caption="This is a third gallery example with two images and fills the entire content container." %}

또한 갤러리 컬럼의 레이아웃은 `layout` 속성으로 override 할 수 있다. 

```liquid
{% raw %}{% include gallery id="gallery" layout="half" caption="This is a half gallery layout example." %}{% endraw %}
```

{% include gallery id="gallery" layout="half" caption="This is a half gallery layout example." %}