---
title: Nginx深入使用-nginx如何处理请求
categories:
  - nginx
tags:
  - nginx官网文档
  - 翻译
  - How nginx processes a request
toc: true
author: 虢國技酱
comments: true
date: 2019-05-24 11:09:47
description:
original:
permalink:
---

在构建高并发、高性能应用时，nginx是一个非常简单和高效的方案之一。nginx作为HTTP和反向代理web服务器（也提供邮件服务），其优秀的架构设计、丰富的官方模块和第三方模块、master-worker进程模型、异步非阻塞请求处理、基于Linux的I/O复用等这些保证了nginx可以轻松应对十万以上并发连接请求。同时超低的内存，一般情况下，10000个非活跃http连接在nginx中仅消耗2.5MB的内存。nginx优秀的设计保证了其扩展性，so拥有极为丰富的第三方模块。高可靠，基于nginx核心框架代码的优秀设计、模块设计的简单性；另外master-worker进程模型也提高了nginx在单个worker进程出错时，其他worker处理不受影响以此保证其服务的高可靠。

本文就简单介绍和记录一下nginx的相关配置，在对处理http请求时的相关作用。
<!-- more -->


## 基于域名的虚拟主机
对一个http请求，nginx首先要决定使用哪个`server`来处理这个请求。先来看一个简单的nginx配置，其中包含了三个监听`*:80`端口的虚拟服务器
```nginx
server {
    listen      80;
    server_name example.org www.example.org;
    ...
}

server {
    listen      80;
    server_name example.net www.example.net;
    ...
}

server {
    listen      80;
    server_name example.com www.example.com;
    ...
}
```
在这个配置中，nginx只测试请求的头字段“Host”，以确定请求应该路由到哪个服务器。如果它的值("Host")不匹配任何服务器名，或者请求根本不包含这个头字段("Host")，那么nginx将把请求路由到这个端口的默认服务器。在上面的配置中，默认服务器是第一个——这是nginx的标准默认行为。还可以显式地设置哪个服务器应该是默认的，在`listen`指令中使用`default_server`参数:
```nginx
server {
    listen      80 default_server;
    server_name example.net www.example.net;
    ...
}
```
> **注意:** 
> * 从nginx 0.8.21版本开始使用`default_server`参数。在nginx 0.8.21之前的版本中，应该使用`default`参数。
> * `default_server`是侦听端口(`listen`)的属性，而不是服务器名称(`server_name`)的属性。

## 如何禁止处理未定义主机名Host的请求
如果不允许一个没有`Host`头信息的请求，则可以定义一个丢弃请求的服务`server`:
```nginx
server {
    listen      80;
    server_name "";
    return      444;
}
```
在这里，服务器名称(`server_name`)被设置为一个空字符串，它将匹配没有“Host”头信息的请求，并返回一个特殊的nginx非标准代码444来关闭连接。
> 从nginx 0.8.48版本开始，`server_name ""`是服务器名的默认设置，所以可以省略`server_name ""`配置指令。而在此之前的版本中，机器的主机名(`hostname`)用作默认服务器名。

## 基于域名和基于IP混合的虚机主机
让我们来看一个更复杂的配置，其中一些虚拟服务器监听不同的地址:
```nginx
server {
    listen      192.168.1.1:80;
    server_name example.org www.example.org;
    ...
}

server {
    listen      192.168.1.1:80;
    server_name example.net www.example.net;
    ...
}

server {
    listen      192.168.1.2:80;
    server_name example.com www.example.com;
    ...
}
```
在这个配置中，nginx首先根据`server`块的`listen`指令测试请求的IP地址和端口。然后，它根据匹配IP地址和端口的`server`块的`server_name`指令测试请求的`Host`头信息字段。如果没有找到服务器名，请求将由默认服务器处理。例如，在`192.168.1.1:80`端口上接收到的`www.example.com`请求将由`192.168.1.1:80`端口的默认服务器处理，即上面第一个服务器，因为在这个端口没有定义`www.example.com`。

如前所述，默认服务器(`default_server`)是监听端口(`listen`)的属性，可以为不同端口定义不同的默认服务器:
```nginx
server {
    listen      192.168.1.1:80;
    server_name example.org www.example.org;
    ...
}

server {
    listen      192.168.1.1:80 default_server;
    server_name example.net www.example.net;
    ...
}

server {
    listen      192.168.1.2:80 default_server;
    server_name example.com www.example.com;
    ...
}
```

## 一个简单的php站点配置
现在让我们看看nginx如何选择一个`location`来处理一个典型的、简单的PHP站点的请求:
```nginx
server {
    listen      80;
    server_name example.org www.example.org;
    root        /data/www;

    location / {
        index   index.html index.php;
    }

    location ~* \.(gif|jpg|png)$ {
        expires 30d;
    }

    location ~ \.php$ {
        fastcgi_pass  localhost:9000;
        fastcgi_param SCRIPT_FILENAME
                      $document_root$fastcgi_script_name;
        include       fastcgi_params;
    }
}
```
nginx首先搜索由字符串字面量给出的最特定的前缀`location`，而不考虑所列顺序。在上面的配置中，唯一的前缀`location`是“/”，因为它匹配任何请求，所以它将作为最后的手段使用。然后nginx按照配置文件中列出的顺序检查正则表达式定义的`location`。第一个匹配的表达式停止搜索，nginx将使用这个`location`。如果没有正则表达式匹配请求，则nginx使用前面找到的最特定的前缀`location`。

注意，所有类型的`location`只测试请求行中没有参数的URI部分。这是因为查询字符串中的参数可以由不同的几种方式给出，例如:
> /index.php?user=john&page=1     
> /index.php?page=1&user=john      

此外，任何人都可以在查询字符串中指定任何参数:
> /index.php?page=1&something+else&user=john

现在让我们看看在上面的配置中如何处理请求:
* 请求`/logo.gif`首先由前缀location `/`匹配，然后由正则表达式`\.(gif|jpg|png)$`匹配，因此由后一个`location`处理。根据`root /data/www`指令将请求映射到文件`/data/www/logo.gif`。然后这个文件被发送到客户端。
* 请求`/index.php`也先由前缀location  `/`匹配,然后由正则表达式`\.(php)$ `匹配。因此，它由后一个`location`处理，请求被传递给正在`localhost:9000`上监听的**FastCGI服务**。`fastcgi_param`指令将FastCGI参数`SCRIPT_FILENAME`设置为`/data/www/index`。然后**FastCGI服务**执行该文件。变量`$document_root`等于`root`指令的值，变量`$fastcgi_script_name`等于请求URI，即`/index.php`。
* 请求`/about.html`只匹配前缀location `/`，因此，它被这个`location`处理。使用`root /data/www`指令将请求映射到文件`/data/www/about.html`。然后文件被发送到客户端。
* 处理请求“/”更为复杂。它只由前缀location `/`匹配，因此，它由这个`location`处理。然后，`index`指令根据其参数值和`root /data/www`指令测试`index`文件是否存在。如果文件`/data/www/index.html`不存在，而文件`/data/www/index.php`存在，则指令执行内部重定向到`/index.php`。nginx再次搜索所有的`location`，就像请求是由客户端发送的一样。就像我们前面看到的，这个重定向请求最终将由**FastCGI服务**处理。


> 原文：[How nginx processes a request](http://nginx.org/en/docs/http/request_processing.html)