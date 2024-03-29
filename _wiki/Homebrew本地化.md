---
layout: wiki
title: Homebrew本地化
categories: Homebrew
description: HomeBrew本地化
keywords: Homebrew，Mac

---

# Homebrew本地化



Homebrew 对于使用 Mac 的开发者来说，是再熟悉不过的了，它可以在 macOS  中方便的安装和管理各种系统并不自带的开发包。但令人苦恼的是很多时候它的下载和更新速度太慢，让人非常头疼，今天 Gitee  为各位推荐的就是在国内自动安装 Homebrew 的脚本。

项目名称：HomebrewCN

项目作者：CunKai

项目地址：https://gitee.com/cunkai/HomebrewCN

脚本内容

/bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh)"

只需要把这段脚本内容复制到「终端」中即可。

脚本效果

![img](https://pics0.baidu.com/feed/96dda144ad345982c21d9c55e2a3f5abc9ef84c4.jpeg?token=c6ffd4eeccd043f40adb9176db0f6965)

运行后我们可以看到，共有 4 个下载源可供选择，这时输入选择下载源的序号即可。

![img](https://pics7.baidu.com/feed/2fdda3cc7cd98d10ff43fdddd5687c087aec9022.jpeg?token=8771cfe5b7797d62dce64f74f7166a92)

这时我们就可以看到速度有多么的快了，Gitee 表示安装 Homebrew 从来没有这么舒爽过！

常见错误说明

（转载自作者知乎专栏：https://zhuanlan.zhihu.com/p/111014448）

1.Mac 10.11系统版本以下的（包括10.11），brew官方已经停止对这类老系统的支持。

2.如果遇到报错中含有errno 54 / 443 / 的问题：

这种一般切换源以后没有问题，因为都是公益服务器，不稳定性很大。

3.检测到你不是最新系统，需要自动升级Ruby后失败的：

rm -rf /Users/$(whoami)/Library/Caches/Homebrew/brew -v

如果还失败运行下面的脚本：

/bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/UpdateRuby.sh)"

4.如果报错 command not found : brew

先运行下面命令看是否能出来Homebrew的版本号（结果看倒数3句）

/usr/local/Homebrew/bin/brew -v      

再运行设置临时PATH的代码：

export PATH=/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbinbrew -v

如果能用就是电脑PATH配置问题，重启终端运行echo $PATH打印出来自己分析一下。

5.如果brew -v没有报错 ， brew update出错的：

这种不影响使用，尝试再次运行brew update可能赶上服务器不稳定的一瞬间。

6.brew有一个自检程序，如果有问题自检试试：

/usr/local/bin/brew doctor