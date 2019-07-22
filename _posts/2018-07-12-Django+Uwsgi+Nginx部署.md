---
layout: post
title: Django+uWSGI+Nginx部署
categories:
  - Linux
  - Django
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
> 执行它后，uWSGI将启动2个应用进程，每个进程有4个线程，和一个master主进程（监控其他进程状态，如果有进程死了，则重启）。同时，你可以访问”127.0.0.1:9191″来获取JSON格式的应用运行信息，uWSGI还提供了工具命令”uwsgitop”来像top一样监控应用运行状态，你可以用pip来安装它。

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
> 这段配置表明Nginx会将收到的所有请求都转发到”127.0.0.1:3031″端口上，即uWSGI服务器上。

##### 重启nginx与uWSGI服务器

```
sudo service nginx restart
uwsgi --reload uwsgi.pid
```
> 启动uwsgi时需进行uwsgi.ini所在目录中

##### 配置完成的Nginx

```
# You may add here your
# server {
#	...
# }
# statements for each of your virtual hosts to this file

##
# You should look at the following URL's in order to grasp a solid understanding
# of Nginx configuration files in order to fully unleash the power of Nginx.
# http://wiki.nginx.org/Pitfalls
# http://wiki.nginx.org/QuickStart
# http://wiki.nginx.org/Configuration
#
# Generally, you will want to move this file somewhere, and start with a clean
# file but keep this around for reference. Or just disable in sites-enabled.
#
# Please see /usr/share/doc/nginx-doc/examples/ for more detailed examples.
##

server {
	listen 80 default_server;
	listen [::]:80 default_server ipv6only=on;

	root /mnt/Project/ML/AKManhua/Manhua;
	index index.html index.htm;

	# Make site accessible from http://localhost/
        #listen 80;
	server_name *****; 网站域名，或ip
        charset UTF-8;
        client_max_body_size 75M;

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
        
    


	# Only for nginx-naxsi used with nginx-naxsi-ui : process denied requests
	#location /RequestDenied {
	#	proxy_pass http://127.0.0.1:8080;    
	#}

	#error_page 404 /404.html;

	# redirect server error pages to the static page /50x.html
	#
	#error_page 500 502 503 504 /50x.html;
	#location = /50x.html {
	#	root /usr/share/nginx/html;
	#}

	# pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
	#
	#location ~ \.php$ {
	#	fastcgi_split_path_info ^(.+\.php)(/.+)$;
	#	# NOTE: You should have "cgi.fix_pathinfo = 0;" in php.ini
	#
	#	# With php5-cgi alone:
	#	fastcgi_pass 127.0.0.1:9000;
	#	# With php5-fpm:
	#	fastcgi_pass unix:/var/run/php5-fpm.sock;
	#	fastcgi_index index.php;
	#	include fastcgi_params;
	#}

	# deny access to .htaccess files, if Apache's document root
	# concurs with nginx's one
	#
	#location ~ /\.ht {
	#	deny all;
	#}
}


# another virtual host using mix of IP-, name-, and port-based configuration
#
#server {
#	listen 8000;
#	listen somename:8080;
#	server_name somename alias another.alias;
#	root html;
#	index index.html index.htm;
#
#	location / {
#		try_files $uri $uri/ =404;
#	}
#}


# HTTPS server
#
#server {
#	listen 443;
#	server_name localhost;
#
#	root html;
#	index index.html index.htm;
#
#	ssl on;
#	ssl_certificate cert.pem;
#	ssl_certificate_key cert.key;
#
#	ssl_session_timeout 5m;
#
#	ssl_protocols SSLv3 TLSv1 TLSv1.1 TLSv1.2;
#	ssl_ciphers "HIGH:!aNULL:!MD5 or HIGH:!aNULL:!MD5:!3DES";
#	ssl_prefer_server_ciphers on;
#
#	location / {
#		try_files $uri $uri/ =404;
#	}
#}
```













