---
title: python的类属性和实例属性
categories:
  - null
tags:
  - null
toc: true
author: 虢國技酱
comments: true
date: 2018-02-01 11:29:58
description:
original:
permalink:
---

# 问题概述

<!-- more -->


# 类属性和实例属性


# 验证两者关系和使用特性


> 以下验证环境是: `python 2.7.10`; 对于 `python 3.x.x` 是否一样，按照下面的方法，请大家自行验证，

```python
class Student(object):
    age = 18
    yy = []
    params = dict(where='xian')

    def __init__(self, name):
        self.name = name
        #self.params = dict(where='xian')
        print id(self.name)

    def preparams(self, params):
        #self.params = params
        print id(params), id(self.params)
    

stu_a = Student('wangxiaobo')
stu_b = Student('Michael')

stu_a.preparams({})
stu_b.preparams(dict())

```

# 用法注意


# 总结
