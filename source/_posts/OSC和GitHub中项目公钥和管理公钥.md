title: OSC和GitHub中项目公钥和管理公钥
categories:
  - git
tags:
  - github
  - 配置
  - 公钥
toc: true
author: 虢國技酱
comments: true
date: 2016-11-11 17:53:25
description:
original:
permalink:
---
对于开源托管网站GitHub大家应该不陌生吧，以前一直设置的是github上面的ssh公钥，一直没有发生什么问题，今天在遇到一个问题：git同时把代码push到两个远程仓库。所以就在github和osc上建立了两个测试远程仓库，但是osc我没有经常使用，所以有配置了一次ssh公钥，但是，提交的时候一直‘拒绝提交’。很想不通，我试着 `ssh -T git@git.oschina.net` ，结果 **welcome** 了，clone也可以，最后在发现我设置的是项目部署公钥！ 汗！！！！
<!-- more -->

在此做个记录:

# OSC

* 在你的个人资料中有一个SSH公钥，
![](http://images0.cnblogs.com/blog2015/537027/201507/221913431316594.png)

* 2、是在你的每个项目中都有一个部署公钥管理（部署公钥配置后的机器，只支持clone与pull等只读操作.）。
![](http://images0.cnblogs.com/blog2015/537027/201507/221911413659826.png)

# GitHub
下面这两个的作用和上面是对应的

* ssh keys
![](http://images0.cnblogs.com/blog2015/537027/201507/221914011788480.png)

* Deploy keys
![](http://images0.cnblogs.com/blog2015/537027/201507/221911247095182.png)

# 总结一下

* 第一种公钥相当于设置管理员权限一样，你可以clone、pull、【push】

* 第二种是项目公钥，为了安全起见只允许，clone和pull

* 所以如果当你想要push时代码到远程库不要密码时，那就设置公钥到第一中
