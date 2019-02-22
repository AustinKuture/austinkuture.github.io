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
> MobileNetV1 : 0.75

_转载文章请注明来源及作者_

```  
最近在搭建一个人工智能学习平台，主要包括在线训练图像分类模型、自学习聊天机器人、模型在线转换等功能。  
本篇文章主要讲解如何将训练好的Save Model模型，转换成移动端使用的Tensorflow Lite模型与Web端使用的Tensorflow JS模型。  
此处我们以Posenet为例来实现Lite与JS模型的转换。
```

Posenet是Tensorflow.js官方推荐的例子，本文不再赘述，感兴趣的话可以参考另篇文章[Posenet](http://www.kuture.com.cn/2019/01/24/PoseNet动作识别/)

## Tensorflow格式之间的转换

Tensorflow官方文档中，对GraphDef\(.pb\)、FrozenGraphDef\(带有冻结变量的.pb\)、SavedModel\(.pb - 用于推断服务器端的通用格式\)和Checkpoint文件\(在训练过程中的序列化变量\)有明确的解释，其转换过程如下图所示：

![trans](/images/posts/AI/tranlite01.jpg)

如果要转换tfjs或者Lite模型可以使用Keras的HDF5、SaveModel或者Froen Graphdef。由于目前Posenet只有tfjs版，为了更好的分解网络结构,我们先将js版转换为Python版的Posenet，然后再将tensorflow生成的SaveModel(.pb) 与 Checkpoints(.ckpt)，使用freeze_graph固化得到Frozen Graph(.pb)，此时的Frozen Graph(.pb)模型就可转换成Lite与tfjs模型了。
> 鉴于Python版的Posenet重建过程较为复杂且不是当前文章的重点，所以此处关于Python版的重建过程不再赘述。

## MobileNet_V1的模型结构

MobileNets基于一种流线型结构使用深度可分离卷积来构造轻型权重的深度神经网络，深度可分离卷积是一种将标准卷积分解成深度卷积以及一个1x1的卷积即逐点卷积。对于MobileNet而言，深度卷积针对每个单个输入通道应用单个滤波器进行滤波，然后逐点卷积应用1x1的卷积操作来结合所有深度卷积得到的输出。而标准卷积一步即对所有的输入进行结合得到新的一系列输出。深度可分离卷积将其分成了两步，针对每个单独层进行滤波然后下一步即结合。这种分解能够有效的大量减少计算量以及模型的大小。如图1所示，一个标准的卷积1(a)被分解成深度卷积1(b)以及1x1的逐点卷积1(c)。 
![trans02](/images/posts/AI/trans02.png)



