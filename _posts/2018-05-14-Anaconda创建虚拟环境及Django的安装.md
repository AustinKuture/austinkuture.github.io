---
layout: post
title: Anaconda创建虚拟环境及Django的安装
categories:
  - Django
  - Anacoda
  - Linux
description: Anaconda创建虚拟环境及Django的安装
keywords: Linux,Anaconda,虚拟环境,Django
comments: true
---

# Anaconda创建虚拟环境及Django的安装
>适用系统： Deepin、Ubuntu、Mac OS、Windows、Raspberry Pi Linux
>调试环境： Deepin 15.5

本篇文章主要讲解如何使用Anaconda进行虚拟环境的创建、调用、删除等功能，并完成Django的安装与使用
### [Django介绍](#)
Django是一个开放源代码的Web应用框架，由Python写成。运用了MVT模式（Model,View,Template)，主要目标是使得开发复杂的、数据库驱动网站变得简单，Django注重组件的重用性和“可插拔性”，敏捷开发和DRY法则（Don't Repeat Yourself)。Django还提供了可选的创建、阅读、更新、删除界面。
### Anaconda安装Django1.8.2
使用Anaconda之前，请先安装Anaconda安装方法此处不再赘述。Anaconda安装完成后，输入以下命令可以查看当前已创建过的虚拟环境。
```
conda info --envs
```
!