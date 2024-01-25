---
layout: wiki
title: Linux
categories: Tools
description: Linux小技巧
keywords: Linux, Ubuntu
---



# Linux小技巧

* **卸载软件**

  ```sh
  sudo dpkg -r xxx
  ```

* **开机自启动**

  ```
  # 安装supervisor
  sudo apt install supervisor
  
  # 新建启动脚本
  sudo gedit /etc/supervisor/conf.d/xxx.conf
  
  # 编辑脚本内容
  ; 设置进程的名称，使用 supervisorctl 来管理进程时需要使用该进程名
  [program:xxxTask]
  ; 使用venv下的Python去启动脚本
  command=/home/uw/anaconda3/bin/python ipDetectReport.py
  ; 进入哪个目录执行command
  directory=/home/uw/
  user=uw
  numprocs=1
  autostart=true
  autorestart=false
  ; logs目录提前创建好
  stdout_logfile=/home/uw/cron_tasks/logs/supervisor-out.log
  stdout_logfile_backups=10
  ; 错误日志记得输出，这样启动如果报错容易看出来
  stderr_logfile=/home/uw/cron_tasks/logs/supervisor-error.log
  redirct_stderr=true
  startsecs=1
  stopasgroup=true
  
  # 加载、刷新启动脚本
  supervisorctl update        //配置文件修改后使用该命令加载新的配置
  supervisorctl reload        //重新启动配置中的所有程序
  supervisorctl status        //查看所有进程的状态
  supervisorctl stop es       //停止es
  supervisorctl start es      //启动es
  supervisorctl restart       //重启es
  ```

* **获取设备序列号**

  ```shell
  echo password | sudo - S dmidecode -t 1
  ```

* **获取U盘序列号**

  ```shell
  ll /dev/disk/by-id
  ```

* **设置文件不可修改、删除重建**

  ```shell
  sudo chattr + i filename
  ```

* **终端弹窗：**

  ```shell
  * apt install terminator
  * terminator -e 'ls;read'
  ```

* **error: subprocess-exited-with-error**

  ```shell
  sudo apt-get install build-essential
  ```

* **跨平台系统监控器：**

  ```
  https://www.oschina.net/p/Bottom
  ```

### VirtualBox

**命令行安装与卸载：**

```shell
sudo apt-get update
sudo apt install virtualbox
sudo apt-get remove virtualbox*
```

> https://blog.csdn.net/qq_39257814/article/details/125506511

**创建虚拟机:**



