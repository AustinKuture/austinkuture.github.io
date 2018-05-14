---
layout: wiki
title: Pycharm个性化
categories: pycharm
description: pycharm 模板信息
keywords: php
---


# Pycharm个性化

pythcarm使用过程中，每次新建文件时自动添加作者、日期、版权等信息
### 1. 打开pycharm,选择File -> Settings

![Pycharm00](/images/WiKi/Pycharm00.png)
### 2. 选择Editor -> Code Style -> File and Code Templates
![Pycharm01](/images/WiKi/Pycharm01.png)
### 3. 模板变量
（a）shebang行

#!/usr/bin/python3
```
（a）shebang
 #!/usr/bin/python3
（b）预定义的变量要扩展为格式为$ {<variable_name>}的相应值。

可用的预定义文件模板变量为：

$ {PROJECT_NAME} - 当前项目的名称。

$ {NAME} - 在文件创建过程中在“新建文件”对话框中指定的新文件的名称。

$ {USER} - 当前用户的登录名。

$ {DATE} - 当前的系统日期。

$ {TIME} - 当前系统时间。

$ {YEAR} - 今年。

$ {MONTH} - 当月。

$ {DAY} - 当月的当天。

$ {HOUR} - 目前的小时。

$ {MINUTE} - 当前分钟。

$ {PRODUCT_NAME} - 将在其中创建文件的IDE的名称。

$ {MONTH_NAME_SHORT} - 月份名称的前3个字母。 示例：1月，2月等

$ {MONTH_NAME_FULL} - 一个月的全名。 示例：1月，2月等
```
### 实例
```
#coding=utf-8
@header ${NAME}.py
@abstract   
            ###
            MyBlog: http://www.kuture.com.cn
@author  Created by Kuture on ${DATE}
@version 1.0.0 ${DATE} Creation(###)

Copyright © ${YEAR}年 Mr.Li All rights reserved

```
效果如下：
![Pycharm02](/images/WiKi/Pycharm02.png)
