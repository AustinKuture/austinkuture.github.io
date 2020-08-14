---
layout: post
title: 添加Mac的Time Machine备份到smb网络硬盘
categories:
  - Technology
description: 添加Mac的Time Machine备份到smb网络硬盘
keywords: 'SMB, Time Machine, 硬盘, 网盘'
comments: true
---

# 添加Mac的Time Machine备份到smb网络硬盘
家里的极路由支持smb协议，也就是windows 共享文件夹。路由器上插上移动硬盘，然后装上局域网文件中转站插件即可。

Mac 支持Time machine备份，于是今天闲来无事，就做一下系统备份。过程也不是很复杂，看下面的截图吧。[<sup>1</sup>](#refer-anchor-1)

1. 首先打开“磁盘工具”。

![](https://img3.doubanio.com/view/note/l/public/p41844520.jpg)

2. 在“磁盘工具”中，移到最上面的菜单栏，选择文件->新建映像->空白映像

![](https://img3.doubanio.com/view/note/l/public/p41844551.jpg)

3. 在弹出来的配置窗口中，填入如下信息。注意选择映像格式为“稀疏磁盘映像”，设置大小为1TB。名称可以自己设定，最好不要用中文，支持空格，但后面配置命令行的时候要注意路径。配置完之后，点击“存储”。

![](https://img1.doubanio.com/view/note/l/public/p41844569.jpg)

4. 点击存储后，大概几十秒钟的样子，建立成功。这个时候，你可以在“文稿”中看到这个镜像“backup.sparseimage”，并且在finder中，应该能看到已经挂载了该镜像。如果挂载了，先推出这个镜像。没有的话，就忽略该步骤。

![](https://img3.doubanio.com/view/note/l/public/p41844863.jpg)

5. 把back.sparseimage通过网络拷贝到samba共享文件夹下。然后双击打开它。接下来，你可以在设备列表中看见“backup”挂载。如果没有看到，可以点击xxx的Macbook Pro，看是否在列表中，在的话，可以拖出来到左边设备列表中。我这里就遇到了这个问题。

![](https://img1.doubanio.com/view/note/l/public/p41846688.jpg)

6. 接下来，有点麻烦，需要用到命令行，但是只有一行命令，不需要太过于担心。打开终端。在终端里输入：

![](https://img9.doubanio.com/view/note/l/public/p41846775)

在这里容我啰嗦一句，如果之前的名称中有空格，在输入的时候切记要加上"\", 比如取得名字是“mac backup”，那么上面的backup的路径是“mac\ backup”。

7. 成功后，打开Time Machine设置，就能看到backu磁盘了。勾上“自动备份”选项以及“在菜单栏中显示Time Machine”。

![](https://img3.doubanio.com/view/note/l/public/p41847030.jpg)

基本的设置就是这样。这样的备份只是一种非常规的Time Machine使用，基于现有的条件使用。这种备份没办法解决系统崩溃无法开机的情况。还是建议定期使用移动硬盘作备份，并且妥善保管之。另外，在我之前的使用中，出现了网络临时断开后备份报错，到后面怎么也无法再次备份的情况，只能删掉挂载的分区重新建立，所以，这种情况还会带来这种无法预知的问题。大家用的时候还是要慎重。[<sup>2</sup>](#refer-anchor-2)



**参考文献及引用**

* [1] [灰铁（来自豆瓣）]
* [2] [豆瓣]

> 本文图像及相关信息参考于网络文章，如涉侵权，联删。

