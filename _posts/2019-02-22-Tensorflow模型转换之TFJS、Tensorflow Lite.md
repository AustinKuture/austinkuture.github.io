---
layout: post
title: Tensorflow模型转换之TFJS、Tensorflow Lite
categories:
  - Tensorflow
  - 人工智能
description: Tensorflow模型转换之TFJS、Tensorflow Lite
keywords: '动作识别, tensorflow.js, tensorflow lite, 模型转换'
comments: true
---

# Tensorflow模型转换之TFJS、Tensorflow Lite

> Anaconda : 4.5.11  
> Tensorflow : 1.12.0  
> Tensorflowjs : 0.8.0  
> Opencv3\(menpo\) : 3.1.0  
> 系统环境 : Mac OSX 10.14.3  
> Python\(Opencv需要3.5\) : 3.5.4

_转载文章请注明来源及作者_

```  
最近在搭建一个人工智能学习平台，主要包括在线训练图像分类模型、自学习聊天机器人、模型在线转换等功能。  
本篇文章主要讲解如何将训练好的Save Model模型，转换成移动端使用的Tensorflow Lite模型与Web端使用的Tensorflow JS模型。  
此处我们以Posenet为例来实现Lite与JS模型的转换。``

Posenet是Tensorflow.js官方推荐的例子，本文不再赘述，感兴趣的话可以参考另篇文章[Posenet](http://www.kuture.com.cn/2019/01/24/PoseNet动作识别/)

## Tensorflow格式之间的转换

Tensorflow官方文档中，对GraphDef\(.pb\)、FrozenGraphDef\(带有冻结变量的.pb\)、SavedModel\(.pb - 用于推断服务器端的通用格式\)和Checkpoint文件\(在训练过程中的序列化变量\)有明确的解释，其转换过程如下图所示：

!\[\]\(./images/posts/AI/translite01.jpg\)



