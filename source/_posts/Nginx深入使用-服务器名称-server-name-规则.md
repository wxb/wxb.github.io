---
title: Nginx深入使用-服务器名称(server_name)规则
categories:
  - nginx
tags:
  - nginx官网文档
  - 翻译
  - server_name
toc: true
author: 虢國技酱
comments: true
date: 2019-05-24 15:24:57
description:
original:
permalink:
---

服务器虚拟主机名称是使用`server_name`指令定义的，并确定由哪个`server`块来处理给定的请求。参见[nginx如何处理请求](https://wxb.github.io/2019/05/24/Nginx%E6%B7%B1%E5%85%A5%E4%BD%BF%E7%94%A8-nginx%E5%A6%82%E4%BD%95%E5%A4%84%E7%90%86%E8%AF%B7%E6%B1%82.html)。它们可以由**精确名称**、**通配符名称** 或 **正则表达式** 来定义:

<!-- more -->
```nginx
server {
    listen       80;
    server_name  example.org  www.example.org;
    ...
}

server {
    listen       80;
    server_name  *.example.org;
    ...
}

server {
    listen       80;
    server_name  mail.*;
    ...
}

server {
    listen       80;
    server_name  ~^(?<user>.+)\.example\.net$;
    ...
}
```
在按名称搜索虚拟服务器时，如果名称匹配多个`server`，例如同时匹配**通配符名称**和**正则表达式**，则将按以下优先顺序选择第一个:
  * 精确名称      
  * 以星号开头最长的通配符名称，例如`*.example.org”`      
  * 以星号结尾的最长通配符名称，例如`mail.*`             
  * 第一个匹配的正则表达式(按配置文件中配置的顺序)

## 通配符
通配符名称只能在名称的开始或结束处包含星号，并且只能在点号旁边包含星号。`www.*.example.org`和`w*.example.org`名称无效。但是，可以使用正则表达式指定这些名称，例如，`~^www\..+\.example\.org$`和`~^w.*\.example\.org$`。星号可以匹配多个名称部分。`*.example.org`不仅匹配`www.example.org`，而且匹配`www.sub.example.org`。

`.example.org`这个特殊通配符名称可用于匹配 **确切名称**`example.org` 和 **通配符名称**`*.example.org`。

## 正则表达式
nginx使用的正则表达式与Perl编程语言(PCRE)使用的正则表达式兼容。**要使用正则表达式，服务器名称必须以波浪号字符开头**:
```nginx
server_name  ~^www\d+\.example\.net$;
```

否则，它将被视为**确切名称**，或者如果表达式包含星号，则被视为**通配符名称**(而且很可能是无效的名称)。不要忘记设置`^`和`$`***锚***。在语法上它们不是必需的，但在逻辑上是必需的。还要注意，域名中的**点**应该用反斜杠转义。应引用包含字符的 正则表达式中包含`{`和`}`字符时应使用引号括起来:
```nginx
server_name  "~^(?<name>\w\d{1,3}+)\.example\.net$";
```
否则，nginx会启动失败并显示这样的错误信息：
```nginx
directive "server_name" is not terminated by ";" in ...
```

一个命名正则表达式捕获可以稍后用作变量:
```nginx
server {
    server_name   ~^(www\.)?(?<domain>.+)$;

    location / {
        root   /sites/$domain;
    }
}
```
PCRE库使用以下语法支持命名捕获:
> `?<name>`	  Perl 5.10 compatible syntax, supported since PCRE-7.0
> `?'name'`	  Perl 5.10 compatible syntax, supported since PCRE-7.0
> `?P<name>`	Python compatible syntax, supported since PCRE-4.0

如果nginx启动失败并且显示这样错误：
```nginx
pcre_compile() failed: unrecognized character after (?< in ...
```
这意味着PCRE库是版本太老，应该换成`?P<name>`语法试试。这些捕获也可以以数字形式使用:
```nginx
server {
    server_name   ~^(www\.)?(.+)$;

    location / {
        root   /sites/$2;
    }
}
```
但是，这种用法应该仅限于简单的情况(如上面所述)，因为数字引用很容易被覆盖。

## 各式各样的虚拟主机名
有一些服务器名称是经过特殊处理的。
如果需要在`server`块中处理没有“Host”头字段(不是默认值)的请求，则应指定一个空名称:
```nginx
server {
    listen       80;
    server_name  example.org  www.example.org  "";
    ...
}
```
如果`server`块中没有定义`server_name`，那么nginx使用空名称("")作为`server_name`。
> 在本例中，nginx 0.8.48之前版本使用机器的主机名作为服务器名。

如果服务器名定义为“$hostname”(0.9.4)，则使用机器的主机名。

如果某人使用IP地址而不是服务器名发出请求，“Host”请求头字段将包含该IP地址，可以使用该IP地址作为服务器名处理该请求:
```nginx
server {
    listen       80;
    server_name  example.org
                 www.example.org
                 ""
                 192.168.1.1
                 ;
    ...
}
```
在匹配所有服务器名示例中，可以看到奇怪的名称“_”:
```nginx
server {
    listen       80  default_server;
    server_name  _;
    return       444;
}
```
这个名字没有什么特别之处，它只是无数无效域名中的一个，这些域名永远不会与任何真实的名字交叉。其他无效的名称，如“——”和“!”@#”同样可以使用。

0.6.25之前的nginx版本支持特殊名称“*”，这个名称被错误地解释为一个匹配所有服务器名称。它从来没有充当通配符或通配符服务器名。相反，它提供了`server_name_in_redirect`指令现在提供的功能。现在不推荐使用特殊的名称“*”，应该使用`server_name_in_redirect`指令。请注意，无法使用`server_name`指令指定全面性名称或默认服务器。这是`listen`指令的属性，而不是`server_name`指令的属性。参见“nginx如何处理请求”。可以定义监听端口*:80和*:8080的服务器，并将其中一个端口*:8080作为默认服务器，而另一个端口*:80:
```nginx
server {
    listen       80;
    listen       8080  default_server;
    server_name  example.net;
    ...
}

server {
    listen       80  default_server;
    listen       8080;
    server_name  example.org;
    ...
}
```

## 优化
精确名称、以星号开头的通配符名称和以星号结尾的通配符名称存储在绑定到侦听端口的三个哈希表中。哈希表的大小在配置阶段进行了优化，以便能够找到一个CPU缓存丢失最少的名称。设置哈希表的详细信息在单独的[文档](http://nginx.org/en/docs/hash.html)中提供。

首先搜索**确切名称**哈希表。如果没有找到名称，则搜索**以星号开头的通配符名称**的哈希表。如果没有找到该名称，则搜索以**星号结尾的通配符名称**的哈希表。

搜索通配符名称哈希表比搜索精确名称哈希表要慢，因为通配符名称是由域部分搜索的。注意，特殊的通配符形式“.example.org”存储在通配符散列表中，而不是**精确名称**散列表中。

正则表达式是按顺序测试的，因此是最慢的方法，并且不可伸缩。

由于这些原因，最好在可能的情况下使用确切的名称。例如，如果服务器最常被请求的名称是`example.org`和`www.example.org`，那么显式地定义它们会更有效:
```nginx
server {
    listen       80;
    server_name  example.org  www.example.org  *.example.org;
    ...
}
```
而不是使用简化形式:
```nginx
server {
    listen       80;
    server_name  .example.org;
    ...
}
```

如果定义了大量的服务器名，或者定义了异常长的服务器名，那么可能需要在http级别调优[server_names_hash_max_size](http://nginx.org/en/docs/http/ngx_http_core_module.html#server_names_hash_max_size)和[server_names_hash_bucket_size](http://nginx.org/en/docs/http/ngx_http_core_module.html#server_names_hash_bucket_size)指令。`server_names_hash_bucket_size`指令的默认值可能等于32、64或其他值，这取决于CPU缓存线的大小。如果默认值是32，并且服务器名定义为“too.long.server.name.example.org”，那么nginx将无法启动并显示错误消息:
```sh
could not build the server_names_hash,
you should increase server_names_hash_bucket_size: 32
```
在这种情况下，指令值应该增加到2的次幂:
```sh
http {
    server_names_hash_bucket_size  64;
    ...
```
如果定义了大量的服务器名，则会出现另一条错误消息:
```sh
could not build the server_names_hash,
you should increase either server_names_hash_max_size: 512
or server_names_hash_bucket_size: 32
```

在这种情况下，首先尝试将[server_names_hash_max_size](http://nginx.org/en/docs/http/ngx_http_core_module.html#server_names_hash_max_size)设置为接近服务器名称数量的数字。只有当这不起作用，或者nginx的启动时间长到无法接受时，才尝试增加[server_names_hash_bucket_size](http://nginx.org/en/docs/http/ngx_http_core_module.html#server_names_hash_bucket_size)。

如果服务器是侦听端口的唯一服务器，那么nginx根本不会测试服务器名(也不会为侦听端口构建哈希表)。然而，有一个例外。如果服务器名是带捕获的正则表达式，那么nginx必须执行该表达式才能获得捕获。


> 原文：[Server names](http://nginx.org/en/docs/http/server_names.html)