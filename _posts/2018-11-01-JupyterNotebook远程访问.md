---
layout: post
title: JupyterNotebook远程访问
categories: Jupyter
description: JupyterNotebook远程访问
keywords: Jupyter
comments: true
---

# JupyterNotebook远程访问
Jupyter Notebook（此前被称为 IPython notebook）是一个交互式笔记本，支持运行 40 多种编程语言。  
Jupyter Notebook 的本质是一个 Web 应用程序，便于创建和共享文学化程序文档，支持实时代码，数学方程，可视化和 markdown。 用途包括：数据清理和转换，数值模拟，统计建模，机器学习等等

## 一、安装

为了以后便于开发与使用，建议[安装Anaconda](https://www.anaconda.com/download/)，Anaconda指的是一个开源的Python发行版本，其包含了conda、Pythont等180多个科学包与依赖库，这其中就有我们所要用到的Jupyter，Anaconda是一个非常强大的管理工具便于日后的开发，建议直接安装。

## 二、环境配置

默认情况下Jupyter只支持本地访问\(启动方法：打开终端输入指令，jupyter notebook\)，访问地址是`localhost:8888`_。_若想要其他机器访问需要进行额外配置。

#### 1. 生成notebook配置文件

Jupyter配置文件的默认路径为：`~/.jupyter/jupyternotebook_config.py `，该文件并存在需要输入以下指令生成配置文件：

```
jupyter notebook --generate-config
```

> 建议在非root用户权限下执行，如果root用户权限下报错在指令后面加上 --allow-root

成功后出现如下信息：

```
Writing default config to: ~/.jupyter/jupyter_notebook_config.py
```

#### 2. 生成密码

###### 自动生成

从 jupyter notebook 5.0 版本开始，提供了一个命令来设置密码：`jupyter notebook password`，生成的密码存储在`jupyter_notebook_config.json`。

```
$ jupyter notebook password
Enter password:  ****
Verify password: ****
```

###### 手动生成

除了使用提供的命令，也可以通过手动安装，我是使用的手动安装，因为`jupyter notebook password`出来一堆内容，没耐心看。打开 ipython 执行下面内容：

```
In [1]: from notebook.auth import passwd 
In [2]: passwd() 
Enter password: 
Verify password: 
Out[2]: 'sha1:67c9e60bb8b6:9ffede0825894254b2e042ea597d771089e11aed'
```

> `sha1:67c9e60bb8b6:9ffede0825894254b2e042ea597d771089e11aed`这一串就是要在 `jupyter_notebook_config.py`添加的密码

#### 3. 修改配置文件

在 `jupyter_notebook_config.py`中找到下面的行，取消注释并修改。

```
c.NotebookApp.ip='*' 
c.NotebookApp.password = u'sha:ce...刚才复制的那个密文' 
c.NotebookApp.open_browser = False 
c.NotebookApp.port =8888 #可自行指定一个端口, 访问时使用该端口
```

以上设置完以后就可以在服务器上启动 jupyter notebook，`jupyter notebook`, root 用户使用 `jupyter notebook --allow-root`。打开 `IP:指定的端口`, 输入密码就可以访问了

