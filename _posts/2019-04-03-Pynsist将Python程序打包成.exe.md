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
[NSIS](https://nsis.sourceforge.io/Download)（Nullsoft Scriptable Install System）是一个开源的 Windows 系统下安装程序制作程序。它提供了安装、卸载、系统设置、文件解压缩等功能。这如其名字所指出的那样，NSIS 是通过它的脚本语言来描述安装程序的行为和逻辑的。NSIS 的脚本语言和通常的编程语言有类似的结构和语法，但它是为安装程序这类应用所设计的。
##### 创建虚拟环境
使用conda创建虚拟环境xxx,完成后进入虚拟环境使用pip安装

```
pip install pynsist
```
> 建议打包在虚拟环境下进行，在电脑环境下容易报错。

## 2. 编写Pynsist配置文件
NSIS与Pynsisit安装完成后，需要编写配置文件[installer.cfg](https://pynsist.readthedocs.io/en/2.3/)（名字可以自定义）才可以进行打包。
官方配置文件如下所示：

```
  [Application]
  name=My App  # 打包好后的应用名字
  version=1.0  # 应用版本号
  entry_point=myapp:main  # 应用启动入口
  icon=myapp.ico  # 打包后的应用图标
  
  [Python]
  version=3.6.3  # Python 版本，根据项目需要选择相应版本，
                   同时要与Python项目中需要依赖文件保持版本的兼容。
  
  [Include]
  pypi_wheels = requests==2.18.4  # 需要打包进的Python依赖包
       beautifulsoup4==4.6.0
       html5lib==0.999999999
  
  files = LICENSE
      data_files/   # 项目中所需要静态文件 例如html、css、图片等
```

此处以开源软件Mu-editor为例对应用进行打包（我在开源软件的基础上加入了对Esp的支持，同时美化了UI）,配置文件如下:

```
[Application]
name=Mu-editor
version=1.0.2
entry_point=mu.app:run   # mu.app:run 程序运行入口相当于mu.app.run()
icon=xxx\mu\other\Mu-editor.ico
publisher=Kuture   # 作者
license_file=LICENSE  # 证书

[Command mu-debug]  # 自定义入口
entry_point=mu.app:debug

[Command pgzrun]    # 自定义入口 
entry_point=pgzero.runner:main

[Python]
version=3.6.4
bitness=64    
format=bundled

[Include]
pypi_wheels=
    setuptools==38.4.0
    cycler==0.10.0
    decorator==4.2.1
    ipykernel==4.8.0
    ipython==6.2.1
    ipython-genutils==0.2.0
    jupyter-client==5.2.2
    jupyter-core==4.4.0
    matplotlib==2.1.2
    numpy==1.14.1
    parso==0.1.1
    pexpect==4.3.1
    pickleshare==0.7.4
    pip==18.1
    prompt-toolkit==1.0.15
    ptyprocess==0.5.2
    pycodestyle==2.3.1
    pyflakes==1.6.0
    Pygments==2.2.0
    pyparsing==2.2.0
    PyQt5==5.10.1
    pyserial==3.4
    python-dateutil==2.6.1
    pytz==2017.3
    pyzmq==16.0.4
    QScintilla==2.10.3
    qtconsole==4.3.1
    sip==4.19.8
    six==1.11.0
    tornado==4.5.3
    traitlets==4.3.2
    wcwidth==0.1.7
    pygame==1.9.3
    PyQtChart==5.10.1
    appdirs==1.4.3
    gpiozero==1.4.1
    guizero==0.5.3
    pigpio==1.40.post1
    Pillow==5.0.0
    requests==2.18.4
    certifi==2018.1.18
    chardet==3.0.4
    idna==2.6
    urllib3==1.22
    semver==2.8.0
    adafruit-ampy==1.0.7  # 用于esp32/esp8266进行通信
    opencv-python==4.0.0.21  # 用于计算机视觉处理

packages=
    jedi
    simplegeneric
    pgzero
    tkinter
    _tkinter
    turtle
    nudatus
    black
    esptool  # esp芯片开发板的固件刷入工具
    autopep8  # Python代码自动格式化

files=xxx\mu\resources
```
## 3. 打包
配置文件编写完成之后，即可开始打包，首先以管理员的身份打开终端并进行项目的虚拟环境下例如：activate xxx 输入以下命令进行打包：

```
pynsist xxx.cfg
```
安装依赖文件中可能会报错，例如依赖包与Python版本不兼容，更换相应的版本即可。或者包不能打入，可以将包以packages的形式打入。例如esptool在pypi_wheels中不能正常打入的需要放入packages才可以。
打包完成后，进入build中找到相应的.exe文件双击即可安装。


