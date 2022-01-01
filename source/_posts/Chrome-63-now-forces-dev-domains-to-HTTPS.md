---
title: Chrome 63 now forces .dev domains to HTTPS
categories:
  - chrome
tags:
  - google
  - 转载
toc: true
author: 虢國技酱
comments: true
date: 2018-01-04 16:35:24
description:
original:
permalink:
---


![](/images/google/google-chrome.png)

<!-- more -->

Starting today(`DECEMBER 07, 2017`), Google Chrome is rolling out v63 that now forces all .dev domains to use HTTPS. There is no workaround and they are apparently not changing this. Back in September Mattias Geniar wrote a post warning every one of the changes, and his post is a great primer on what the Chrome team has done.

This will affect everyone using .dev and if you are using Valet the recommended fix is to switch to a new unused extension like .test. To change this in valet you can run the following:

```
valet domain test
```

Based on this article by Danny Wahl he recommends you use one of the following: “.localhost”, “.invalid”, “.test”, or “.example”.

If all this sounds like too much trouble another viable option is to switch to Firefox as your development browser. It’s fast, has comfortable dev tools, and has really made a ton of improvements over the past few years.

> 转自：[Chrome 63 now forces .dev domains to HTTPS](https://laravel-news.com/chrome-63-now-forces-dev-domains-https)

---

从今天起(2017-12-07), Chrome 浏览器推出的**v63** 版本会强制使所有`.dev`域的访问使用 **HTTPS** 协议。
