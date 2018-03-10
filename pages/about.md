---
layout: page
title: About
description: 不愿意抄作业
keywords: KillersDeath, Kevin
comments: true
menu: 关于
permalink: /about/
---

我是Kevin，业余物理（本科），术业自然语言（硕士）。

仰慕「优雅编码的艺术」。

坚信熟能生巧，努力改变人生。

## 联系

{% for website in site.data.social %}
* {{ website.sitename }}：[@{{ website.name }}]({{ website.url }})
{% endfor %}

## Skill Keywords

{% for category in site.data.skills %}
### {{ category.name }}
<div class="btn-inline">
{% for keyword in category.keywords %}
<button class="btn btn-outline" type="button">{{ keyword }}</button>
{% endfor %}
</div>
{% endfor %}
