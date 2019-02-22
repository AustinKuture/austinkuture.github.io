---
layout: post
title: Adding comments to your Jekyll blog
categories: Jupyter
description: >-
  Jekyll is a Ruby-based engine which helps you to create static websites and
  blogs. It’s one of the most popular systems out there which comes with tons of
  plugins and examples.
keywords: Jupyter
comments: true
---

# Adding comments to your Jekyll blog
Jekyll 是一款基于Ruby开发的静态网站与博客生成系统，这也是一款目前较为流行的静态博客系统。本篇文章主要讲解如何使用Just Comments与Github Pages相结合生成一个具有评论功能的博客网站。

## 使用Jekyll创建博客系统

```
jekyll new myblogsite
```

> 有关jekyll安装此处不再赘述

创建完成后进入myblogsite目录，运行jekyll

```
bundle exec jekyll serve
```

> 本地服务访问地址为：[http://localhost:4000](http://localhost:4000)

## Jekyll加入评论功能

目前我们已经创建了一个简单的博客系统，接下来为其添加评论功能，这样就可以让游客进行评论了

首先，去Just Comments官网创建一个账号（建议使用Github授权登陆），登陆成功后，在Account界面中会显示用户ID与data-apikey，复制data-apikey。  
![jekyll00](/images/posts/Jekyll/jekyll_00.png)其次，我们需要为博客文章加入Wiget，创建文件\_includes/head.html\(如果该文件已存在则不需要重新创建\)，进入后head.html文件并输入以下内容

```
<head>
  <meta charset="utf-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  {% seo %}
  <link rel="stylesheet" href="{{ "/assets/main.css" | relative_url }}">
  <link rel="alternate" type="application/rss+xml"
    title="{{ site.title | escape }}" href="{{ "/feed.xml" | relative_url }}">
  
  <!-- Start 此处我们为评论加入的js -->
  <script async src="https://just-comments.com/w.js"></script>
  <!-- End 此处我们为评论加入的js -->
  <!-- 其他根据需要进行修改与添加，没有特殊需要为默认值 -->
  
</head>
```

最后，创建或进入文件./_layouts/post.html,在文章尾部加入评论窗口：
```
<!-- START 评论 -->
  <div
    class="just-comments"
    data-allowguests="true"
    data-apikey="之前注册的APIKEY">
  </div>
  <!-- END 评论-->
```

## 效果

![jekyll01](/images/posts/Jekyll/jekyll_01.png)
































