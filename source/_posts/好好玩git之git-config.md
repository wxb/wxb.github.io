title: 好好玩git:git-config
categories:
  - git
tags:
  - 好好玩git
toc: true
author: 虢國技酱
comments: true
date: 2016-11-03 14:15:00
description:
original:
permalink:
---
![](/images/git/0.png)
git是什么？关于git的身世和作用本篇不做赘述，网上相关资料很多，有兴趣的小伙本可以google一下。简单的说git是一个 **分布式** 版本控制软件，和大家熟知的svn一样都是版本控制软件，但是git无论功能和设计理念都先进了很多，特别是这个 **分布式** ！
<!-- more -->

# 我是如何爱上git的？

2014年开始工作，在一家对日外包公司使用svn进行团队代码控制。就记住了Windows上那个🐢，简单了学习了一下如何操作。但是一年多的使用感觉就是那个样，玩不出什么花样！作为一个有想法爱折腾的程序猿当然对svn无感了，后来当我听同事提到版本控制还有一个 **git**，回家立马了解了一下，原来这东西是Linux之父 **Linus Torvalds** 开发出来的，也知道了git和BitKeeper的故事，更加崇拜我神：Linus了！大神就是不给我用这个软件，那我就重新写一个自己的！真是羡慕死我等屌丝，偶像的力量是强大的！虽然我还不知道git到底够不够优秀，但是我已经爱上了它。

# 好好玩~心态

学会使用一个工具很简单，但是要用好一个工具就要花费更多的精力！无论是对于各种的开发语言还是各种工具，我总是抱着一种 **玩** 的心态，好好玩，玩溜了，玩精了，玩到自己都崇拜自己的地步！

工作和生活中学会适当的调节很重要，**闲时要有吃紧的心思，忙里要有偷闲的乐趣** 。所以当我们在工作中遇到不清楚的问题，不能因为进度时间的原因而忘记去学习和研究：问题本身的原因和相关一系列自己不清楚的知识。学习这些相关的一系列知识从而解决这个问题就像砸开一个彩蛋一样，惊喜，自豪和满足是其他不能比的，千万不要随便百度出来一个文章照着操作来解决问题，最起码自己要知道这篇博客说的是我的问题，可以试试！要知道从搜索引擎返回的万千答案中找出符合自己的问题也是一项能力。

那么如何玩呢？玩的范围很重要，就以本篇git为例。你可能已经知道git也就是那么几个名利 `add`,`commit`,`push`,`pull`, 但是现在遇到一个现实的问题是：每次在代码自动合并的时候进入都是emacs编辑器，而你只会使用vim。怎么办？要去再git的手册过一遍吗？当然不是，这样就太浪费时间了。此时要锁定这肯定是配置的问题，好好玩玩Config就可；范围相对较小，花费时间较少，成果来的直接。这样的碎片知识积累在时间的作用下必将慢慢的在你的脑海中形成知识网，连城一片，也就到了质变到了精通的火候。

好好玩，就是专向学习，大胆试验，归纳总结；就是一遍一遍的玩，一遍不熟再继续多玩几遍，就像玩游戏，别给自己太高的目标，就是玩，让自己放松下来。

# git config

## git config 三个配置级别：
* `system`-系统级: 在git安装以后，git的默认配置项都在这里；
    `$ git config --system -l` 列出系统中git系统级配置信息：
    ![](/images/git/1.png)

    `$ git config --system -e` 编辑git系统级配置信息,也可以直接编辑`/etc/gitconfig`这个文件，如果没有这个文件就用 `find / -name gitconfig` 查找一下；我在我的Ubuntu和mac都没有找到这个`/etc/gitconfig`文件，就是用find查找到的。使用命令和直接编辑文件效果都是相同的，但是推荐使用命令。
    ![](/images/git/3.png)

* `global`-全局级：登录用户全局级别的git配置
    `$ git config --global -l` 列出git用户全局级别配置信息：
    ![](/images/git/4.png)

    `$ git config --global -e` 编辑git用户全局级别配置信息,也可以直接编辑`~/.gitconfig`这个文件，
    ![](/images/git/5.png)

