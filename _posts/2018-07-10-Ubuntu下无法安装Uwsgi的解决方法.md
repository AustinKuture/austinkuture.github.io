---
layout: post
title: Ubuntu下无法安装Uwsgi的解决方法
categories:
  - Django
description: Ubuntu下无法安装Uwsgi的解决方法
keywords: django,uwsgi
comments: true
---



# Ubuntu下无法安装Uwsgi的解决方法

> 系统环境： Deepin 15.6

> 适用系统： Ubuntu、Centos

> 语言环境： Python3.6.4

虚环境下无法安装Uwsgi,主要原因是缺少依赖文件，查看当前语言版本并安装相应的依赖文件，本环境中用的的Python3.6.4 故安装如下版本的依赖文件：
```
sudo apt-get install libpython3.6-dev
```
> 如果是python2.7, 则安装 libpython2.7-dev


未安装依赖包时的错误信息如下：

```
[thread 0][x86_64-linux-gnu-gcc -pthread] core/config_py.o
*** uWSGI compiling embedded plugins ***
[thread 0][x86_64-linux-gnu-gcc -pthread] plugins/python/python_plugin.o
[thread 1][x86_64-linux-gnu-gcc -pthread] plugins/python/pyutils.o
In file included from plugins/python/python_plugin.c:1:0:
plugins/python/uwsgi_python.h:2:20: fatal error: Python.h: 没有那个文件或目录
compilation terminated.
In file included from plugins/python/pyutils.c:1:0:
plugins/python/uwsgi_python.h:2:20: fatal error: Python.h: 没有那个文件或目录
compilation terminated.

----------------------------------------

Command "/home/windblow/Desktop/windcontrlenv/bin/python3.5 -u -c "import setuptools, tokenize;__file__='/tmp/pip-build-_yknw2vb/uwsgi/setup.py';f=getattr(tokenize, 'open', open)(__file__);code=f.read().replace('\r\n', '\n');f.close();exec(compile(code, __file__, 'exec'))" install --record /tmp/pip-w10pyemd-record/install-record.txt --single-version-externally-managed --compile --install-headers /home/windblow/Desktop/windcontrlenv/include/site/python3.5/uwsgi" failed with error code 1 in /tmp/pip-build-_yknw2vb/uwsgi/
```

依赖文件安装完成后，安装Uwsgi协议成功后如下如示：
```
Collecting uwsgi
  Downloading https://files.pythonhosted.org/packages/a2/c9/a2d5737f63cd9df4317a4acc15d1ddf4952e28398601d8d7d706c16381e0/uwsgi-2.0.17.1.tar.gz (800kB)
    100% |████████████████████████████████| 808kB 589kB/s 
Building wheels for collected packages: uwsgi
  Running setup.py bdist_wheel for uwsgi ... done
  Stored in directory: /root/.cache/pip/wheels/32/d6/90/0239cc69219013d9f402b098b7c5ef7454792c21acd1d6c24e
Successfully built uwsgi
Installing collected packages: uwsgi
Successfully installed uwsgi-2.0.17.1

```






















