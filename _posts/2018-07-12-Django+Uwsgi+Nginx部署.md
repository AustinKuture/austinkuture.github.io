---
layout: post
title: Django+uWSGI+Nginx部署
categories:
  - Linux
  - Django
  - Nginx
description: Django+uWSGI+Nginx部署
keywords: Linux,ssh,免密铤登陆
comments: true
---


# Django+uWSGI+Nginx部署


uWSGI是一个Web应用服务器，它具有应用服务器，代理，进程管理及应用监控等功能。它支持WSGI协议，同时它也支持自有的uWSGI协议，该协议据说性能非常高，而且内存占用率低，为mod_wsgi的一半左右，我没有实测过。它还支持多应用的管理及应用的性能监控。虽然uWSGI本身就可以直接用来当Web服务器，但一般建议将其作为应用服务器配合Nginx一起使用，这样可以更好的发挥Nginx在Web端的强大功能。本文我们就来介绍如何搭建uWSGI+Ngnix环境来运行Python应用。

## 安装uWSGI

打开终端执行如下命令进行安装
```
pip install uwsgi
```
为理解与使用uWSGI在些通过创建uWSGI配置文件的方式进行部署，uWSGI在Django中部署参考:[Django中uwsgi的部署](http://www.kuture.com.cn/2018/06/20/Django%E4%B8%ADuwsgi%E7%9A%84%E9%83%A8%E7%BD%B2/)这篇文档
本次测试项目配置文件为：
```
[uwsgi]
# 配置服务器的监听ip和端口，让uWSGI作为nginx的支持服务器的话，设置socke就行；如果要让uWSGI作为单独的web-server，用http
#http = 0.0.0.0:3309
socket = 127.0.0.1:3309
# 配置项目目录（此处设置为项目的根目录）
chdir = /mnt/Project/ML/AKManhua/Manhua
# 配置入口模块 (django的入口函数的模块，即setting同级目录下的wsgi.py)
wsgi-file = Manhua/wsgi.py
# 开启master, 将会多开一个管理进程, 管理其他服务进程
master = True
# 服务器开启的进程数量
processes = 2
# 以守护进程方式提供服, 输出信息将会打印到log中
daemonize = wsgi.log
# 服务器进程开启的线程数量
threads = 4
# 退出的时候清空环境变量
vacuum = true
# 进程pid
pidfile = uwsgi.pid
# 配uWSGI搜索静态文件目录（及django项目下我们存放static文件的目录，用uWSGI作为单独服务器时才需要设置）
#check-static = /mnt/Project/ML/AKManhua/Manhua/templates/Weihua
```
> 执行它后，uWSGI将启动4个应用进程，每个进程有2个线程，和一个master主进程（监控其他进程状态，如果有进程死了，则重启）。同时，你可以访问”127.0.0.1:9191″来获取JSON格式的应用运行信息，uWSGI还提供了工具命令”uwsgitop”来像top一样监控应用运行状态，你可以用pip来安装它。

## 配置Nginx

##### 安装Nginx
输入如下命令进行安装
```
sudo apt-get install nginx
```
查看安装情况
```
service --status-all
```
##### 配置文件默认地址
打开nginx配置文件

```
sudo vi /etc/nginx/sites-available/default
```
修改”server { }”下的”root”到你的网站根目录
```
root /***/project;
```
将index.html加入到默认导航页中
```
index index.html;
```
##### 设置代理转发端口
```
	location / {

                include uwsgi_params;
                uwsgi_pass 127.0.0.1:3309;
                uwsgi_read_timeout 5;
		# First attempt to serve request as file, then
		# as directory, then fall back to displaying a 404.
		#try_files $uri $uri/ =404;
		# Uncomment to enable naxsi on this location
		# include /etc/nginx/naxsi.rules
	}

```

##### 重启nginx服务
```
sudo service nginx restart
```














