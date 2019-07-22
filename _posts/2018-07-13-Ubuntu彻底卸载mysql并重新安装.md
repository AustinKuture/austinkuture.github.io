---
layout: post
title: Ubuntu彻底卸载mysql并重新安装
categories:
  - Linux
description: Ubuntu彻底卸载mysql并重新安装
keywords: 'Linux,mysql,彻底卸载mysql,mysql重新安装'
comments: true
---

# Ubuntu彻底卸载mysql并重新安装
删除mysql:

```
sudo apt-get remove mysql-*
```

然后清理残留的数据:

```
dpkg -l |grep ^rc|awk '{print $2}' |sudo xargs dpkg -P
```

> 跳出对话框时选择yes

重新安装mysql

```
sudo apt-get install mysql-client mysql-server
```

> 安装时根据提示设置root密码

检查安装状态：

```
sudo service mysql status
```



