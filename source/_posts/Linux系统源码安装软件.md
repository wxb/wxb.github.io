---
title: Linux系统源码安装软件
categories:
  - Linux
tags:
  - autoconf
  - automake
  - Makefile
  - configure
toc: true
author: 虢國技酱
comments: true
date: 2017-04-23 00:41:08
description:
original:
permalink:
---
当我们习惯了Windows系统的软件安装以后，再学习和使用Linux系统上的软件安装时，真是两个字 **头大**！可是仔细想想Linux上也有好用的软件安装办法啊！  
那就是 **yum** 和 **apt-get**，但你是否发现这些工具有时候或者说很多时候无法满足你的需要，要不提示没有你要安装的软件，不要安装的软件版本太老！而且对于一些通用的、工具类的命令软件按照`yum install`/`apt-get install`还好，但是对于一些个性化软件，想要规范安装目录和结构，这些工具真是不能满足需求啊！所以在Linux系统上安装软件，还是掌握最原始的安装方法才是最根本、最直接的办法！所以今天就来学习学习源码安装到底是怎么一回事。

Linux系统上首先得清楚：命令 == 软件；所以当我们说安装一个命令和安装一个软件是一样的意思。其次，在Linux系统上所有的命令/软件本质上都是文件，不同的是它们是经过编译的二进制可运行文件；而开发者通常开发出来的都是通过高级语言编写文本文件，需用通过编译器编译生成机器认识的二进制文件。

<!-- more -->

# source源码安装软件

## 阅读README的说明

## 一般规律


# autoconf命令和configure文件
## autoconf是什么？
GNU Autoconf - Creating Automatic Configuration Scripts

# automake命令，make命令和Makefile文件



# 参考资料
* [GNU Autoconf](http://www.gnu.org/software/autoconf/autoconf.html#documentation)
* [GNU automake](http://www.gnu.org/software/automake/)
* [automake,autoconf使用详解](http://www.laruence.com/2009/11/18/1154.html)
