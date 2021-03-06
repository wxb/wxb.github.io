title: PHP实现父类调用子类属性和方法：静态和后期静态绑定
categories:
  - PHP
tags:
  - static静态
  - thinkphp
toc: true
author: 虢國技酱
comments: true
date: 2017-03-17 16:22:55
description:
original:
permalink:
---
最近新项目决定使用thinkphp5.0框架开发，在开发中发现tp5.0是静态和实例对象混杂使用，感觉有点思维混乱，手册上对一个查询就有：实例对象、静态调用和快捷函数三种形式！你说方便吧，确实挺方便的！可是吧，多人协作开发，大家你一种风格我一种风格；看着这代码就觉得很没章法，也不知道tp5.0是怎么一个想法？然后在使用中因为某个问题查看源码时看到了：`static::` 这种静态调用，真是没这么用过，只用过`self::` `parent::`这两种， 所以赶紧学习。

<!-- more -->

# PHP静态
声明类属性或方法为静态，就可以不实例化类而直接访问。静态属性不能通过一个类已实例化的对象来访问（但静态方法可以）; 声明关键字：**static**
关于php静态总结几点:
* 静态属性和方法，可通过 `类名::`直接访问；
* 静态属性不能通过对象调用，但静态方法是可以的；可以通过对象调用静态方法，单不能通过静态方式调用非静态方法（其实也很好想通：动能调静，静不能调动；因为静是公共的是对所有都执行相同的行为动作，而动却是因具体示例对象不同而不同的）；
* 静态方法不需要通过对象即可调用，所以伪变量 $this 在静态方法中不可用
* 电影静态属性方法可使用关键字：`self`,`parent`,`static`;
* 静态属性只能被初始化为文字或常量，不能使用表达式；这一点发现给问题：
    ```php
    <?php

    class Person
    {
        public static $name = 'person A';
        public static $num;

        public function __construct($val, $number=0)
        {
            self::$name = $val;
            self::$num = $number+1;
        }

        public static function getName()
        {
            return self::$name;
        }

        public static function getNum()
        {
            return self::$num;
        }
    }


    echo Person::$name."\n";  // person A

    $b = new Person('person B', 1);
    echo Person::$name."\n";  // person B
    echo $b->getName()."\n";  // person B
    echo $b->getNum()."\n";   // 2

    $c = new Person('person C', 2);
    echo Person::$name."\n";  // person C
    echo $c->getName()."\n";  // person C
    echo $c->getNum()."\n";   // 3

    /**
     * 重点来了，怎么是 结果是：person C，我们上面输出的时候还是 person B 啊
     */
    echo $b->getName()."\n";  // person C
    echo $b->getNum()."\n";   // 3

    ```
有图有真相：
![](/images/php/2.png)
所以说：并不是 **静态属性只能被初始化为文字或常量，不能使用表达式**， 语法上是没有错误的，只是如果静态属性被动态生成或者表达式赋值，就会发生怪异行为或者说和你预期不一的行为，上面的示例中我已经指明：在经过new完`$c`对象后在输出`echo $b->getName()."\n";`，结果和第一次输出不一样了。所以现在应该清楚：静态是类公用的东西，当你在某个对象上做出改变时影响的将是所有这个类涉及和类对象涉及的代码行为；而这样的行为就很容易出现不可预期的事情发生，所以尽量避免这样做，当然如果你就是打算利用这一点完成你所期望的行为，那ok！没问题，去做吧，没有任何语法错误！

