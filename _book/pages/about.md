---
layout: page
title: About
description: 汲众言者博识，阅众家者博学
keywords: Kuture,AutinKuture,yakun,李亚坤
comments: true
menu: 关于
permalink: /about/
---

汲众言者博识，

阅众家者博学。


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
