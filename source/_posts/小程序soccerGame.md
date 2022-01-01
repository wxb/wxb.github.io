title: 小程序soccerGame
categories:
  - wechat
tags:
  - 小程序
  - wechat
toc: true
author: 虢國技酱
comments: true
date: 2017-03-11 20:47:03
description:
original:
permalink:
---

微信小程序公开以后，关于小程序的话题就非常火爆！趁着年假对小程序进行了简单的了解和学习。`soccerGame`这个小项目也属于学习练手的项目，模仿**肆客足球**APP的赛程样式配合申请的**聚合数据**API提供的足球信息完成这个小程序

<!-- more -->


# 涉及小程序技术
* wxml、wxss页面相关知识
* 小程序发送网络请求API接口：wx.request
* 小程序数据绑定
* 小程序事件操作
* 小程序生命周期
* 小程序数据缓存（本地存储）

# 完成后app样式
![](/images/wechat/7.jpg)
![](/images/wechat/8.jpg)
![](/images/wechat/9.jpg)

# 源码
源码就不贴在这里了，有兴趣的小伙伴请到我我的github：[soccerGame](https://github.com/wxb/wechatApp/tree/master/soccerGame) 就可以找到源码

# 开发总结
* 官方给小程序的手册算是比较友好，一般情况下，简单的刷一遍官方提供的手册，基本开发没有太大问题。主要学习的就是wxml和wxss，组件和API都有详细说明，根据开发的需要灵活组合即可
* 转变传统开发思维，比如在使用`this.setData()`数据绑定，和我们传统web开发直接操作DOM有所不同、事件操作时传递参数都和传统开发方式有所不同
* 代码组织形式，小程序目前对文件目录结构进行了相关的要求，但是这种要求对我们组织代码结构并不是特别友好，需要对小程序进行深入研究，完成一套开发友好的代码组织模式
* js功底要求，小程序完全使用js开发，对ES6也兼容；所以如果想要快速开发，还是需要有不错的js功底

小程序开发总体来说入门要求不是特别高，主要是转变传统开发理念和对js精通很重要！
