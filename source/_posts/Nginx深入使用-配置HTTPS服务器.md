---
title: Nginx深入使用-配置HTTPS服务器
categories:
  - null
tags:
  - null
toc: true
author: 虢國技酱
comments: true
date: 2019-05-27 09:26:18
description:
original:
permalink:
---

要配置HTTPS服务器，必须在服务器`server`块的监听套接字`listen`上启用`ssl`参数，并指定服务器证书[server certificate](http://nginx.org/en/docs/http/ngx_http_ssl_module.html#ssl_certificate)和私钥[private key](http://nginx.org/en/docs/http/ngx_http_ssl_module.html#ssl_certificate_key)文件的位置:
```nginx
server {
    listen              443 ssl;
    server_name         www.example.com;
    ssl_certificate     www.example.com.crt;
    ssl_certificate_key www.example.com.key;
    ssl_protocols       TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers         HIGH:!aNULL:!MD5;
    ...
}
```

服务器证书是一个公共实体。它被发送到连接到服务器的每个客户机。私钥是一个安全的实体，应该存储在具有受限访问权限的文件中，但是，它必须能够被nginx的主进程读取。私钥可以交替地存储在与证书相同的文件中:
```nginx
ssl_certificate     www.example.com.cert;
ssl_certificate_key www.example.com.cert;
```
在这种情况下，文件访问权限也应该受到限制。虽然证书和密钥存储在一个文件中，但是只有证书被发送到客户机。



<!-- more -->

# 优化HTTPS服务器

# SSL证书链

# 一个单独的HTTP/HTTPS服务器


# 基于名称的HTTPS服务器

# 具有多个名称的SSL证书

# 服务器名称显示