参考 [php手册-static](http://php.net/manual/zh/language.oop5.static.php)

# PHP特有的后期静态绑定
自 PHP 5.3.0 起，PHP 增加了一个叫做后期静态绑定的功能，用于在继承范围内引用静态调用的类。我们经常使用到的是：`self::`，`parent::`，没什么说的。关于后期静态绑定主要说`static:: `，“后期绑定”的意思是说，`static::` 不再被解析为定义当前方法所在的类，而是在实际运行时计算的。说的简单点，就是 `static::` 不会进行php代码检查，你可以调用一个类或者其父类中都没有的一个静态属性或方法，php不会报错；只要在你写的代码在运行到 `static::` 时这个类中有对应的属性或方法就行。
这里不再做示例了，php官方手册关于后期静态绑定有很清楚的示例：[php后期静态绑定](http://php.net/manual/zh/language.oop5.late-static-bindings.php)。

# 项目中用到后期静态绑定示例
在php中子类调用父类属性和方法很平常，那么父类如何调用一个子类的方法呢？
在项目中需要写一个基础方法，刚好php后期静态绑定很适合，这里做个示例。
需求是这样的：对于客户端提交过来的数据，为了安全，程序只接受那些我们知道的明确的信息，比如我们的原型中只有根据姓名查询，但如果有人多加了几个查询字段呢？有时候我们的代码在数据查询的时候处理不好就可能出现一些问题，所以需要一个基础方法，让我们指处理那些我们明确的、可预期的客户端提交数据。
**实现**
每个开发者，在Controller中定义对应客户端预期字段，调用我写的基础方法，只获取这些指定字段以内的信息！

```php
class Func
{
    /**
     * @Function    onlyParams
     * @Author      wangxb
     * @Description 只获取受控制请求参数
     * @return mixed
     */
    protected function onlyParams(){
        $curAction = $this->request->action();
        // Func中并没有定义$allowParams这个静态属性，当调用这个方法时，后期静态绑定就开始了
        // static:: 所调用的是代码当前运行类中的属性或者方法
        $params = static::$allowParams[$curAction];
        array_push($params, '__token__');
        return $this->request->only($params);
    }
}

```

```php
class Resource extends Func
{
    protected static $allowParams = [
        //key:操作方法名; value:可接受请求参数数组
        'index'  => ['fullRegionId','phoneFullRegionId','plan','unit','keyword','page'],
        'undist' => ['namePhone','fullRegionId','createTimeStart','createTimeEnd','pageRows'],
    ];

    public function index()
    {
        $params = $this->onlyParams(); // 代码运行到这里，后期静态绑定开始
        ...
    }

    public function undist()
    {
        $params = $this->onlyParams();
        ...
    }
}
```
是不是很神奇，宝宝都被震精到了：父类调用子类属性和方法！


# 我的日常Coding模式
好的方法总是会事半功倍，工作几年后得慢慢养成自己良好的Coding习惯和方法！先贴一张办公桌：

![](/images/php/1.png)

我一般使用笔记本做开发，在扩展至少一个显示器，慢慢的我发现这样的方式真是非常舒服！两个屏幕我是这样利用的：
* 笔记本主要是开一个编辑器和浏览器，进行主要的代码编写工作，`common+tab` 键在编辑代码和调试代码查看结果时非常方便
* 扩展屏主要放：产品原型、相关手册和一些随时会用到的源代码

比如工作中使用的是tp5.0框架，我会在扩展屏里面打开手册和tp源码！随时浏览手册，熟悉手册中相关的用法，尽可能保持自己所写的代码、所用的语法更加规范和标准；保持源码随时可浏览、可分析，毕竟手册所描述的不一定对或者不一定是你所能理解的！但忠实于源码总不会出错，如果错，那就可以开开心心去github提issue或者做一次pr提交了，能给做个开源贡献感觉也是棒棒哒！经常学习手册，你会发现很多新东西- **温故而知新** 就是这个feel！经常浏览学习源码，你不但能对框架更熟悉，而且能够看到牛逼人是怎么写代码的，毕竟你的身边不总是会有牛逼的人；经常学习源代码，你会发现很多语言上新的用法和代码的结构规划，这种潜移默化的影响真的是 **润物细无声** 啊！

所以很建议大家保持至少一个扩展屏，一屏保持专注工作Coding，一屏保证你抬头就能看到你需要的东西！
