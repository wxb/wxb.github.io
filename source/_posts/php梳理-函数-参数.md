---
title: 'php梳理-函数[参数]'
categories:
  - php
tags:
  - php
  - 函数参数
  - 可变数量的参数列表
toc: true
author: 虢國技酱
comments: true
date: 2019-06-27 10:45:21
description:
original:
permalink:
---
函数作为程序语言中非常重要的组成部分，为我们编程提供了一种封装功能的手段，使我们可以编写出高内聚、可复用的程序功能。简单的说函数就是输入指定参数，经过内部处理后 返回计算得出的结果。 
本篇就主要梳理一下PHP函数的参数: 通过参数列表可以传递信息到函数，即以逗号作为分隔符的表达式列表。

<!-- more -->


## 值传参
默认情况下，函数参数通过值传递：即使在函数内部改变参数的值，它并不会改变函数外部的值;传递到函数的参数进行了值复制。

## 引用传参
引用传参会将调用函数时输入的参数的内存地址复制到函数的形参中，因此函数内部形参的修改会影响到外部入参的变量值,因为它们都指向相同的内存地址。      
如果想要函数的一个参数总是通过引用传递，可以在函数定义中该参数的前面加上符号 `&`

```php
function add_some_extra(&$string)
{
    $string .= 'and something extra.';
}

$str = 'This is a string, ';
add_some_extra($str);
echo $str;    // outputs 'This is a string, and something extra.'
```

## 默认参数
函数的默认参数是在调用函数时未传递入参，函数内部使用参数的默认值。
* 默认值必须是常量表达式，不能是诸如变量，类成员，或者函数调用等
  ```php
  function makecoffee($type = "cappuccino")
  {
      return "Making a cup of $type.\n";
  }
  echo makecoffee();
  echo makecoffee(null);
  echo makecoffee("espresso");

  /*
  以上例程会输出：
    Making a cup of cappuccino.
    Making a cup of .
    Making a cup of espresso.
  */
  ```
* 任何默认参数必须放在任何非默认参数的右侧，即有默认值的形参应在函数定义参数列表的靠后位置
  ```php
  // 不正确用法
  function makeyogurt($type = "acidophilus", $flavour)
  {
      return "Making a bowl of $type $flavour.\n";
  }

  echo makeyogurt("raspberry");   // won't work as expected
  ```
* 允许使用使用非标量类型作为默认参数,如数组 array 和特殊类型 NULL 作为默认参数:
  ```php
  function makecoffee($types = array("cappuccino"), $coffeeMaker = NULL)
  {
      $device = is_null($coffeeMaker) ? "hands" : $coffeeMaker;
      return "Making a cup of ".join(", ", $types)." with $device.\n";
  }
  echo makecoffee();
  echo makecoffee(array("cappuccino", "lavazza"), "teapot");
  ```
* PHP 5 起，传引用的参数也可以有默认值
  ```php

  function add_some_extra(&$var1=false, &$var2='')
  {
      echo gettype($var1, $var2);
  }

  add_some_extra()  // boolean string
  ```


## 可变数量的参数列表
对于自定义函数，PHP支持**可变数量**的参数列表, 即函数的参数数量不定。
* 在 PHP 5.5 及更早版本中，使用函数 func_num_args()，func_get_arg()，和 func_get_args() 。
```php
function sum() {
    $acc = 0;
    foreach (func_get_args() as $n) {
        $acc += $n;
    }
    return $acc;
}

echo sum(1, 2, 3, 4); // 10
```
* 在 PHP 5.6 及以上的版本中，由 `...` 语法实现
  * 使用`...`访问形参变量: 在PHP 5.6或更高版本中，参数列表可能包括`...`，表示函数接受可变数量的参数。参数将作为数组传递给给定的变量:
    ```php
    function sum(...$numbers) {
        $acc = 0;
        foreach ($numbers as $n) {
            $acc += $n;
        }
        return $acc;
    }

    echo sum(1, 2, 3, 4); // 10
    ```
  * 使用`...`提供入参: 可以用`...`调用函数将数组或可遍历变量或文字解压缩到参数列表:
    ```php
    function add($a, $b) {
        return $a + $b;
    }

    echo add(...[1, 2])."\n";

    $a = [1, 2];
    echo add(...$a);
    /*
    输出：
    3
    3
    */
    ```
  * `...`形参变量前，也可以指定**类型提示**，如果提供了类型，那么所有的`...`访问到的参数必须是该类型对象。
    ```php
    function total_intervals($unit, DateInterval ...$intervals) {
        $time = 0;
        foreach ($intervals as $interval) {
            $time += $interval->$unit;
        }
        return $time;
    }

    $a = new DateInterval('P1D');
    $b = new DateInterval('P2D');
    echo total_intervals('d', $a, $b).' days';

    // This will fail, since null isn't a DateInterval object.
    echo total_intervals('d', null);
    /*
    输出：
      3 days
      Catchable fatal error: Argument 2 passed to total_intervals() must be an instance of DateInterval, null given, called in - on line 14 and defined in - on line 2
    */
    ```

## 类型声明
类型声明(在PHP 5中，类型声明也被称为类型提示)允许函数在调用时要求参数为特定类型。 

* 为了指定一个类型声明，类型应该加到参数名前
* 标量类型声明有两种模式:**强制模式 (默认)**和**严格模式**;默认情况下，所有的PHP文件都处于弱类型校验模式(强制模式)。
  * 强制模式 (默认) 
    * PHP将会强迫错误类型的值转为函数期望的标量类型。 例如，一个函数的一个参数期望是string，但传入的是integer，最终函数得到的将会是一个string类型的值
    * 一个没有启用严格模式的文件内调用了一个在启用严格模式的文件中定义的函数，那么将会遵循调用者的偏好（弱类型），而这个值将会被转换。 
  * 严格模式
    * 使用 declare 语句和strict_types 声明来启用严格模式：
      ```php
      declare(strict_types=1);

      function sum(int $a, int $b) {
          return $a + $b;
      }

      var_dump(sum(1, 2));
      var_dump(sum(1.5, 2.5));
      ``` 
    * 基于每一个文件开启严格模式, `declare` 声明指令必须放在文件的顶部，甚至要写在`namespace`之前
    * 只有一个与类型声明完全相符的变量才会被接受，否则将会抛出一个TypeError
    * 唯一的一个例外是可以将integer传给一个期望float的函数。
    * 启用严格模式同时也会影响**返回值类型声明**.
    * 一个没有启用严格模式的文件内调用了一个在启用严格模式的文件中定义的函数，那么将会遵循调用者的偏好（弱类型），而这个值将会被转换。
* 可以通过将参数的默认值设为NULL来实现允许传递NULL
* 如果给出的值类型不对，那么将会产生一个错误:
  * 在PHP 5中，这将是一个**可恢复的致命错误**
  * 在PHP 7中将会抛出一个**TypeError**异常
* 有效的类型
  ![](/images/php/man/2.png)  
  **注意：**
  * **上面图中的标量类型不支持类型别名, 它们将被当做类名或接口名来处理。例如，使用`boolean`作为入参或者返回值类型声明时，就会要求入参或者返回值是`boolean`类或者接口的实例，而不是`bool`**
  * **`bool`、`float`、`int`和`string`这四种标量类型是 PHP7 新增，使用时应注意PHP版本**