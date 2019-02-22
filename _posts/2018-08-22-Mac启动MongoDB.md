---
layout: post
title: Mac启动MongoDB
categories:
  - Mac OSX
  - Mongo
description: Mac启动MongoDB
keywords: 'Mac, Mongo'
comments: true
---

# Mac启动MongoDB
##### 使用终端启动MongoDB：
打开终端输入以下指令，添加MongoDB配置文件：
```
sudo mongod --config /usr/local/etc/mongod.conf
```
保持当前窗口不关闭，新建终端，输入：
```
mongo
```
出现以下内容则代表启动成功：
![Mongo_00](/images/posts/Mac/mongo_00.png)

#### 启动Redis
```
cd /usr/local/bin
sudo ./redis-server
```