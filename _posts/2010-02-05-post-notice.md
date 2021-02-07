---
title: "Post: Notice"
last_modified_at: 2020-10-05
categories:
  - Jekyll
tags:
  - Post Formats
  - notice
comments: false
header:
  overlay_color: "#555"
  caption: "Manual Docunment"
hidden: true
---


`notice`. 즉, 안내문은 주변 컨텐츠 정보를 설명하는 정보를 표시한다. 일반적으로 특정한 세부사항을 강조하는데에 사용 된다.

Kramdown(마크다운 종류 중 하나)을 사용할 때, `{: .notice}` 를 문장 뒤에 추가해서 `.notice` 를 `<p></p>` 요소에 할당할 수 있다. 마크다운에서는 `<p></p>` 대신에 그냥 줄글 형태로 글을 쓰기에 마지막에 줄을 바꾸고 `{: .notice}`을 추가해주면 된다.

**Changes in Service:** We just updated our [privacy policy](#) here to better service our customers. We recommend reviewing the changes.
{: .notice}

**Primary Notice:** Lorem ipsum dolor sit amet, consectetur adipiscing elit. Integer nec odio. [Praesent libero](#). Sed cursus ante dapibus diam. Sed nisi. Nulla quis sem at nibh elementum imperdiet.
{: .notice--primary}

<div class="notice--primary" markdown="1">
**Primary Notice with code block:** Lorem ipsum dolor sit amet, consectetur adipiscing elit. Integer nec odio. [Praesent libero](#). Sed cursus ante dapibus diam. Sed nisi. Nulla quis sem at nibh elementum imperdiet.

```html
<html>
  <body>Some body.<body>
</html>
```
</div>

**Info Notice:** Lorem ipsum dolor sit amet, [consectetur adipiscing elit](#). Integer nec odio. Praesent libero. Sed cursus ante dapibus diam. Sed nisi. Nulla quis sem at nibh elementum imperdiet.
{: .notice--info}

**Warning Notice:** Lorem ipsum dolor sit amet, consectetur adipiscing elit. [Integer nec odio](#). Praesent libero. Sed cursus ante dapibus diam. Sed nisi. Nulla quis sem at nibh elementum imperdiet.
{: .notice--warning}

**Danger Notice:** Lorem ipsum dolor sit amet, [consectetur adipiscing](#) elit. Integer nec odio. Praesent libero. Sed cursus ante dapibus diam. Sed nisi. Nulla quis sem at nibh elementum imperdiet.
{: .notice--danger}

**Success Notice:** Lorem ipsum dolor sit amet, consectetur adipiscing elit. Integer nec odio. Praesent libero. Sed cursus ante dapibus diam. Sed nisi. Nulla quis sem at [nibh elementum](#) imperdiet.
{: .notice--success}

`notice`에 일부 문단 또는 다른 요소들을 포함할 수도 있다. 위에서 세번째 예제는 
맨 위의 `Changes in Service` 부터 `Success Notice` 까지 적용된 `notice`의 내용이 다르다. 순서대로 다음과 같다.
```html
내용 {: .notice}
내용 {: .notice--primary}
<div class="notice--primary" markdown="1"> 내용 ```code``` </div>
내용 {: .notice--info}
내용 {: .notice--warning}
내용 {: .notice--danger}
내용 {: .notice--success}
```


Jekyll의 **Liquid** 형식의 `capture`를 이용하여 내부 요소를 **Markdown**의 형태로 유지하고 `notice` 기능을 아래와 같이 **Markup**으로 생성할 수 있다. 줄 바꿈이나 공백을 포함 할 수 있기에 좋은 방식이다. 

```html
{% raw %}{% capture notice-2 %}
#### New Site Features

* You can now have cover images on blog pages
* Drafts will now auto-save while writing
{% endcapture %}{% endraw %}

<div class="notice">{% raw %}{{ notice-2 | markdownify }}{% endraw %}</div>
```

{% capture notice-2 %}
#### New Site Features

* You can now have cover images on blog pages
* Drafts will now auto-save while writing
{% endcapture %}

<div class="notice">
  {{ notice-2 | markdownify }}
</div>

혹은 `capture`를 사용하지 않고 오직 **HTML**만을 사용하여 `notice`를 생성할 수 있다. 
```html
<div class="notice">
  <h4>Message</h4>
  <p>A basic message.</p>
</div>
```

<div class="notice">
  <h4>Message</h4>
  <p>A basic message.</p>
</div>