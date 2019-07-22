---
layout: post
title: Linux Docker
categories:
  - Linux
description: Linux Docker
keywords: Linux, Docker, 容器, Jupyter
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
root@iZ2ze5pt2475mgiqpzlh5kZ:~# apt-get update
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
* 在执行这个命令的时候，如果是阿里云上的主机可能会报一个错，无法解析主机名。root@iZ2ze5pt2475mgiqpzlh5kZ：就是我这台机的主机名
```
root@iZ2ze5pt2475mgiqpzlh5kZ:~# curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add - 
sudo: unable to resolve host iZbp162mb58mqtz72o389nZ OK
```
* 打开/etc/hosts,并将主机名添加上去， 主机名粘贴在 localhost 之后
```
vim /etc/hosts
```
> 没有安装vim的话可以使用 vi 进行打开 
* 添加Docker官方密钥
```
root@iZ2ze5pt2475mgiqpzlh5kZ:~# curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
OK
```
* 9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88通过搜索指纹的最后8个字符，确认您现在拥有带指纹的密钥 。
```
root@iZ2ze5pt2475mgiqpzlh5kZ:~# sudo apt-key fingerprint 0EBFCD88 
pub 4096R/0EBFCD88 2017-02-22
      Key fingerprint = 9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88 
uid Docker Release (CE deb) <docker@docker.com> 
sub 4096R/F273FCD8 2017-02-22 root@iZbp162mb58mqtz72o389nZ:~# 
```
* 查看Ubuntu发行版名称
```
root@iZ2ze5pt2475mgiqpzlh5kZ:~# lsb_release -cs
xenial
```
* 查看本机的系统架构
```
root@iZ2ze5pt2475mgiqpzlh5kZ:~# dpkg --print-architecture
amd64
```
![](/images/posts/Linux/docker00.png)

根据系统选择相应的发行版本
* amd64
```
$ sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```
* armhf
```
$ sudo add-apt-repository \
   "deb [arch=armhf] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```
* xenial
```
sudo add-apt-repository \
   "deb [arch=xenial] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```
> 用Linux Mint Rafaela，可以使用 trusty

### 2.3 正式安装
* 更新apt软件包索引。
```
sudo apt-get update
```
* 安装最新版本的Docker CE，或者转到下一步安装特定版本：
```
sudo apt-get install docker-ce
```
> 至此已安装完成，无特殊版本需要可以直接跳至第三节
* 注意：
有多个Docker存储库？
如果启用了多个Docker存储库，则安装或更新时未指定版本apt-get install或 apt-get update命令始终会安装尽可能高的版本，这可能不适合您的稳定性需求

* 要安装特定版本的Docker CE，请列出回购站中的可用版本，然后选择并安装：
一个。列出您的回购中可用的版本：

```
apt-cache madison docker-ce

docker-ce | 18.03.0~ce-0~ubuntu | https://download.docker.com/linux/ubuntu xenial/stable amd64 Packages
```
> 初次使用不建议安装特定全本

* 例如，通过其完全限定的软件包名称来安装特定的版本，该名称是软件包名称（docker-ce）加上版本字符串（第2列），直到第一个连字符，用等号（=） 分隔docker-ce=18.03.0.ce。
 Docker守护进程自动启动
 
