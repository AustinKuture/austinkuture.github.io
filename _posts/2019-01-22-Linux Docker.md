---
layout: post
title: Linux Docker
categories:
  - Linux
  - Docker
description: Linux Docker
keywords: 'Linux, Docker, 容器, Jupyter'
comments: true
---

# Linux Docker
Docker 是一个开源的应用容器引擎，让开发者可以打包他们的应用以及依赖包到一个可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。容器是完全使用沙箱机制，相互之间不会有任何接口。
一个完整的Docker有以下几个部分组成：
* DockerClient客户端
* Docker Daemon守护进程
* Docker Image镜像
* DockerContainer容器

[Docker官网](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
## 一、系统环境
### 1.1 系统要求
安装Docker可以适配的系统环境如下：
* Artful 17.10 (Docker CE 17.11 Edge and higher only)
* Xenial 16.04 (LTS)
* Trusty 14.04 (LTS)
* Docker CE is supported on Ubuntu on x86_64, armhf, s390x (IBM Z), and ppc64le (IBM Power) architectures.
* ppc64le and s390x limitations: Packages for IBM Z and Power architectures are only available on Ubuntu Xenial and above.

### 1.2 以Ubuntu为例
查看系统版本信息
```
root@iZ2ze5pt2475mgiqpzlh5kZ:~# lsb_release -a
LSB Version:	core-9.20160110ubuntu0.2-amd64:core-9.20160110ubuntu0.2-noarch:security-9.20160110ubuntu0.2-amd64:security-9.20160110ubuntu0.2-noarch
Distributor ID:	Ubuntu
Description:	Ubuntu 16.04.4 LTS
Release:	16.04
Codename:	xenial
```











