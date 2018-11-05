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

> 本地服务访问地址为：http://localhost:4000

## Jekyll加入评论功能

目前我们已经创建了一个简单的博客系统，接下来为其添加评论功能，这样就可以让游客进行评论了

首先

