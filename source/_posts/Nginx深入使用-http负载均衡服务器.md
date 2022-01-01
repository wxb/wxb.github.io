---
title: Nginx深入使用-http负载均衡服务器
categories:
  - nginx
tags:
  - nginx官网文档
  - 翻译
  - 负载均衡
toc: true
author: 虢國技酱
comments: true
date: 2019-05-27 09:25:52
description:
original:
permalink:
---
负载平衡是一种通过多个应用程序实例,以达到优化资源利用率、最大化吞吐量、减少延迟和确保容错配置的常用的技术.    

nginx作为一个非常有效的HTTP负载均衡器，将流量分配到多个应用服务器，并使用nginx提高web应用程序的性能、可伸缩性和可靠性。

<!-- more -->

## 负载均衡策略
nginx支持以下负载均衡策略:
* `round-robin`——对应用服务器的请求以**循环**方式分发，
* `least-connected`--下一个请求将分配给**活跃连接最少**的服务器，
* `ip-hash`-- 散列函数用于确定下一个请求应该选择哪个服务器(基于客户机的IP地址)。。


## 默认负载均衡配置
nginx进行负载均衡的一个最简单的配置如下: 
```nginx
http {
    upstream myapp1 {
        server srv1.example.com;
        server srv2.example.com;
        server srv3.example.com;
    }

    server {
        listen 80;

        location / {
            proxy_pass http://myapp1;
        }
    }
}
```
在上面的负载均衡配置示例中，同一个应用程序有3个实例运行在`srv1-srv3`上。如果没有特别配置负载平衡方法，则**默认为轮询**。所有请求都代理到服务器组`myapp1`, nginx应用HTTP负载平衡来分发请求。 

nginx中反向代理实现包括了: `HTTP`、`HTTPS`、`FastCGI`、`uwsgi`、`SCGI`、`memcached`和`gRPC`的负载平衡。   

要为`HTTPS`而不是`HTTP`配置负载平衡，只需使用`“HTTPS”`作为协议。

在为`FastCGI`、`uwsgi`、`SCGI`、`memcached`或`gRPC`设置负载平衡时，分别使用对应的`fastcgi_pass`、`uwsgi_pass`、`scgi_pass`、`memcached_pass`和`grpc_pass`指令。

## 最少连接负载均衡
另一个负载平衡原则是最小连接:`least-connected`。在某些请求需要更长的时间才能完成的情况下，最小连接允许控制应用程序实例上的负载更加平衡。

使用最少连接的负载平衡，nginx将尝试不让繁忙的应用程序服务器负载过多的请求，而是将新请求分发给不那么繁忙的服务器。

当使用`least_conn`指令作为服务器组配置的一部分时，nginx将激活最小连接策略来平衡应用实例的负载:
```nginx
upstream myapp1 {
    least_conn;
    server srv1.example.com;
    server srv2.example.com;
    server srv3.example.com;
}
```

## 持久会话负载均衡
请注意,当nginx的负载均衡策略是:**轮询**或**最少连接**时, 每个客户端后续的请求都有可能被分发到不同的上游应用服务器实例。这种情况下无法保证相同的客户机的请求总是被分发到相同的上游服务器实例。

如果需要将客户机请求绑定到特定的上游应用程序服务器实例，换句话说，使客户机的会话具有“粘性”或“持久性”，以便客户端请求始终被分发到特定的服务器—那么可以使用`ip-hash`负载平衡策略。

使用`ip-hash`时，将客户机的IP地址用作散列键，以确定应该为客户机的请求分发到服务器组中的哪个上游应用服务器实例。此负载均衡策略确保同一客户机的请求总是指向同一上游应用服务器实例，除非该上游应用服务器实例不可用。

要配置`ip-hash`负载均衡，只需将`ip_hash`指令添加到服务器(上游)组配置中:
```nginx
upstream myapp1 {
    ip_hash;
    server srv1.example.com;
    server srv2.example.com;
    server srv3.example.com;
}
```

## 指定权重负载均衡
还可以通过使用服务器权重进一步影响nginx负载平衡算法。

在上面的示例中，没有配置服务器权重，这意味着对于特定的负载平衡策略，所有指定的上游服务器都被视为相同的权重。

特别是对于轮询，在有足够多的请求并且当请求以统一的方式处理并且足够快地完成时，它还意味着请求在服务器之间的分布大致相同。

当为一个上游服务器实例指定[weight](http://nginx.org/en/docs/http/ngx_http_upstream_module.html#server)权重参数时，该权重将作为负载平衡决策的一部分进行计算。
```nginx
upstream myapp1 {
    server srv1.example.com weight=3;
    server srv2.example.com;
    server srv3.example.com;
}
```
在上面这种配置时，每5个新请求将在应用程序实例之间分布如下:3个请求将定向到`srv1`，一个请求将定向到`srv2`，另一个请求将定向到`srv3`。

在nginx的最新版本中，最少连接`least-connected `和`ip-hash`负载平衡策略中同样可以使用权重。

## 健康检查

nginx中的反向代理实现包括带内(或被动)服务器健康检查。如果来自某个特定的上游服务器实例的响应出现错误而失败，nginx将把该服务器标记为失败，并暂时避免为后续入站请求选择该服务器。

[max_fail](http://nginx.org/en/docs/http/ngx_http_upstream_module.html#server)指令用来设置:在[fail_timeout](http://nginx.org/en/docs/http/ngx_http_upstream_module.html#server)指令指定的时间内，nginx尝试与上游服务器实例连续通信的失败次数。默认情况下，`max_fail`被设置为1。当将其设置为0时，将禁用此服务器的健康检查。`fail_timeout`参数定义了上游服务器实例被标记为失败的时间。在服务器故障之后的`fail_timeout`间隔之后，nginx将开始用活动客户机的请求优雅地探测服务器。如果探测成功，则将该上游服务器实例标记为活跃的(正常的)实例。


## 扩展阅读
此外，nginx中还有更多控制服务器负载平衡的指令和参数，例如[proxy_next_upstream](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_next_upstream)、[backup](http://nginx.org/en/docs/http/ngx_http_upstream_module.html#server)、[down](http://nginx.org/en/docs/http/ngx_http_upstream_module.html#server)和[keepalive](http://nginx.org/en/docs/http/ngx_http_upstream_module.html#keepalive)。更多信息请查看nginx的[参考文档](http://nginx.org/en/docs/)。