---
layout: wiki
title: Jetson Nano
categories: Arm
description: Jetson Nano
keywords: Jetson,nano,jetson nano,jetson nano b01,b01
---

# Jetson Nano

```
本文主要用于记录，基于Jetson Nano开发板上应用于无人驾驶小车的系统开发流程，主要包括镜像制作、环境配置、硬件驱动、自动驾驶模型的训练等。
```

### 1. 镜像制作

* **镜像下载**

  ```
  重点提醒：JetsonNano处理器架构是aarch64架构，所以在jetson nano上安装软件时请选择arrch64版本的，否则会导致无法预料的严重后果。
  arm64和aarch64之间的区别：arm64已经与aarch64合并，因为aarch64和arm64指的是同一件事。ARM64是由Apple创建的，而AARCH64是由其他人（最著名的是GNU / GCC的）创建的。用于aarch64的Apple后端称为arm64，而LLVM 编译器社区开发的后端称为aarch64（因为它是64位ISA的规范名称），后来将arm64和 aarch64 两者合并，现在的后端称为aarch64。
  ```

  * **镜像文件**

    [Jetson 下载中心6](https://developer.nvidia.cn/embedded/downloads)

    ![](/images/WiKi/Jetson/jetson_000.png)

    > 下载完成后本地会保存为后缀为.zip的文件

* **格式化SD卡**

  ```
  初始化一张大于32GB的SD卡，并对其进行格式化，格式可以为exFAT
  ```

* **镜像烧录**

  文章以Mac OS系统为例进行镜像的烧录，下载[树莓派镜像烧录软件](https://www.raspberrypi.com/software/)，软件安装完成后，**操作系统**选项里点击**使用自定义镜像**并选择已下载的镜像文件，**SD卡**选项选择要进行烧录的SD卡即可，设置完成后点击**烧录**按钮镜像的烧录，等待完成即可。

  ![](/images/WiKi/Jetson/jetson_001.png)

### 2. 环境配置

* **修改环境变量**

  Jetson Nano利用官方镜像进行安装后，系统已经安装好了JetPack，cuda，cudaa，OpenCV等组件，需要修改下环境变量才可以使用。

  利用gedit打开 ~ 路径下.bashrc文件：

  ```
  sudo gedit ~./bashrc
  ```

  文件的最后添加以下三行：

      export PATH=/usr/local/cuda-10.2/bin:$PATH
       
      export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH
       
      export CUDA_HOME=$CUDA_HOME:/usr/local/cuda-10.2

  重新执行.bashrc文件，直接生效；

  source ~./bashrc

  输入nvcc -V命令进行测试，如果显示如下信息，证明修改正确。

      nvcc -V
       
      nvcc: NVIDIA (R) Cuda compiler driver
      Copyright (c) 2005-2019 NVIDIA Corporation
      Built on Wed_Oct_23_21:14:42_PDT_2019
      Cuda compilation tools, release 10.2, V10.2.89

* **更换国内镜像源**

  NVIDIA官方提供的Linux镜像版本为Ubuntu 18.04 LTS，镜像默认的是Ubuntu官方源，在国内使用该源下载程序时速度较慢，所以需要更换。

  首先，打开/etc/apt/sources.list文件，注释原内容

  ```
  sudo gedit /etc/apt/sources.list
  ```

   在末尾添加下述内容（以清华大学镜像源为例，注意镜像源需要支持arm64架构）

      # 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
      deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ bionic main restricted universe multiverse
      # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ bionic main restricted universe multiverse
      deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ bionic-updates main restricted universe multiverse
      # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ bionic-updates main restricted universe multiverse
      deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ bionic-backports main restricted universe multiverse
      # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ bionic-backports main restricted universe multiverse
      deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ bionic-security main restricted universe multiverse
      # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ bionic-security main restricted universe multiverse
       
      # 预发布软件源，不建议启用
      # deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ bionic-proposed main restricted universe multiverse
      # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ bionic-proposed main restricted universe multiverse

  保存并关闭文件，然后输入以下命令来更新源：

  ```
  sudo apt-get update
  ```

  其它部分常用镜像源：

  **中科大镜像源：**

      # 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
      deb http://mirrors.ustc.edu.cn/ubuntu-ports bionic main restricted universe multiverse
      # deb-src http://mirrors.ustc.edu.cn/ubuntu-ports bionic main restricted universe multiverse
      deb http://mirrors.ustc.edu.cn/ubuntu-ports bionic-updates main restricted universe multiverse
      # deb-src http://mirrors.ustc.edu.cn/ubuntu-ports bionic-updates main restricted universe multiverse
      deb http://mirrors.ustc.edu.cn/ubuntu-ports bionic-backports main restricted universe multiverse
      # deb-src http://mirrors.ustc.edu.cn/ubuntu-ports bionic-backports main restricted universe multiverse
      deb http://mirrors.ustc.edu.cn/ubuntu-ports bionic-security main restricted universe multiverse
      # deb-src http://mirrors.ustc.edu.cn/ubuntu-ports bionic-security main restricted universe multiverse
       
      # 预发布软件源，不建议启用
      # deb http://mirrors.ustc.edu.cn/ubuntu-ports bionic-proposed main restricted universe multiverse
      # deb-src http://mirrors.ustc.edu.cn/ubuntu-ports bionic-proposed main restricted universe multiverse

  **阿里源：**

      # 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
      deb https://mirrors.aliyun.com/ubuntu-ports/ bionic main restricted universe multiverse
      # deb-src https://mirrors.aliyun.com/ubuntu-ports/ bionic main restricted universe multiverse
      deb https://mirrors.aliyun.com/ubuntu-ports/ bionic-updates main restricted universe multiverse
      # deb-src https://mirrors.aliyun.com/ubuntu-ports/ bionic-updates main restricted universe multiverse
      deb https://mirrors.aliyun.com/ubuntu-ports/ bionic-backports main restricted universe multiverse
      # deb-src https://mirrors.aliyun.com/ubuntu-ports/s bionic-backports main restricted universe multiverse
      deb https://mirrors.aliyun.com/ubuntu-ports/ bionic-security main restricted universe multiverse
      # deb-src https://mirrors.aliyun.com/ubuntu-ports/ bionic-security main restricted universe multiverse
       
      # 预发布软件源，不建议启用
      # deb https://mirrors.aliyun.com/ubuntu-ports/ bionic-proposed main restricted universe multiverse
      # deb-src https://mirrors.aliyun.com/ubuntu-ports/ bionic-proposed main restricted universe multiverse

### 3. pip安装

```python
sudo apt-get install python3-pip python3-dev
```

 更新pip，保证是最新版本

```python
pip3 install --upgrade pip
```

### 4. Jtop安装

* **依赖安装**

  ```
  sudo apt-get install git cmake
  sudo apt-get install python3-dev
  sudo apt-get install libhdf5-serial-dev hdf5-tools
  sudo apt-get install libatlas-base-dev gfortran
  ```

* **安装jtop**

  ```
  sudo -H pip3 install -U jetson-stats
  ```

### 5. 安装Tensorflow

* **安装依赖库**

  ```
  sudo apt install python3-scipy -y
  sudo apt install python3-pandas -y
  sudo apt install python3-sklearn -y
  sudo apt install python3-seaborn -y
  ```

  安装一些其他依赖

  ```
  sudo apt-get install libhdf5-serial-dev hdf5-tools libhdf5-dev zlib1g-dev zip libjpeg8-dev
  ```

* **安装numpy**

  需要先升级 protobuf 版本至 3.3.0：

  ```
  sudo pip3 install protobuf==3.3.0
  ```

  安装 numpy：

  ```
  sudo pip3 install -U numpy==1.16.1
  ```

  注意这里numpy的版本，太高或者太低多多少少都会导致一些后面安装的问题，如果不指定版本，默认安装1.19.5版本会导致后面装h5py的时候报非法指令的错误。

* **安装h5py**

  这个包对依赖的版本要求真的非常高！出了各种各样的问题，最终选择下载源文件手动安装

  笔记本在清华的镜像软件源下载h5py==2.10.0（h5py-2.10.0.tar.gz）[Links for h5py (tsinghua.edu.cn)](https://pypi.tuna.tsinghua.edu.cn/simple/h5py/)

  下载完成用U盘拷贝到nano中

  在nano里面解压文件，并在对应文件夹中（文件夹可以看到setup.py）打开终端

  **注意！在安装前先要安装Cython==3.0.0a10!如果有Cython也要保证版本一致！**

  ```
  sudo pip3 install -U Cython==3.0.0a10
  ```

  **接着运行setup.py来装h5py，注意这里的python3不能少，否则系统默认是2.7的版本，会找不到Cython**

  ```
  sudo python3 ./setup.py install
  ```

  一段漫长的编译以后，h5py==2.10.0就装好了！

* **安装keras-applications包**

  ```
  sudo pip3 install -U keras-applications
  ```

* **安装future包**

  ```
  sudo pip3 install -U future
  ```

* **安装 setuptools 和 testresources 包**

  ```
  sudo pip3 install -U setuptools testresources
  ```

* **检查相应库的版本**

  最后检查一遍相应库的版本看是否满足安装 Tensorflow 的条件。

  检查相应库的版本：

  ```
  pip3 list
  ```

  安装 Tensorflow 的条件：

  ```
  numpy==1.16.1, future==0.17.1, mock==3.0.5, h5py==2.9.0, gast==0.2.2, keras_preprocessing==1.0.5, keras_applications==1.0.8, scipy==1.4.1
  ```

* **安装Tensorflow**

  使用pip3安装TensorFlow，该命令将安装与JetPack 4.4兼容的TensorFlow的最新版本：

  ```
  sudo pip3 install --pre --extra-index-url https://developer.download.nvidia.com/compute/redist/jp/v461 tensorflow
  ```

  如果速度太慢可以pip暂时换源：（在上面命令后加 -i https://pypi.tuna.tsinghua.edu.cn/simple）

  ```
  sudo pip3 install --pre --extra-index-url https://developer.download.nvidia.com/compute/redist/jp/v461 tensorflow -i https://pypi.tuna.tsinghua.edu.cn/simple
  ```

  等待一段时间后，Tensorflow就装好啦！！
  





