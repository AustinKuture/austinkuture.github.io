---
layout: post
title: Pynsist制作Python程序的Windows安装包
categories:
  - Pynsist
  - Python
description: Pynsist制作Python程序的Windows安装包
keywords: 'Python打包, 打包成exe, Pynsist'
comments: true
---

# Pynsist制作Python程序的Windows安装包

```
使用Python语言编写GUI程序时，往往需要将程序中的依赖包连同程序一同打包成.exe安装文件，
打包完成后，便于在其他Window电脑上安装并使用该程序。本篇文章主要讲解如何使用Pynsist进行打包。
```

## 1. 安装Pynsist

##### 安装NIST
[NSIS](https://www.baidu.com/link?url=gUJe8bNlUIK5cKHwuKL1YygidcmbMVQuj4e_jfr6e-ye8IcgDg-omtb2oxl9Whsf&wd=&eqid=c615fdac0007654a000000025ca450be)（Nullsoft Scriptable Install System）是一个开源的 Windows 系统下安装程序制作程序。它提供了安装、卸载、系统设置、文件解压缩等功能。这如其名字所指出的那样，NSIS 是通过它的脚本语言来描述安装程序的行为和逻辑的。NSIS 的脚本语言和通常的编程语言有类似的结构和语法，但它是为安装程序这类应用所设计的。
##### 创建虚拟环境
使用conda创建虚拟环境xxx,完成后进入虚拟环境使用pip安装

```
pip install pynsist
```
> 建议打包在虚拟环境下进行，在电脑环境下容易报错。

## 2. 编写Pynsist配置文件
NSIS与Pynsisit安装完成后，需要编写配置文件[installer.cfg](https://pynsist.readthedocs.io/en/2.3/)才可以进行打包。
官方配置文件如下所示：

```
  [Application]
  name=My App
  version=1.0
  entry_point=myapp:main  # 应用启动入口
  icon=myapp.ico  # 打包后的应用图标
  
  [Python]
  version=3.6.3
  
  [Include]
  pypi_wheels = requests==2.18.4  # 需要打包进的Python依赖包
       beautifulsoup4==4.6.0
       html5lib==0.999999999
  
  files = LICENSE
      data_files/   # 项目中所需要静态文件 例如html、css、图片等
```

