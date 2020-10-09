---
title: "Post: Video (YouTube) / Image (with Link, Caption)"
categories:
  - Manual
tags:
  - image
  - Post Formats
comments: false
hidden: true
---

유튜브 비디오를 아래와 같이 붙일 수 있습니다. **Markup**을 이용해서 태그 형식으로 붙인다. 
<iframe width="640" height="360" src="https://www.youtube-nocookie.com/embed/l2Of1-d5E5o?controls=0&amp;showinfo=0" frameborder="0" allowfullscreen></iframe>
<br>
```html
<iframe width="640" 
        height="360" 
        src="https://www.youtube-nocookie.com/embed/l2Of1-d5E5o?controls=0&amp;showinfo=0"
        frameborder="0" 
        allowfullscreen>
</iframe>
```
<br>
또한 이미지 링크를 붙일 수 있습니다. 링크형태. 즉, 클릭 시 설정해둔 링크로 이동한다.

[![foo](https://live.staticflickr.com/8361/8400335147_5fabaa504c_o.jpg)](https://flic.kr/p/dNiUYB)
```yaml
[![foo](https://live.staticflickr.com/8361/8400335147_5fabaa504c_o.jpg)](https://flic.kr/p/dNiUYB)
```
<br>

캡션 달린 이미지도 생성가능하다. 캡션이 달리면 이미지 하단에 설명 및 자막을 달 수 있다. **Liquid**의 `capture`기능을 이용하여 이미지 링크 생성 후 **Markup** `figure` 태그를 이용합니다.
{% capture fig_img %}
![Foo]({{ "/assets/images/manual_image/unsplash-gallery-image-3.jpg" | relative_url }})
{% endcapture %}

<figure>
  {{ fig_img | markdownify | remove: "<p>" | remove: "</p>" }}
  <figcaption>Photo from Unsplash.</figcaption>
</figure>

```html
{% raw %}{% capture fig_img %}
![Foo]({{ "/assets/images/manual_image/unsplash-gallery-image-3.jpg" | relative_url }})
{% endcapture %}{% endraw %}

<figure>
  {% raw %}{{ fig_img | markdownify | remove: "<p>" | remove: "</p>" }}{% endraw %}
  <figcaption>Photo from Unsplash.</figcaption>
</figure>
```