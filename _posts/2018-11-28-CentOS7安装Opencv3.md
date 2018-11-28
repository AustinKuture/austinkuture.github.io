---
layout: post
title: CentOS7安装Opencv3
categories:
  - Opencv
  - CentOS
description: CentOS7安装Opencv3
keywords: 'Opencv, CentOS, libpng12.so.0'
comments: true
---

# CentOS7安装Opencv3

```
最近anaconda推出了Python3.7 ,自己就装了该版本，但是安装后发现安装opencv3不像Python3.5 版本安装opencv3那样简单，以前安装opencv3 只需要conda install –channel https://conda.anaconda.org/menpo opencv3 这条命令即可，现在安装上Python3.6，用该命令会报以下错误：
UnsatisfiableError: The following specifications were found to be in conflict:
- opencv3 -> python 2.7*
- python 3.7*
```

### 解决办法：

#### 方法一：

先执行 conda install python=3.5，之后执行conda install -c menpo opencv3 该方法是又回到了Python3。5版本，那就不如一开始就下载anaconda4.2版本的，本身就是Python3。5，直接应用conda install -c menpo opencv3 命令安装opencv3即可。

#### 方法二：

根据安装python的版本下载相应的opencv \*.whl文件，笔者是python3.6，所以对应的是opencv\_python‑3.2.0‑cp36‑cp36m‑win\_amd64.whl这个文件，下载网址是：http://www.lfd.uci.edu/~gohlke/pythonlibs/，

![](https://img-blog.csdn.net/20170720143218654?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTUwOTk3MQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

下载好后把文件拷贝到D:\Program Files\Anaconda3\Lib\site-packages文件夹下（anaconda安装路径，每个人的不一样，要根据自己的安装路径更改），在该文件下按住Shift建+鼠标右键，出来一个对话框，选择‘在此处打开命令窗口’即可打开doc 窗口，之后执行pip install opencv\_python‑3.2.0‑cp36‑cp36m‑win\_amd64.whl 安装opencv3，执行完，显示成功安装 opencv-python‑3.2.0，就应该没问题。

![](https://img-blog.csdn.net/20170720143012655?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTUwOTk3MQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)



## 导入 cv2 报错  ImportError: libpng12.so.0: cannot open shared object file: No such file or directory

```
sudo yum install libpng12
```