* `local` -仓库级: 对不同的仓库进行自定义配置
    `$ git config --local -l` 列出git仓库级别配置信息：
    ![](/images/git/6.png)

    `$ git config --local -e` 编辑git仓库级别配置信息,也可以直接编辑`.git/config`这个文件，
    ![](/images/git/7.jpg)
    那么这里的 user.name 和 user.email配置很重要吗？有什么影响？不配置是不是提交不成功？查看下文第二个问题示例

这三个级别配置的内容都是相同的，不同的是优先级；同样的配置信息的优先级是：`local` > `global` > `system`; 也就是说，针对同样的某个配置，如果同时存在在`local`和`global`中时，`local`中的配置将会覆盖`global`中的配置项的值，当然也会同样覆盖`system`中的配置项；这样做的好处是：既方便共用，又可以个性化自定义。遵循的理念是：对于所有用户都通用的配置项放在`system`中；对于每个独立的用户相对的共用项放在`global`;对于某个仓库特殊的配置放在`local`

## git config 相关命令
* git config -h 查看帮助
* git config -l 列出所有配置信息
* git config -e 编辑配置
* git config --get 获取某个配置项
    ![](/images/git/10.jpg)
* git config --add 增加一项配置
    `$ git config --local --add alias.ls "status"`
    ![](/images/git/11.jpg)
* git config --unset 取消指定设置
    `$ git config --local --unset alias.ls`
* git --rename-section 重命名section
    `$ git config --local --rename-section alias rename`
* git --remove-section 删除section
    `$ git config --local --remove-section rename`
    ![](/images/git/12.jpg)

**注意：**
上面所有的命令都可以通过增加一个参数：`--system`,`--global`,`--local` 来指定是对哪一级别的设置

## 列举两个相关问题解决

* 配置git的editor文本编辑器

    Git默认会调用你的环境变量editor定义的值作为文本编辑器

    有些同学可能遇到过，当我们pull拉去代码合并操作时需要增加说明信息，我对vi很熟悉，但是如果遇到git进入了一个你没见过的editor文本编辑器：eg. emacs
    听说过emacs应该知道，emacs是通过C-c C-v等组合键来操作的。我对emacs不是很熟悉，习惯了使用vi，那么怎样配置git的默认文本编辑器呢？
    ```bash
    $ git config --local core.editor vi   配置默认编辑器
    $ git config --local -l               查看配置
    ```
    ![](/images/git/14.jpg)


* 配置仓库提交账户
    在我们有时候提交的commit history中出现如图所示的情况：
    ![](/images/git/13.jpg)
    都是我自己提交的代码，为什么中间的某一次和其他几次都不一样？不是我自己提交的吗？
    提交肯定是我提交的，只是这次特殊的提交是在工作的Ubuntu机子上提交的。而在Ubuntu机子上没有进行`local`仓库级的配置，只有公司GitLab的`global`级的配置通用配置：
     ```
     user.name=wangxiaobo
     user.email=wangxiaobo@moguzx.com
     ```
     但是我的github应该是:

     ```
     user.name=wxb
     user.email=wxb0328@icloud.com
     ```

     所以在提交以后github系统中并不存在 `user.name=wangxiaobo`,`user.email=wangxiaobo@moguzx.com`信息，所有就没有头像了

    user.name 和 user.email配置还有有什么作用?
    其实这里的配置并不重要，跟你提交能不能提交成功是没有关系的。只是在你通过公钥免密提交时，这里的这个配置就会在对应的github或者GitLab系统中找到这个账号的相关信息，比如头像！
    对我这些装逼的人就是不至于丢失下面的这些
    ![](/images/git/8.jpg)
    ![](/images/git/9.jpg)

    解决问题：
    ```shell
    $ git config --local user.name "wxb"
    $ git config --local user.email "wxb0328@iclooud.com"
    ```

    github 关于贡献统计方法说明：[how we count contributions](https://help.github.com/articles/why-are-my-contributions-not-showing-up-on-my-profile/)

# 注意
git config 所有的命令都存在三个配置级别：`system`,`global`,`local`。可以通过指定一个参数：`--system`,`--global`,`--local` 来指定是对哪一级别的设置

# 帮助资料

* [github](https://github.com) 最大的代码托管站点，在这里可以找到非常多非常优秀的源代码
* [Pro Git](https://git-scm.com/book/zh/v1) 全面学习git的一本好书
* [try git](http://try.github.io/) 在线学习git相关指令的网站
* [man git](https://www.kernel.org/pub/software/scm/git/docs/) git命令帮助手册（英文）
