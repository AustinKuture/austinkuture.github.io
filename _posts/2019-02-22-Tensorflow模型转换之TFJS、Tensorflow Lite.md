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

如果要转换tfjs或者Lite模型可以使用Keras的HDF5、SaveModel或者Froen Graphdef。由于目前Posenet只有tfjs版，为了更好的分解网络结构,我们先将js版转换为Python版的Posenet，然后再将tensorflow生成的SaveModel\(.pb\) 与 Checkpoints\(.ckpt\)，使用freeze\_graph固化得到Frozen Graph\(.pb\)，此时的Frozen Graph\(.pb\)模型就可转换成Lite与tfjs模型了。
在进行模型转换之前，我们先熟悉一下MobileNet

> 鉴于Python版的Posenet重建过程较为复杂且不是当前文章的重点，所以此处关于Python版的重建过程不再赘述。

## MobileNet\_V1

#### 深度可分离卷积
MobileNets基于一种流线型结构使用深度可分离卷积来构造轻型权重的深度神经网络，深度可分离卷积是一种将标准卷积分解成深度卷积以及一个1x1的卷积即逐点卷积。对于MobileNet而言，深度卷积针对每个单个输入通道应用单个滤波器进行滤波，然后逐点卷积应用1x1的卷积操作来结合所有深度卷积得到的输出。而标准卷积一步即对所有的输入进行结合得到新的一系列输出。深度可分离卷积将其分成了两步，针对每个单独层进行滤波然后下一步即结合。这种分解能够有效的大量减少计算量以及模型的大小。如图1所示，一个标准的卷积1\(a\)被分解成深度卷积1\(b\)以及1x1的逐点卷积1\(c\)。   
![trans02](/images/posts/AI/trans02.png)

* 一个标准卷积层输入DF∗DF∗M的特征图F，并得到一个DG∗DG∗N的输出特征图G，其中DF表示输入特征图的宽和高，M是输入的通道数（输入的深度）DG为输出特征图的宽和高，N是输出的通道数（输出的深度）。  
* 标准卷积层通过由大小为DK∗DK∗M∗N个卷积核K个参数，其中DK是卷积核的空间维数，M是输入通道数，N是输出通道数。  
标准卷积的输出的卷积图，假设步长为1，则padding由下式计算：Gk,l,n=∑i,j,mKi,j,m,n⋅Fk+i−1,l+j−1,m。其计算量为DK∗DK∗M∗N∗DF∗DF，其由输入通道数M、输出通道数N、卷积核大小DK、输出特征图大小DF决定。MobileNet模型针对其进行改进。首先，使用深度可分离卷积来打破输出通道数与卷积核大小之间的相互连接作用。  
* 标准的卷积操作基于卷积核和组合特征来对滤波特征产生效果来产生一种新的表示。滤波和组合能够通过分解卷积操作来分成两个独立的部分，这就叫做深度可分离卷积，可以大幅度降度计算成本。  
* 深度可分离卷积由两层构成：深度卷积和逐点卷积。我们使用深度卷积来针对每一个输入通道用单个卷积核进行卷积，得到输入通道数的深度，然后运用逐点卷积，即应用一个简单的1x1卷积，来对深度卷积中的输出进行线性结合。MobileNets对每层使用batchnorm和ReLU非线性激活。  
* 深度卷积对每个通道使用一种卷积核，可以写成：Gk,l,m^=∑i,jKi,j,m^⋅Fk+i−1,l+j−1,m，其中K̂ 是深度卷积核的尺寸DK∗DK∗M，K̂ 中第m个卷积核应用于F中的第m个通道来产生第m个通道的卷积输出特征图Ĝ 。  
* 深度卷积的计算量为：DK∗DK∗M∗DF∗DF。  
* 深度卷积相对于标准卷积十分有效，然而其只对输入通道进行卷积，没有对其进行组合来产生新的特征。因此下一层利用另外的层利用1x1卷积来对深度卷积的输出计算一个线性组合从而产生新的特征。  
* 那么深度卷积加上1x1卷积的逐点卷积的结合就叫做深度可分离卷积，最开始在（Rigid-motion scattering for image classification.）中被提出。  
* 深度可分离卷积的计算量为：DK∗DK∗M∗DF∗DF+M∗N∗DF∗DF,即深度卷积和1x1的逐点卷积的和。  
* 通过将卷积分为滤波和组合的过程得到对计算量的缩减：![trans03.png](/images/posts/AI/trans03.png)
* MobileNet使用3x3的深度可分离卷积相较于标准卷积少了8到9倍的计算量，然而只有极小的准确率的下降如第4节。  
* 另外的空间维数的分解方式如（Flattenedconvolutional neural networks for feedforward acceleration）（Rethinking the inception architecture for computer vision.）中。但是相较于深度可分离卷积，计算量的减少也没有这么多。

#### MobileNet网络结构
MobileNet结构就像前面所提到的由深度可分离卷积所构成，且除了第一层之外为全卷积。通过用这些简单的项定义网络能够更容易的探索网络的拓扑结构来找到一个更好的网络。MobileNet结构由下表1定义。
![trans03.png](/images/posts/AI/trans04.png)

## Tensorflow Lite 模型转换
#### 查看Frozen Graph模型中的变量名称
模型在转换时需要知道模型的输入与输出节点，所以我们先查看一下模型转换后的变量名称，确定一直输入与输出节点。

```
import tensorflow as tf
# graph_model_path 为Frozen Graph模型的路径
with tf.gfile.GFile(graph_model_path, 'rb') as rf:
    graph_def = tf.GraphDef()
    graph_def.ParseFromString(rf.read())

with tf.Graph().as_default() as graph:
    tf.import_graph_def(graph_def, name='')
    i = 0
    opname_list = []
    for op in graph.get_operations():
        print(i, ' : ', op.name, op.values())
        opname_list.append(op.name)
        i += 1
```

Frozen Graph格式的Posenet模型：

```
s
```
>其中124行之前为Mobilenet的网络结构，124行的image是我们要找的图像输入节点。211、213与214是我们要找的输出节点。

在确定好输入与输出节点之后就可以进行Lite模型转换了。
#### 使用TFLiteConverter转换模型
```
inputs = ['image']  # 输入节点
outputs = ['heatmap',
           'offset_2',
           'displacement_fwd_2',
           'displacement_bwd_2']  # 出节点

converter = tf.contrib.lite.TFLiteConverter.from_frozen_graph(input_graph, inputs, outputs)
converter.post_training_quantize = True
tflite_model = converter.convert()

open(output_lite, 'wb').write(tflite_model)
```