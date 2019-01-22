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
> Codename 为xenial查看系统要求，版本符合

### 1.3 卸载旧的或已安装Docker版本
* 旧的doceker或者已经安装过的一般叫docker或者docker-engine。
卸载它们。出现下面这样说明，已经卸载完了
```
root@iZ2ze5pt2475mgiqpzlh5kZ:~# apt-get remove docker docker-engine docker.io Reading package lists... Done 
Building dependency tree Reading state information... Done 
E: Unable to locate package docker 
E: Unable to locate package docker-engine E: Unable to locate package docker.io E: Couldn't find any package by glob 'docker.io'
E: Couldn't find any package by regex 'docker.io' 
```
## 二、安装Docker
### 2.1 根据需要选择不同的方式安装Docker
* 大多数用户 设置Docker的存储库并从中进行安装，以便安装和升级任务。这是推荐的方法。
* 有些用户下载DEB软件包并 手动安装，并完全手动管理升级。这对于在无法访问互联网的空隙系统上安装Docker等情况很有用。
* 在测试和开发环境中，一些用户选择使用自动 便利脚本来安装Docker。
### 2.2 使用Docker存储库进行安装
* 首次在新主机上安装Docker CE之前，需要设置Docker存储库。之后，您可以从存储库安装和更新Docker。
* 设置存储库
* 1.更新apt包资源索引
```
root@iZbp162mb58mqtz72o389nZ:~# apt-get update
Get:1 http://mirrors.cloud.aliyuncs.com/ubuntu xenial InRelease [247 kB]
Get:2 http://mirrors.cloud.aliyuncs.com/ubuntu xenial-updates InRelease [109 kB]
Get:3 http://mirrors.cloud.aliyuncs.com/ubuntu xenial-security InRelease [1
```
* 2.安装软件包以允许apt通过HTTPS使用存储库：
```
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common
```
* 3.添加Docker的官方密钥
```
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```