```
sudo apt-get install docker-ce=<VERSION>
```
## 三、Docker的使用
### 3.1 镜像的安装
* 去docker仓库查找image
```
root@iZ2ze5pt2475mgiqpzlh5kZ:~#  docker search centos
NAME                               DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
centos                             The official build of CentOS.                   5133                [OK]                
ansible/centos7-ansible            Ansible on Centos7                              119                                     [OK]
jdeathe/centos-ssh                 CentOS-6 6.10 x86_64 / CentOS-7 7.5.1804 x86…   106                                     [OK]
consol/centos-xfce-vnc             Centos container with "headless" VNC session…   76                                      [OK]
imagine10255/centos6-lnmp-php56    centos6-lnmp-php56                              50                                      [OK]
centos/mysql-57-centos7            MySQL 5.7 SQL database server                   45                                      
tutum/centos                       Simple CentOS docker image with SSH access      43                                      
openshift/base-centos7             A Centos7 derived base image for Source-To-I…   39                                      
gluster/gluster-centos             Official GlusterFS Image [ CentOS-7 +  Glust…   38                                      [OK]
centos/postgresql-96-centos7       PostgreSQL is an advanced Object-Relational …   37                                      
centos/python-35-centos7           Platform for building and running Python 3.5…   33                                      
kinogmt/centos-ssh                 CentOS with SSH                                 25                                      [OK]
openshift/jenkins-2-centos7        A Centos7 based Jenkins v2.x image for use w…   20                                      
centos/php-56-centos7              Platform for building and running PHP 5.6 ap…   18                                      
pivotaldata/centos-gpdb-dev        CentOS image for GPDB development. Tag names…   10                                      
openshift/wildfly-101-centos7      A Centos7 based WildFly v10.1 image for use …   6                                       
openshift/jenkins-1-centos7        DEPRECATED: A Centos7 based Jenkins v1.x ima…   4                                       
darksheer/centos                   Base Centos Image -- Updated hourly             3                                       [OK]
pivotaldata/centos                 Base centos, freshened up a little with a Do…   2                                       
pivotaldata/centos-mingw           Using the mingw toolchain to cross-compile t…   2                                       
blacklabelops/centos               CentOS Base Image! Built and Updates Daily!     1                                       [OK]
pivotaldata/centos-gcc-toolchain   CentOS with a toolchain, but unaffiliated wi…   1                                       
pivotaldata/centos7-build          CentosOS 7 image for GPDB compilation           0                                       
pivotaldata/centos7-test           CentosOS 7 image for GPDB testing               0                                       
smartentry/centos                  centos with smartentry                          0                                       [OK]
```
* 下载image到本地，以centos为例
```
docker pull centos
```
* 查看下载下来的image
```
root@iZ2ze5pt2475mgiqpzlh5kZ:~# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hello-world         latest              fce289e99eb9        3 weeks ago         1.84kB
centos              latest              1e1148e4cc2c        6 weeks ago         202MB
```

### 3.2 容器的使用
* 创建并启动容器
```
docker run -dt -p 8080:8888 centos /bin/bash
```
> -p 为指定容器的映射端口，8080为物理机端口，8888为容器端口 

* 查看容器id
```
root@iZ2ze5pt2475mgiqpzlh5kZ:~# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                           PORTS               NAMES
8d5ce4c43a91        centos              "/bin/bash"         2 hours ago         Exited (137) About an hour ago                       jovial_diffie
```
* 开始已退出的容器
```
docker start id(容器id)
```
* 进入容器
```
docker exec -ti xxx(容器id) /bin/bash
[root@8d5ce4c43a91 /]#
```

### 3.3 环境的配置及新容器的生成
进行容器内，安装Anaconda

```
yum install wget  # 安装wget
wget https://repo.anaconda.com/archive/Anaconda3-5.3.1-Linux-x86_64.sh  # 下载anaconda
chmod +x Anaconda3-5.3.1-Linux-x86_64.sh  # 添加可执行权限
sh Anaconda3-5.3.1-Linux-x86_64.sh  # 安装
```
> Anaconda根据步骤一进行安装即可。
> 报错tar: This does not look like a tar archive tar: Exiting with failure status due to previous errors 
> 安装 yum install -y bzip2

* Anaconda安装完成，保存为新的镜像
```
docker commit xxx(id) xxx(新的镜像名)
```
* 关闭并删除容器
```
docker stop xxx(id)  # 停止容器
docker rm xxx(id)  # 删除容器
```
* 重启启动容器
```
docker run -dt -p xxxx:xxxx centos_anaconda /bin/bash
```
> 挂接本地磁盘到容器 docker run -dt -p 7000-8000:7000-8000 -v /usr/local/home:/usr/local/container/home centos/java8 /bin/bash

* copy宿主机器的文件到容器：
```
docker cp /home/msg/name.tar xxx(id):/home/software
```
