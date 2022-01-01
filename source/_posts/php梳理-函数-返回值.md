---
title: 'php梳理-函数[返回值]'
categories:
  - php
tags:
  - php
  - 函数返回值
toc: true
author: 虢國技酱
comments: true
date: 2019-06-27 10:50:37
description:
original:
permalink:
---

函数的返回值通过使用可选的返回语句(`return`)返回。可以返回包括数组和对象的任意类型。返回语句会立即中止函数的运行，并且将控制权交回调用该函数的代码行

<!-- more -->

## return 返回语句的使用
  * 函数中 `return` 语句是可选的，如果省略了 return，则返回值为 NULL。
  * `return`可以返回包括数组和对象的任意类型
  * `return`会立即中止函数的运行，并且将控制权交回调用该函数的代码行

  ```php
  function square($num)
  {
      return $num * $num;
  }
  echo square(4);   // outputs '16'.
  ```

## 以数组形式得到多个返回值
  * 函数不能返回多个值，但可以通过返回一个数组来得到类似的效果。
  * 同时配合使用`list`使获取函数多个返回值的代码更加优雅：
  ```php
  function small_numbers()
  {
      return array (0, 1, 2);
  }
  list ($zero, $one, $two) = small_numbers();
  ```

## 函数返回一个引用
  * 函数可以返回一个引用
  * 从函数返回一个引用，必须在函数声明和指派返回值给一个变量时都使用引用运算符 `&`：
  ```php
  function &returns_reference()
  {
      return $someref;
  }

  $newref =& returns_reference();
  ```

## 返回值类型声明
  * PHP 7 增加了对返回值类型声明的支持
  ```php
  function sum($a, $b): float {
    return $a + $b;
  }

  // Note that a float will be returned.
  var_dump(sum(1, 2));
  ```
  * 返回值类型声明将指定该函数返回值的类型
    ```php
    class C {}
    function getC(): C {
        return new C;
    }

    var_dump(getC());
    /*
    输出：
      object(C)#1 (0) {
      }
    */
    ```
  * 严格类型 也会影响返回值类型声明
    * 在默认的弱模式中，如果返回值与返回值的类型不一致，则会被强制转换为返回值声明的类型
    * 在强模式中，返回值的类型必须正确，否则将会抛出一个TypeError异常.
      ```php
      declare(strict_types=1);

      function sum($a, $b): int {
          return $a + $b;
      }

      var_dump(sum(1, 2));
      var_dump(sum(1, 2.5));
      /*
      输出：
        int(3)
        Fatal error: Uncaught TypeError: Return value of sum() must be of the type integer, float returned in - on line 5 in -:5
      */
      ```
  * 子类方法覆盖父类方法时
    * 如果父类方法有定义返回类型，子类方法的返回值类型声明必须与父类一致
    * 如果父类方法没有定义返回类型，那么子类方法可以定义任意的返回值类型声明。
  * 当返回值类型声明之前有`?`时，表示允许函数返回**null**
    ```php
    function canReturnNullorString(): string
    {
        return null;
    }
    var_dump(canReturnNullorString()); // PHP Fatal error:  Uncaught TypeError: Return value of canReturnNullorString() must be of the type string, null returned in ....

    // ------------
    function canReturnNullorString(): ?string
    {
        return null;
    }
    var_dump(canReturnNullorString()); // NULL
    ```
  * 资源不允许作为返回类型
    ```php
    function fileOpen(string $fileName, string $mode): resource
    {
        $handle = fopen($fileName, $mode);
        if ($handle !== false)
        {
            return $handle;
        }
    }

    $resourceHandle = fileOpen("myfile.txt", "r");
    /*
      Errors with:
      Fatal error: Uncaught TypeError: Return value of fileOpen() must be an instance of resource, resource returned.
    */
    ```