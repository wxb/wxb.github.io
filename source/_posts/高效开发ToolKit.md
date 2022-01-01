---
title: 高效开发ToolKit
categories:
  - 工具
tags:
  - 工具
  - 扩展库
  - awesome
toc: true
author: 虢國技酱
comments: true
date: 2021-01-29 10:51:13
description:
original:
permalink:
---

![](/images/tool/toolkit.png)

<!-- more -->


## awesome

* [sindresorhus/awesome](https://github.com/sindresorhus/awesome) Awesome lists about all kinds of interesting topics

* shell 
    * [alebcay/awesome-shell](https://github.com/alebcay/awesome-shell)
        > A curated list of awesome command-line frameworks, toolkits, guides and gizmos. Inspired by awesome-php. 

* golang
    * [avelino/awesome-go](https://github.com/avelino/awesome-go)  
        > A curated list of awesome Go frameworks, libraries and software https://awesome-go.com/

    * [jobbole/awesome-go-cn](https://github.com/jobbole/awesome-go-cn)
        > Go 资源大全中文版， 内容包括：Web框架、模板引擎、表单、身份认证、数据库、ORM框架、图片处理、文本处理、自然语言处理、机器学习、日志、代码分析、教程和（电子）书等。
        
* php
    * [ziadoz/awesome-php](https://github.com/ziadoz/awesome-php)  
        > A curated list of amazingly awesome PHP libraries, resources and shiny things.

    * [kahun/awesome-sysadmin](https://github.com/kahun/awesome-sysadmin)
        > A curated list of amazingly awesome open source sysadmin resources inspired by Awesome PHP.
        
    * [JingwenTian/awesome-php](https://github.com/JingwenTian/awesome-php) 
        > 收集整理一些常用的PHP类库, 资源以及技巧. 以便在工作中迅速的查找所需...
        
    * [chiraggude/awesome-laravel](https://github.com/chiraggude/awesome-laravel)
        > A curated list of bookmarks, packages, tutorials, videos and other cool resources from the Laravel ecosystem
        
* OpenResty
    * [bungle/awesome-resty](https://github.com/bungle/awesome-resty)  
        > A List of Quality OpenResty Libraries, and Resources. 

* vscode 
    * [viatsko/awesome-vscode](https://github.com/viatsko/awesome-vscode) 
        > A curated list of delightful Visual Studio Code packages and resources. For more awesomeness, check out awesome. 

## golang

* go周边
    * 生成唯一Gopher图片 [gopherize](https://gopherize.me/) 
        > Generate a Gopher pic that's as unique as you 

    * [gopherkon](https://quasilyte.dev/gopherkon/)

    * [egonelbre/gophers](https://github.com/egonelbre/gophers)
    
    * [petermattis/goid](https://github.com/petermattis/goid)
        > 获取当前goroutine id

* 代码组织
    * [golang-standards/project-layout
](https://github.com/golang-standards/project-layout) Standard Go Project Layout

* 网络与框架
    * [valyala/fasthttp](https://github.com/valyala/fasthttp)
        > Fast HTTP package for Go. Tuned for high performance. Zero memory allocations in hot paths. Up to 10x faster than net/http 

    * [gorilla/mux](https://github.com/gorilla/mux)
        > A powerful HTTP router and URL matcher for building Go web servers with 🦍 http://www.gorillatoolkit.org/pkg/mux 

    *  [gorilla/websocket](https://github.com/gorilla/websocket) A WebSocket implementation for Go.
        > Gorilla WebSocket is a Go implementation of the WebSocket protocol.
        
    * [golang/protobuf](github.com/golang/protobuf/proto)
        > Go support for Protocol Buffers - Google's data interchange format
        
    * [micro/go-micro](https://github.com/micro/go-micro) A Go microservices development framework
        > go 微服务框架

    * [coredns/coredns](https://github.com/coredns/coredns)
        > CoreDNS is a DNS server/forwarder, written in Go, that chains plugins. Each plugin performs a (DNS) function.

    * [go-kratos/kratos](https://github.com/go-kratos/kratos)
        > Kratos是bilibili开源的一套Go微服务框架，包含大量微服务相关框架及工具。

* 路由
    * [julienschmidt/httprouter](https://github.com/julienschmidt/httprouter) 
        > A high performance HTTP request router that scales well

* 并发
    * [golang/sync](https://github.com/golang/sync) Go官方提供的“sync”和“sync/atomic”之外的 并发原语 扩展包 
        > This repository provides Go concurrency primitives in addition to the ones provided by the language and "sync" and "sync/atomic" packages.    
        > https://pkg.go.dev/golang.org/x/sync
        * [errgroup](https://pkg.go.dev/golang.org/x/sync@v0.0.0-20201207232520-09787c993a3a/errgroup) 将一个通用的父任务拆成几个小任务并发执行的场景，其实，将一个大的任务拆成几个小任务并发执行，可以有效地提高程序的并发度
          > *Package errgroup provides synchronization, error propagation, and Context cancelation for groups of goroutines working on subtasks of a common task.*
        * [semaphore](https://pkg.go.dev/golang.org/x/sync@v0.0.0-20201207232520-09787c993a3a/semaphore) 
          > *Package semaphore provides a weighted semaphore implementation.*
        * [singleflight](https://pkg.go.dev/golang.org/x/sync@v0.0.0-20201207232520-09787c993a3a/singleflight) 
          > *Package singleflight provides a duplicate function call suppression mechanism.*
        * [syncmap](https://pkg.go.dev/golang.org/x/sync@v0.0.0-20201207232520-09787c993a3a/syncmap) 
          > *Package syncmap provides a concurrent map implementation.*

    * [neilotoole/errgroup](https://github.com/neilotoole/errgroup)
         > neilotoole/errgroup is a drop-in alternative to Go's wonderful sync/errgroup but limited to N goroutines. This is useful for interaction with rate-limited APIs, databases, and the like.
         
    * [marusama/cyclicbarrier](https://github.com/marusama/cyclicbarrier) 循环栅栏:CyclicBarrier允许一组 goroutine 彼此等待，到达一个共同的执行点。同时，因为它可以被重复使用，所以叫循环栅栏。具体的机制是，大家都在栅栏前等待，等全部都到齐了，就抬起栅栏放行
        > CyclicBarrier is a synchronizer that allows a set of goroutines to wait for each other to reach a common execution point, also called a barrier.

    * [Jeffail/tunny](https://github.com/Jeffail/tunny) A goroutine pool for Go
        > Tunny is a Golang library for spawning and managing a goroutine pool, allowing you to limit work coming from any number of goroutines with a synchronous API. 

    * [panjf2000/ants](https://github.com/panjf2000/ants) 🐜⚡️A high-performance goroutine pool for Go, inspired by fasthttp.  
        > ants是一个高性能的协程池，实现了对大规模goroutine的调度管理、goroutine复用，允许使用者在开发并发程序的时候限制协程数量，复用资源，达到更高效执行任务的效果。
        
    * 并发安全map [orcaman/concurrent-map](https://github.com/orcaman/concurrent-map)
        > a thread-safe concurrent map for go

    * 高并发框架 [LMAX-Exchange/disruptor](https://github.com/LMAX-Exchange/disruptor)   
        > High Performance Inter-Thread Messaging Library
        > [disruptor](https://lmax-exchange.github.io/disruptor/)

* 缓存及数据库
    * [lib/pq](https://github.com/lib/pq)
        > Pure Go Postgres driver for database/sql http://godoc.org/github.com/lib/pq

    * [go-pg/pg](https://github.com/go-pg/pg)
        > Golang ORM with focus on PostgreSQL features and performance http://godoc.org/github.com/go-pg/pg

    * [go-redis/redis](https://github.com/go-redis/redis)
        > Type-safe Redis client for Golang

    * [gomodule/redigo](https://github.com/gomodule/redigo)
        > Redigo is a Go client for the Redis database.

    * [jinzhu/gorm](github.com/jinzhu/gorm) 
        > The fantastic ORM library for Golang, aims to be developer friendly https://gorm.io
        
    * [Shopify/sarama](https://github.com/Shopify/sarama)
        > Sarama is a Go library for Apache Kafka 0.8, and up. https://shopify.github.io/sarama
    
    * [coocood/freecache](https://github.com/coocood/freecache)
        > A cache library for Go with zero GC overhead and high concurrent performance

    * [golang/groupcache](https://github.com/golang/groupcache) 缓存框架
        > groupcache is a distributed caching and cache-filling library, intended as a replacement for a pool of memcached nodes in many cases.

    * [cockroachdb/cockroach](https://github.com/cockroachdb/cockroach)
        > CockroachDB is a distributed SQL database built on a transactional and strongly-consistent key-value store. It scales horizontally; survives disk, machine, rack, and even datacenter failures with minimal latency disruption and no manual intervention; supports strongly-consistent ACID transactions; and provides a familiar SQL API for structuring, manipulating, and querying data.

* 配置操作包
    * [ini](https://ini.unknwon.io/) 超赞的 Go 语言 INI 文件操作 
    * [goconfig](https://github.com/unknwon/goconfig/blob/master/README_ZH.md) 一个易于使用，支持注释的 Go 语言配置文件解析器 

* 定时任务
    * [cron](https://github.com/robfig/cron)  a cron library for go 
    * [beego/task](https://github.com/astaxie/beego/tree/develop/toolbox) beego task 
* 认证
    * [jwt-go](https://github.com/dgrijalva/jwt-go)  JSON Web令牌（JWT） 

* 测试
    * [smartystreets/goconvey](https://github.com/smartystreets/goconvey) GoConvey is awesome Go testing  
        > Go testing in the browser. Integrates with `go test`. Write behavioral tests in Go. http://goconvey.co 

    * [stretchr/testify](https://github.com/stretchr/testify) Testify - Thou Shalt Write Tests  
        > A toolkit with common assertions and mocks that plays nicely with the standard library
        > * assert
        > * require
        > * mock      
            > [vektra/mockery](https://github.com/vektra/mockery):A mock code autogenerator for golang  
        > * suite
        
    * [appleboy/gofight](https://github.com/appleboy/gofight) API Handler Testing for Golang Web framework. 
        > Testing API Handler written in Golang.
        
    * [golang/mock](https://github.com/golang/mock) GoMock is a mocking framework for the Go programming language. 
        > GoMock is a mocking framework for the Go programming language. It integrates well with Go's built-in testing package, but can be used in other contexts too.
        
    * [bouk/monkey](https://github.com/bouk/monkey)
        > Monkey patching in Go https://bou.ke
        
    * [onsi/ginkgo](https://github.com/onsi/ginkgo) BDD Testing Framework for Go http://onsi.github.io/ginkgo/
    
    * [DATA-DOG/go-sqlmock](https://github.com/DATA-DOG/go-sqlmock) Sql mock driver for golang to test database interactions
        > sqlmock is a mock library implementing sql/driver. Which has one and only purpose - to simulate any sql driver behavior in tests, without needing a real database connection. It helps to maintain correct TDD workflow.
        
    * [jarcoal/httpmock](https://github.com/jarcoal/httpmock) HTTP mocking for Golang
        > Easy mocking of http responses from external resources.
    
* API文档
    * [go-swagger/go-swagger](https://github.com/go-swagger/go-swagger) 
        > Swagger 2.0 implementation for go https://goswagger.io  

    * [swaggo/gin-swagger](https://github.com/swaggo/gin-swagger)
        > gin middleware to automatically generate RESTful API documentation with Swagger 2.0.
    
* 工具箱toolkit
    * [Unknwon/com](https://github.com/Unknwon/com) Common Functions 
        > This is an open source project for commonly used functions for the Go programming language.  

    * [google/uuid](https://github.com/google/uuid) 
        > Go package for UUIDs based on RFC 4122 and DCE 1.1: Authentication and Security Services.
        
    * [uniplaces/carbon](https://github.com/uniplaces/carbon)   
        > Carbon for Golang, an extension for Time
        
    * [wcharczuk/go-chart](https://github.com/wcharczuk/go-chart)
        > go chart is a basic charting library in native golang.
        
    * [gonum/plot](https://github.com/gonum/plot)
        > A repository for plotting and visualizing data
        
    * [go-daily-lib](https://github.com/darjun/go-daily-lib) 
        > 每日一库
        
        * [thedevsaddam/gojsonq](github.com/thedevsaddam/gojsonq)
        * [tidwall/gjson](github.com/tidwall/gjson)
        * [tidwall/sjson](github.com/tidwall/sjson)
        
* 热编译工具
    * [gravityblast/fresh](https://github.com/gravityblast/fresh)
        > Build and (re)start go web apps after saving/creating/deleting source files.

    * [cosmtrek/air](https://github.com/cosmtrek/air)
        > Live reload for Go apps 
        
    * [silenceper/gowatch](github.com/silenceper/gowatch)
        > gowatch is a command line tool that builds and (re)starts your go project everytime you save a Go or template file.||Go程序热编译工具，提升开发效率
        
    * [codeskyblue/fswatch](https://github.com/codeskyblue/fswatch)
        > Watch file change, and trigger commands. (Cross platform)

* 依赖注入
    * [google/wire](https://github.com/google/wire) 
        > Compile-time Dependency Injection for Go
    
    * [uber-go/dig](https://github.com/uber-go/dig)   
        > A reflection based dependency injection toolkit for Go.
    
    * [facebookarchive/inject](https://github.com/facebookarchive/inject)    
        > Package inject provides a reflect based injector.  
        

* 命令行工具
    * [spf13/cobrav](https://github.com/spf13/cobra) A Commander for modern Go CLI interactions  
        > Cobra is both a library for creating powerful modern CLI applications as well as a program to generate applications and command files. 

    * [tomnomnom/gron](https://github.com/tomnomnom/gron)
        > Make JSON greppable!
        
        
* 类型转换
    * [mailru/easyjson](https://github.com/mailru/easyjson) Fast JSON serializer for golang.
        > Package easyjson provides a fast and easy way to marshal/unmarshal Go structs to/from JSON without the use of reflection. In performance tests, easyjson outperforms the standard encoding/json package by a factor of 4-5x, and other JSON encoding packages by a factor of 2-3x. 

    * [mitchellh/mapstructure](https://github.com/mitchellh/mapstructure) Go library for decoding generic map values into native Go structures.
        > mapstructure is a Go library for decoding generic map values to structures and vice versa, while providing helpful error handling.

* 日志

    * [sirupsen/logrus](https://github.com/sirupsen/logrus) Structured, pluggable logging for Go. 
        > Logrus is a structured logger for Go (golang), completely API compatible with the standard library logger.
        
    * [uber-go/zap](https://github.com/uber-go/zap) Blazing fast, structured, leveled logging in Go.
        > Uber推出的一个快速、结构化的分级日志库
        
    * [cihub/seelog](https://github.com/cihub/seelog) Seelog is a native Go logging library that provides flexible asynchronous dispatching, filtering, and formatting.
        > 灵活的异步调度、格式化和过滤功能
        
* 文件处理

    * [jung-kurt/gofpdf](https://github.com/jung-kurt/gofpdf) A PDF document generator with high level support for text, drawing and images     
        > 注意：该库不支持中文！！！
        
    * [signintech/gopdf](https://github.com/signintech/gopdf) A simple library for generating PDF written in Go lang
        > 注意：支持中文

* 错误与异常处理
    * [pkg/errors](https://github.com/pkg/errors)  Simple error handling primitives        
        >  Package errors provides simple error handling primitives.

* 参数验证
    * [asaskevich/govalidator](https://github.com/asaskevich/govalidator)  
        > [Go] Package of validators and sanitizers for strings, numerics, slices and structs
    
    * [go-playground/validator](https://github.com/go-playground/validator)  
        > Go Struct and Field validation, including Cross Field, Cross Struct, Map, Slice and Array diving 


## php

* composer
    * [hirak/prestissimo](https://github.com/hirak/prestissimo) 
        > Composer 多线程下载加速 

* Laravel
    * [chiraggude/awesome-laravel](https://github.com/chiraggude/awesome-laravel)
        > A curated list of bookmarks, packages, tutorials, videos and other cool resources from the Laravel ecosystem
    
    * [alexeymezenin/laravel-best-practices](https://github.com/alexeymezenin/laravel-best-practices)
        > Laravel最佳实践 

    * [swooletw/laravel-swoole](https://github.com/swooletw/laravel-swoole)
        > High performance HTTP server based on Swoole. Speed up your Laravel or Lumen applications.

    * [hhxsv5/laravel-s](https://github.com/hhxsv5/laravel-s)
        > LaravelS is an out-of-the-box adapter between Swoole and Laravel/Lumen.
        
    * [z-song/laravel-admin](https://github.com/z-song/laravel-admin)
        > Build a full-featured administrative interface in ten minutes https://laravel-admin.org
        
    * [Maatwebsite/Laravel-Excel](https://github.com/Maatwebsite/Laravel-Excel)
        > 🚀 Supercharged Excel exports and imports in Laravel https://laravel-excel.com
        
    * [barryvdh/laravel-debugbar](https://github.com/barryvdh/laravel-debugbar)
        > Laravel Debugbar (Integrates PHP Debug Bar)
        
    * [the-control-group/voyager](https://github.com/the-control-group/voyager)    
        > Voyager - The Missing Laravel Admin https://voyager.devdojo.com

    * [spatie/laravel-permission](https://github.com/spatie/laravel-permission)
        > Associate users with roles and permissions 
        
    * [barryvdh/laravel-ide-helper](https://github.com/barryvdh/laravel-ide-helper)
        > Laravel IDE Helper
        
    * [fruitcake/laravel-cors](https://github.com/fruitcake/laravel-cors)
        > Adds CORS (Cross-Origin Resource Sharing) headers support in your Laravel application
    
* Swoole
    * [easyswoole](https://www.easyswoole.com/)  
        > EasySwoole 是一款基于Swoole Server 开发的常驻内存型的分布式PHP框架，专为API而生，摆脱传统PHP运行模式在进程唤起和文件加载上带来的性能损失。 EasySwoole 高度封装了 Swoole Server 而依旧维持 Swoole Server 原有特性，支持同时混合监听HTTP、自定义TCP、UDP协议，让开发者以最低的学习成本和精力编写出多进程，可异步，高可用的应用服务 

    * [Hyperf](https://hyperf.io/)  = Hyperspeed + Flexibility
        > The Way to PHP Microservice


## MySQL
* [dbcli/mycli](https://github.com/dbcli/mycli) 
    > A command line client for MySQL that can do auto-completion and syntax highlighting.      
    >A Terminal Client for MySQL with AutoCompletion and Syntax Highlighting. http://mycli.net

## 效率工具

* 命令行工具
    
    * [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh) 
    * [powerlevel9k](https://github.com/Powerlevel9k/powerlevel9k)
    * [iterm2-material-design](https://github.com/MartinSeeler/iterm2-material-design)
    * [lsd](https://github.com/Peltoche/lsd)
    * [colorls](https://github.com/athityakumar/colorls)
    * [stedolan/jq](https://github.com/stedolan/jq)
    * [tidwall/jj](https://github.com/tidwall/jj)
    * 命令行瑞士军[m-cli](https://github.com/rgcr/m-cli)

## 网关 

* Lua
    * [Kong/kong](https://github.com/Kong/kong) The Cloud-Native API Gateway 
        > Kong is a cloud-native, fast, scalable, and distributed Microservice Abstraction Layer (also known as an API Gateway, API Middleware or in some cases Service Mesh). Made available as an open-source project in 2015, its core values are high performance and extensibility.    
        > ***kong dashborad: [pantsel/konga](https://github.com/pantsel/konga)***      
        > ***点评: 开源版本功能基本够用***
        
    * [apache/incubator-apisix](https://github.com/apache/incubator-apisix)
        > Cloud-Native Microservices API Gateway https://www.iresty.com   

* Java
    * [Netflix/zuul](https://github.com/Netflix/zuul) 
        > Zuul is a gateway service that provides dynamic routing, monitoring, resiliency, security, and more.    
        > ***点评: 用Spring Cloud全家桶首选***

* golang 
    * RESTful HTTP API 转 gRPC 反向代理网关： [grpc-ecosystem/grpc-gateway](https://github.com/grpc-ecosystem/grpc-gateway) gRPC to JSON proxy generator following the gRPC HTTP spec   
        > The grpc-gateway is a plugin of the Google protocol buffers compiler protoc. It reads protobuf service definitions and generates a reverse-proxy server which 'translates a RESTful HTTP API into gRPC.

    * API网关： [TykTechnologies/tyk](https://github.com/TykTechnologies/tyk)  Tyk Open Source API Gateway written in Go
        > Tyk is a lightweight, open source API Gateway and Management Platform enables you to control who accesses your API, when they access it and how they access it. Tyk will also record detailed analytics on how your users are interacting with your API and when things go wrong.       
        > ***点评: 功能较全,但是授权方面是个问题***
        
    * HTTP API 网关: [fagongzi/gateway](https://github.com/fagongzi/gateway) An HTTP API Gateway
        > Gateway is a restful API gateway based on HTTP, which can be used as a unified API access layer.      
        > ***点评: 全开源,自主可控性高***
        
    * [devopsfaith/krakend](https://github.com/devopsfaith/krakend) Ultra performant API Gateway with middlewares
        > An open framework to assemble ultra performance API Gateways with middlewares; core service of the KrakenD API Gateway.
        
    * [henrylee2cn/teleport](https://github.com/henrylee2cn/teleport) 
        > Teleport is a versatile, high-performance and flexible socket framework. It can be used for RPC, micro services, peer-peer, push services, game services and so on.
        
    * 悟空 API 网关 [eolinker/goku-api-gateway](https://github.com/eolinker/goku-api-gateway) A Powerful HTTP API Gateway in pure golang！
        > Goku API Gateway is a Golang-based microservice gateway that enables high-performance dynamic routing, multi-tenancy management, API access control, etc. It's also suitable for API management under micro-service system.      
        > ***点评:开源版基本不能投产,商业版功能丰富***
        
    * [haxpax/gosms](https://github.com/haxpax/gosms) Your own local SMS gateway in Go
    
        
## 服务网格
* golang
    * [hashicorp/consul](https://github.com/hashicorp/consul)  
        > Consul is a distributed, highly available, and data center aware solution to connect and configure applications across dynamic, distributed infrastructure. 

* java
    * [Netflix/eureka](https://github.com/Netflix/eureka)  AWS Service registry for resilient mid-tier load balancing and failover.
        > Eureka is a REST (Representational State Transfer) based service that is primarily used in the AWS cloud for locating services for the purpose of load balancing and failover of middle-tier servers. 

* k8s
    * [istio/istio](https://github.com/istio/istio)  Connect, secure, control, and observe services. https://istio.io   
        > An open platform to connect, manage, and secure microservices. 

## APM应用性能监控
*  [cat](https://github.com/dianping/cat) CAT 作为服务端项目基础组件，为美团点评各业务线提供系统丰富的性能指标、健康状况、实时告警等。

* [skywalking](https://github.com/apache/skywalking) APM, Application Performance Monitoring System
    
## 调用链监控
* [jaeger](https://github.com/jaegertracing/jaeger) CNCF Jaeger, a Distributed Tracing Platform
* 

## CI/CD工具
* [gocd/gocd](https://github.com/gocd/gocd) Continuous Delivery server https://www.gocd.org
* [jenkins](https://jenkins.io/zh/)
* 

## 画图工具

* 代码图片工具：[codeimg.io](https://codeimg.io/)
* [lucidchart](https://www.lucidchart.com/)
* [omnigraffle](https://www.omnigroup.com/omnigraffle/)
* [drawio](https://www.draw.io/)    
    > https://github.com/jgraph/drawio
* [cloudcraft](https://cloudcraft.co/)
* 

## 性能测试工具

* [wg/wrk](https://github.com/wg/wrk)
    > Modern HTTP benchmarking tool 

## git

* [tj/git-extras](https://github.com/tj/git-extras)
    > GIT utilities -- repo summary, repl, changelog population, author commit percentages and more 