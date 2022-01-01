---
title: GuzzleHttp接口并发请求
categories:
  - php
tags:
  - Laravel
  - GuzzleHttp
toc: true
author: 虢國技酱
comments: true
date: 2021-02-05 10:24:32
description:
original:
permalink:
---

在目前的软件开发中，微服务化或者说服务网格化后，服务和服务之间、系统和系统之间、系统和服务之间经常存在大量的接口请求。接口请求受限于网络状态、服务性能和架构格局的影响，导致业务响应慢，后台任务执行超时。

那如果我们想要提高业务性能、缩短响应时间是不是就无能为力了！

我一直觉得：无论多么高大上的架构，都需要依赖每一个服务，每一个子系统的稳定与高效；所以，我们还是需要在我们能够控制的业务逻辑层面尽可能的优化我们代码执行效率。

<!-- more -->

## 问题综述

  影响我们业务执行效率的因素很多：语言层面、数据库层面、网络侧面、架构层面、代码层面等。我们最方便优化的就是代码层面，也是我们每个程序员一定会接触到的。

  在我们的服务网格中，我们的系统大量的依赖于各个服务和系统，接口的请求异常频繁，而接口的请求的时延和成功率相对数据库更加飘忽不定；

  大量的接口顺序请求，不光是影响我们的业务执行速度，导致我们的业务执行时间过长
  还会导致我们的业务一致性和数据一致性问题，A-B-C三个接口顺序请求，A成功我们进行了一下业务，B失败我们直接返回了，后续的业务没有执行，此时我们从业务和数据看这次流程，存在部分有效数据，也缺失了部分需要的逻辑结果数据

## 方案调研

  基于我们的问题：提升业务中接口的请求效率；我们找到Laravel内置的GuzzleHttp包，有两个特性：异步请求和并发请求。

## 测试验证

  基因Laravel提供的测试套件，对GuzzleHTTP的并发请求进行测试。我们假定目标接口单次请求耗时1秒，而我们的测试代码执行10次接口请求，以此对比GuzzleHTTP的**并发请求**的效果。

  ### 测试环境

  * 机器
    > MacBook Pro; 
    > 2.6 GHz 六核Intel Core i7;
    > 16 GB 2667 MHz DDR4

  * php 
    > PHP 7.3.25 (cli) (built: Dec 19 2020 11:21:26) ( NTS )

  * Laravel
    > "laravel/framework": "^8.12"

  * web服务器
    > 本机测试，使用Laravel提供的 `php artisan serve` 进行web服务

  * phpunit
    > "phpunit/phpunit": "^9.3.3"

  ### 目标接口
  > 这里假定我们的请求目标接口执行业务逻辑耗时及网络通信等耗时在 1秒；接口成响应时返回一个json，包含响应码`code`和响应消息`msg`

  ```php
    Route::get('/guzzle', function () {
        sleep(1);
        return ['code'=>0, 'msg'=>'succ'];
    });
  ```

  ### 接口顺序请求测试

  * 接口顺序请求测试代码
  ```php
    /**
    * 顺序请求
    *
    * @return void
    */
    public function testSequenceRequest()
    {
        $client = new Client(['base_uri' => 'http://127.0.0.1:8000']);

        foreach (range(1, 10) as $v) {
            $response = $client->request('GET', '/guzzle');

            $code = $response->getStatusCode();
            $body = $response->getBody();

            $this->assertEquals(200, $code);
            $this->assertJsonStringEqualsJsonString(json_encode(['code' => 0, 'msg' => 'succ']), $body);
        }
    }
  ```

  * 接口顺序请求测试结果
  ![](/images/php/guzzle/sequence.png)
  
  ### 接口并发请求测试

  * 接口并发请求测试代码
  ```php
    /**
    * 并发请求
    *
    * @return void
    */
    public function testConcurrentRequest()
    {
        $client = new Client(['base_uri' => 'http://127.0.0.1:8000']);

        $promises = array_fill(1, 10, $client->getAsync('/guzzle'));

        $responses = Promise\Utils::unwrap($promises);

        foreach (range(1, 10) as $v) {
            $code = $responses[$v]->getStatusCode();
            $body = $responses[$v]->getBody();

            $this->assertEquals(200, $code);
            $this->assertJsonStringEqualsJsonString(json_encode(['code' => 0, 'msg' => 'succ']), $body);
        }
    }
  ```

  * 接口并发请求测试结果
  ![](/images/php/guzzle/concurrent.png)

  ### 比对结果
    
    我在多次执行测试后，同一种测试结果基本只是在10+毫秒级别的差异；而且通过监控系统的资源使用情况，GuzzleHTTP的并发请求在资源使用并未大幅增加的情况下，大幅压缩了请求时间，性能提升明显。

## 适用场景
  > 前提是目标接口支持并发请求

  * 在我们的业务逻辑中有大量接口请求，并且这些接口请求不存在互相依赖或者顺序依赖其他条件的情况，我们可以调整这些请求并发获取结果后在进行业务处理，
  * 接口并发调整后，一是缩短了接口请求时间，还有一个好处就是我们的业务所需要的接口依赖数据都已获取到，假如某个接口的数据不符合业务逻辑，我们的业务可以提前失败返回，而不是部分逻辑成功部分逻辑失败的这种不一致的情况发生
  * 有些分页接口需要循环获取数据，在封装的SDK中，通常我们使用`yield`生成器来增加使用的便利性，现在我们可以利用 GuzzleHTTP 并发请求和 `yield`生成器，便利的同时性能更高
