---
title: "Layout: Author Override & Profile disabled"
author: Billy Rick
excerpt: "A post to test author overrides using a data file and disabled profile"
last_modified_at: 2018-09-10T12:32:27-04:00
categories:
  - Jekyll
tag:
  - excerpt
header:
  overlay_color: "#555"
  caption: "Manual Docunment"
comments: false
hidden: true
---

Sites that may have content authored from various individuals can be accommodated by using [data files](https://jekyllrb.com/docs/datafiles/).

To attribute an author to a post or page that is different from the site author specified in `_config.yml`:

**Step 1.** Create `_data/authors.yml` and add authors using the following format. Anything variables found under `author` in `_config.yml` can be used (e.g. `name`, `bio`, `avatar`, author `links`, etc.).

```yaml
# /_data/authors.yml

Billy Rick:
  name        : "Billy Rick"
  bio         : "What do you want, jewels? I am a very extravagant man."
  avatar      : "/assets/images/bio-photo-2.jpg"
  links:
    - label: "Email"
      icon: "fas fa-fw fa-envelope-square"
      url: "mailto:billyrick@rick.com"
    - label: "Website"
      icon: "fas fa-fw fa-link"
      url: "https://thewhip.com"
    - label: "Twitter"
      icon: "fab fa-fw fa-twitter-square"
      url: "https://twitter.com/extravagantman"

Cornelius Fiddlebone:
  name        : "Cornelius Fiddlebone"
  bio         : "I ordered what?"
  avatar      : "/assets/images/bio-photo.jpg"
  links:
    - label: "Email"
      icon: "fas fa-fw fa-envelope-square"
      url: "mailto:cornelius@thewhip.com"
    - label: "Twitter"
      icon: "fab fa-fw fa-twitter-square"
      url: "https://twitter.com/rhymeswithsackit"
```

**Step 2.** Assign one of the authors in `authors.yml` to a post or page you wish to override the `site.author` with. 

Example: To assign `Billy Rick` as an author for a post the following YAML Front Matter would be applied:

```yaml
author: Billy Rick
```

또한 작가 프로필 출력 기능을 On/Off 할 수도 있습니다. 포스팅의 meta 정보에서 다음과 같이 작성합니다.
기본값은 `_config.yml` 에서 조정 가능하다. 현재 기본값은 `true`, 게시글은 `author: Billy Rick`을 적용하며 따로 적지 않았습니다.   
기본으로 설정되어 있는 옵션이 먼저 적용되고 포스팅 meta에 들어가있는 옵션이 덮어 씌워져서 적용됩니다.
```yaml
author_profile: false
``` 