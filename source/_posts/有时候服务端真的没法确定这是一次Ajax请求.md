title: 有时候服务端真的没法确定这是一次Ajax请求
categories:
  - ajax
  - http
tags:
  - ajax
  - http
  - XMLHttpRequest
toc: true
author: 虢國技酱
comments: true
date: 2017-03-09 01:49:46
description:
original:
permalink:
---
![](/images/ajax/ajax.png)

对于web开发来说，ajax请求是不是感觉非常熟悉，是不是觉得对http来说应该分个：普通请求和ajax请求呢？是不是认为后端程序可以根据`X-Request-With`来判断请求是不是ajax？事实上，确实后端确实是这样做的，百度一下基本都是这答案；对于熟悉thinkphp的php开发者也应该知道tp的`isAjax`(5.0)或`IS_AJAX`(3.2)判断方法本质上也是`$_SERVER['HTTP_X_REQUESTED_WITH']`，那这个判断到底靠谱吗？基于`XMLHttpRequest`的ajax请求都会发送`X-Request-With`头吗？

<!-- more -->

# Ajax

AJAX = Asynchronous JavaScript and XML（异步的 JavaScript 和 XML）。名字里虽然包含了XML，但ajax数据格式却不限于XML！
> 异步 JavaScript + XML, 本身不是一种技术, 是在2005年由 Jesse James Garrett 提出的一个术语, 描述了一种“新”的结合使用大量已经存在的技术的方式, 包括: HTML 或 XHTML, CSS, JavaScript, DOM, XML, XSLT, 还有最重要的 XMLHttpRequest 对象.
当这些技术以Ajax模型的方式集合起来，web应用可以更快地，无需加载整个页面就能更新用户界面。这使应用能更快地响应用户行为.
摘自：[MDN AJAX](https://developer.mozilla.org/zh-CN/docs/AJAX)

Ajax最初是由Google使用起来的，在Google搜索时，在搜索框输入关键字时，JavaScript 会把这些字符发送到服务器，然后服务器会返回一个搜索建议的列表。随后又在Google地图上使用，大大提升了用户体验和性能。

# XMLHttpRequest

Ajax实现最重要的一个依赖就是：**XMLHttpRequest**，一个web提供的API对象。
> XMLHttpRequest 是一个API, 它为客户端提供了在客户端和服务器之间传输数据的功能。它提供了一个通过 URL 来获取数据的简单方式，并且不会使整个页面刷新。这使得网页只更新一部分页面而不会打扰到用户。XMLHttpRequest 在 AJAX 中被大量使用。
> XMLHttpRequest 是一个 JavaScript 对象，它最初由微软设计,随后被 Mozilla、Apple 和 Google采纳. 如今,该对象已经被 W3C组织标准化. 通过它,你可以很容易的取回一个URL上的资源数据. 尽管名字里有XML, 但 XMLHttpRequest 可以取回所有类型的数据资源，并不局限于XML。 而且除了HTTP ,它还支持file 和 ftp 协议.
摘自：[MDN XMLHttpRequest](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest)

注意是**web API**，也就是只在浏览器环境中可以使用，跟JavaScript没有一毛钱的关系！证明一下：
* 浏览器上new个XMLHttpRequest对象
![](/images/ajax/webxhr.png)
成功！
* node中试试？不都是写JavaScript吗！
![](/images/ajax/nodexhr.png)
失败！

所以明白`XMLHttpRequest`是浏览器提供给我们的接口这个概念了吧！浏览器提供了很多接口，有兴趣可以去瞅瞅 [Web API 接口](https://developer.mozilla.org/zh-CN/docs/Web/API)

# angular的http是基于xhr，后端却不知道
其实，问题出现就是今天前端的小伙伴在使用angular的http发生ajax请求时，后端的php却没有检查出来！起初以为angular的http不是基于XMLHttpRequest的，查看手册：
> 现代浏览器支持两种基于 HTTP 的 API： XMLHttpRequest (XHR) 和 JSONP。少数浏览器还支持 Fetch。
> Angular HTTP 库简化了 XHR 和 JSONP API 的编程，这就是本章所要讲的。
> 摘自 [angular手册](https://angular.cn/docs/ts/latest/guide/server-communication.html#!#fetch-data)

写个例子看看：
```js

    var app = angular.module('myApp', []);
    app.controller('siteCtrl', function($scope, $http) {
        $http.get("http://boss.local.com/Account/login")
                .success(function (response) {$scope.names = response.sites;});
    });
```
看看结果：
![](/images/ajax/ngxhr.png)
确实是XMLHttpRequest发送的ajax请求，但是后端却没有`HTTP_X_REQUESTED_WITH`信息。

ok，我们手动设置一下：
```js
    // 设置X-Requested-With头信息
    var app = angular.module('myApp', []);
    app.controller('siteCtrl', function($scope, $http) {
        $http.get("http://boss.local.com/Account/login",{
            headers : {'X-Requested-With' : 'XMLHttpRequest'}
        })
                .success(function (response) {$scope.names = response.sites;});
    });
```
这下，后端就可以获取`HTTP_X_REQUESTED_WITH`信息了:
![](/images/ajax/ngxhr1.png)

# jquery是怎么做的？
先看看jQuery的手册上怎么说的：
> headers (default: {})
Type: PlainObject
An object of additional header key/value pairs to send along with requests using the XMLHttpRequest transport. The header X-Requested-With: XMLHttpRequest is always added, but its default XMLHttpRequest value can be changed here. Values in the headers setting can also be overwritten from within the beforeSend function. (version added: 1.5)

**注意**： `The header X-Requested-With: XMLHttpRequest is always added`，所以说使用jQuery的ajax类方法默认会设置`XMLHttpRequest`头信息，所以是jquery的使用导致我们以为，所有的ajax的请求就会自带`XMLHttpRequest`头信息！

就不上示例了，更详细的信息，大家查看官方手册：[jQuery API](http://api.jquery.com/jQuery.ajax/)

# 那么原生的JavaScript发生ajax请求呢？

写个JavaScript原生的ajax请求，看看后端是否可以获取`X-Request-With`
```js
    var xmlHttp=null;

    function showHint(str)
    {
        if (str.length==0)
        {
            document.getElementById("txtHint").innerHTML="";
            return;
        }
        try
        {// Firefox, Opera 8.0+, Safari, IE7
            xmlHttp=new XMLHttpRequest();
        }
        catch(e)
        {// Old IE
            try
            {
                xmlHttp=new ActiveXObject("Microsoft.XMLHTTP");
            }
            catch(e)
            {
                alert ("Your browser does not support XMLHTTP!");
                return;
            }
        }
        var url="/Account/login?q=" + str;
        url=url+"&sid="+Math.random();
        xmlHttp.open("GET",url,false);
        xmlHttp.send(null);
        document.getElementById("txtHint").innerHTML=xmlHttp.responseText;
    }
```
后端php打印`$_SERVER`:
```php
    var_dump($_SERVER); die;
```
结果：
![](/images/ajax/dump.png)
可以看到并没有：`HTTP_X_REQUESTED_WITH`信息，后端这下就不知道这是XMLHttpRequest请求了，但是浏览器很清楚：
![](/images/ajax/dump2.png)

我们利用`setRequestHeader`在xmlHttp.send(null)前设置一下，
```js
...
xmlHttp.open("GET",url,false);
xmlHttp.setRequestHeader('X-Request-With', 'XMLHttpRequest');
xmlHttp.send(null);
...
```
结果：
![](/images/ajax/dump1.png)

**所以，对原生JavaScript利用XMLHttpRequest发生请求，后端获取到的并不是一定准确的，很容易被伪造！而对http来说X-Request-With这样的头信息也只是普通的信息，http遇到这个信息没有任何表示，还是一次正常的http请求**

# 总结

所以说，ajax或者说XMLHttpRequest发送的一次http请求，和普通的请求根本没有任何区别，对http协议来说，做的都是一样的事情；如果要说一点区别那可能就是`X-Request-With`这个头信息吧。如果没有这个头信息，除了浏览器谁也不知道这是一次ajax请求！

当然，正是由于XMLHttpRequest是web API，浏览器提供的一个接口，让我们在不刷新页面的前提下发送一次http请求，但是为了安全防止伪造，特别是在同源策略下，浏览器对XMLHttpRequest的请求做了一些限制，我们可以修改某些header头信息，但是有部分头信息是不允许修改的，浏览器会自动添加：（关于这些头信息的意义大家请仔细学习http）

* Accept-Charset
* Accept-Encoding
* Connection
* Content-Length
* Cookie
* Cookie2
* Content-Transfer-Encoding
* Date
* Expect
* Host
* Keep-Alive
* Referer
* TE
* Trailer
* Transfer-Encoding
* Upgrade
* User-Agent
* Via

最后，要说的是：如果你需要在后端区分Ajax提交，那就务必确认你所使用的前端框架设置了`X-Request-With`这个头信息，如果是写的原生JavaScript的话，那就一定要设置`xhr.setRequestHeader('X-Request-With', 'XMLHttpRequest')`

ok,解决了问题，做个记录，欢迎大家拍砖！
