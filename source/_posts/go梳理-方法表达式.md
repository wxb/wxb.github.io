---
title: go梳理-方法表达式
categories:
  - golang
tags:
  - 方法
  - 方法表达式
toc: true
author: 虢國技酱
comments: true
date: 2019-12-09 09:19:36
description:
original:
permalink:
---

在go语言中，我们可以简单的把没有接受者的函数称作函数，而把有接受者的函数称作方法。函数和方法 这种说话的区分主要是借鉴或者是受面向对象(OOP)编程影响，在面向对象编程中我们把定义在类中的函数称作方法。

go语言中的类型方法调用，一般是通过`类型变量.方法名(参数...)`的方式来调用; 当然我们也可以面向过程的方式来使用方法，此时方法将作为一个普通的函数，类型变量作为该函数的第一个参数（是不是感觉有点Python的味道）;

> 推荐 [《go语言高级编程-1.4.2 方法》](https://chai2010.gitbooks.io/advanced-go-programming-book/content/ch1-basic/ch1-04-func-method-interface.html)

<!-- more -->

## 函数

在编程时，我们将经常使用代码抽离出来进行简单的封装形成一个执行体或者执行单元，我们把这样的执行体就称作 **函数**

在go语言中，函数是一等公民（类型）。我们可以定义一种函数类型，然后初始化一个变量为这种函数类型的变量。 函数主要有具名和匿名之分，包级函数一般都是具名函数，具名函数是匿名函数的一种特例。

```go
// 具名函数
func Add(a, b int) int {
    return a+b
}

// 匿名函数
var Add = func(a, b int) int {
    return a+b
}
```

## 方法

方法一般是面向对象编程(OOP)的一个特性，是关联到具体对象上的。但是Go语言的方法却是关联到类型的，这样可以在编译阶段完成方法的静态绑定。

> 定义一个File文件类型和相关操作
```go

// File 文件对象
type File struct {
    path string
}

// Close 关闭文件
func (f *File) Close() error {
    defer fmt.Println("Close: ", f.path)

    return nil
}

// Read 读文件数据
func (f *File) Read(offset int64, data []byte) int {
    defer fmt.Println("Read: ", f.path)

    return len(data)
}

```

> 类型方法的使用
```go
func main() {
    f, _ := OpenFile("/path/prod.ini")

    f.Read(1, []byte("abc"))
    f.Close()
}
```

## 方法表达式

方法是由函数演变而来，只是将函数的第一个对象参数移动到了函数名前面了而已。因此我们依然可以按照原始的过程式思维来使用方法。通过 **方法表达式** 这个特性就可以将方法还原为普通类型的函数进行使用。

> 方法表达式的用法
```go
func main() {
    // 不依赖具体的文件对象
    // func CloseFile(f *File) error
    var CloseFile = (*File).Close

    // 不依赖具体的文件对象
    // func ReadFile(f *File, offset int64, data []byte) int
    var ReadFile = (*File).Read

    // 通过叫方法表达式的特性可以将方法还原为普通类型的函数：

    // 文件处理
    f, _ := OpenFile("/path/prod.ini")
    ReadFile(f, 0, []byte("abc"))
    CloseFile(f)

    // 文件处理
    ff := File{"/path/test.ini"}
    ReadFile(&ff, 0, []byte("aabbcc"))
    CloseFile(&ff)

}
```

可以看出，通过 **方法表达式** 这个特性；可是帮助我们以函数的使用方式来使用类型方法，在一些批量操作或者循环操作时帮助我们写出更加优雅的代码！
