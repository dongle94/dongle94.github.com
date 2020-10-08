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

YouTube video embed below.  
비디오 추가를 아래와 같이 붙일 수 있습니다. 
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
image Link type.  
이미지 링크를 붙일 수 있습니다. 즉, 클릭 시 링크로 이동합니다.

[![foo](https://live.staticflickr.com/8361/8400335147_5fabaa504c_o.jpg)](https://flic.kr/p/dNiUYB)
```yaml
[![foo](https://live.staticflickr.com/8361/8400335147_5fabaa504c_o.jpg)](https://flic.kr/p/dNiUYB)
```
<br>

캡션 달린 이미지도 생성 가능합니다. Django 템플릿 태그 생성 후 html `figure` 태그를 이용합니다.
{% capture fig_img %}
![Foo]({{ "/assets/images/unsplash-gallery-image-3.jpg" | relative_url }})
{% endcapture %}

<figure>
  {{ fig_img | markdownify | remove: "<p>" | remove: "</p>" }}
  <figcaption>Photo from Unsplash.</figcaption>
</figure>
