---
layout: page
title: Wiki
description: 人越学越觉得自己无知
keywords: 维基, Wiki
comments: false
menu: 维基
permalink: /wiki/
---

> 记多少命令和快捷键会让脑袋爆炸呢？


<div class="btn-inline">
{% for wiki in site.wiki %}
{% if wiki.title != "Wiki Template" %}
<button class="btn btn-outline" type="button" onclick="window.location.href='{{ site.url }}{{ wiki.url }}'">{{ wiki.title }}</button>
{% endif %}
{% endfor %}
</div>
