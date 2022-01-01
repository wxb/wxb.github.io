---
title: Tornado踩坑--cookie
categories:
  - python
tags:
  - tornado
  - cookie
toc: true
author: 虢國技酱
comments: true
date: 2018-08-28 14:33:34
description:
original:
permalink:
---

去年底接手公司某个项目，python语言、tornado框架；修修补补了解业务和代码逻辑之后，发现代码组织有些混乱，扩展性较差！所以推倒重构，花费很大精力和心思想要做到极致，差不多四个月时间开发测试通过。

本以为这是自己的代表作，奈何被断断续续报告的 “**系统自动退出**” 所折磨！真是想说一声：阿西八！ 


<!-- more -->

## 问题场景
  * 首先认证登录系统，设置加密cookie，有效时长 90 天
  * 系统用一段时间(绝没有90天)后，会自动退出，重定向到 `/auth` 认证页面

## 系统逻辑
系统使用前需要按照配置的认证信息进行认证登录，如果认证登录成功，程序会在浏览器设置三个cookie信息：`park_code`、`device_id`和`device_sign`。在每次请求时检查这些信息是否在系统中存在，同时根据这些信息获取到对于账号的相关信息。如果没有获取到有效地这三个cookie信息，系统会认为没有认证过，自动跳转到认证页面，要求输入认证信息后继续操作。
  
## 问题追踪
  * 期初以为是设置的 90 天过期，时间太短，OK！弄长点：**365天**！ 发现依然问题存在
  * 我在想是不是Windows系统垃圾清理清理掉了浏览器的cookie，但是这不合理啊，Windows系统又不是没有存储了，磁盘还有很多空闲，这个可能性不大
  * 基于程序的逻辑，我去检查了浏览器上cookie信息，发现cookie信息存在！并且`Expires / Max-Age`过期时间一栏还早着呢
  * 服务器端打印出每次请求获取到的cookie信息，发现都是`None`，为什么浏览器上有cookie，但是服务器就是获取不到呢？

## 源码分析
Tornado做为python主流框架，按理来说应该有丰富完整的文档，但是对于国内用户（特别是我这种小学英语毕业）来说：可用的、优秀的中文文档太少太少！还是直接上源码看吧，简单直接！   
借助强大的IDE：vscode！追溯源码，快速跳转到定义简直了！

关于`cookie`的相关源码主要都在 `site-packages/tornado/web.py` 文件中，阔以说看起来相当方便和简单，只是这个变态的文件代码真是多：足足 **3318** 行代码！所以不要一行一行看，你会累死的，你也会放弃的🤣 , 还是有问题去追溯比较好！

我们的问题所用到的方法只有两个：`set_secure_cookie` 和 `get_secure_cookie`；设置cookie和获取cookie，看看tornado如何操作cookie的：


* web.py: 590行 
  `set_secure_cookie()` -调用最关键->  `create_signed_value()` 方法生成加密值，下面是我摘取源码的测试脚本，看注释啦

```python
    

import base64
import hashlib
import hmac
import time

DEFAULT_SIGNED_VALUE_VERSION = 2
_UTF8_TYPES = (bytes, type(None))


def utf8(value):
    # type: (typing.Union[bytes,unicode_type,None])->typing.Union[bytes,None]
    """Converts a string argument to a byte string.

    If the argument is already a byte string or None, it is returned unchanged.
    Otherwise it must be a unicode string and is encoded as utf8.
    """
    if isinstance(value, _UTF8_TYPES):
        return value
    if not isinstance(value, unicode_type):
        raise TypeError(
            "Expected bytes, unicode, or None; got %r" % type(value)
        )
    return value.encode("utf-8")

# 理解说明： 生成签名 版本1 
def _create_signature_v1(secret, *parts):
    hash = hmac.new(utf8(secret), digestmod=hashlib.sha1)
    for part in parts:
        hash.update(utf8(part))
    return utf8(hash.hexdigest())

# 理解说明：生成签名 版本2
def _create_signature_v2(secret, s):
    hash = hmac.new(utf8(secret), digestmod=hashlib.sha256)
    hash.update(utf8(s))
    return utf8(hash.hexdigest())

# 理解说明：最最最关键的核心方法：创建签名算法
def create_signed_value(secret, name, value, version=None, clock=None, key_version=None):
    if version is None:
        version = DEFAULT_SIGNED_VALUE_VERSION
    if clock is None:
        clock = time.time  # 理解说明：创建始终（默认当前时间时间戳）

    timestamp = utf8(str(int(clock())))
    value = base64.b64encode(utf8(value))
    if version == 1:
        signature = _create_signature_v1(secret, name, value, timestamp)
        value = b"|".join([value, timestamp, signature])
        return value
    elif version == 2:
        # The v2 format consists of a version number and a series of
        # length-prefixed fields "%d:%s", the last of which is a
        # signature, all separated by pipes.  All numbers are in
        # decimal format with no leading zeros.  The signature is an
        # HMAC-SHA256 of the whole string up to that point, including
        # the final pipe.
        #
        # The fields are:
        # - format version (i.e. 2; no length prefix)
        # - key version (integer, default is 0)
        # - timestamp (integer seconds since epoch)
        # - name (not encoded; assumed to be ~alphanumeric)
        # - value (base64-encoded)
        # - signature (hex-encoded; no length prefix)
        def format_field(s):
            return utf8("%d:" % len(s)) + utf8(s)
        to_sign = b"|".join([
            b"2",
            format_field(str(key_version or 0)),
            format_field(timestamp),
            format_field(name),
            format_field(value),
            b''])

        if isinstance(secret, dict):
            assert key_version is not None, 'Key version must be set when sign key dict is used'
            assert version >= 2, 'Version must be at least 2 for key version support'
            secret = secret[key_version]

        signature = _create_signature_v2(secret, to_sign)


# ------------------------ 测试验证 -------------------------------
secret = '****TzKXQAGaYdkL5gEmGeJJFuYh7EQnp2XdTP1o****'
print create_signed_value(secret, 'park_code', '9080700237')

# 输出
2|1:0|10:1535385317|9:park_code|16:OTA4MDcwMDIzNw==|0b3c88533f62dcf8cfd8547cc1632c8a13887a966546f5d2bdbbb85464ab6500
# 说明
version|length:unknown|length:created_timestamp|length:key|length:value|signature

```

* web.py 639行
  `get_secure_cookie()` -调用最关键解密签名的cookie-> `decode_signed_value()` -版本2签名解密-> `_decode_signed_value_v2()`, 调用顺序就是这样，看下面摘取源码验证的脚步吧！

```python

import base64
import hashlib
import hmac
import time

_UTF8_TYPES = (bytes, type(None))


def utf8(value):
    # type: (typing.Union[bytes,unicode_type,None])->typing.Union[bytes,None]
    """Converts a string argument to a byte string.

    If the argument is already a byte string or None, it is returned unchanged.
    Otherwise it must be a unicode string and is encoded as utf8.
    """
    if isinstance(value, _UTF8_TYPES):
        return value
    if not isinstance(value, unicode_type):
        raise TypeError(
            "Expected bytes, unicode, or None; got %r" % type(value)
        )
    return value.encode("utf-8")


def _create_signature_v2(secret, s):
    hash = hmac.new(utf8(secret), digestmod=hashlib.sha256)
    hash.update(utf8(s))
    return utf8(hash.hexdigest())

def _decode_fields_v2(value):
    def _consume_field(s):
        length, _, rest = s.partition(b':')
        n = int(length)
        field_value = rest[:n]
        # In python 3, indexing bytes returns small integers; we must
        # use a slice to get a byte string as in python 2.
        if rest[n:n + 1] != b'|':
            raise ValueError("malformed v2 signed value field")
        rest = rest[n + 1:]
        return field_value, rest

    rest = value[2:]  # remove version number
    key_version, rest = _consume_field(rest)
    timestamp, rest = _consume_field(rest)
    name_field, rest = _consume_field(rest)
    value_field, passed_sig = _consume_field(rest)
    return int(key_version), timestamp, name_field, value_field, passed_sig

def _time_independent_equals(a, b):
        if len(a) != len(b):
            return False
        result = 0
        if isinstance(a[0], int):  # python3 byte strings
            for x, y in zip(a, b):
                result |= x ^ y
        else:  # python2
            for x, y in zip(a, b):
                result |= ord(x) ^ ord(y)
        return result == 0

def _decode_signed_value_v2(secret, name, value, max_age_days, clock):
    try:
        # 理解注释 _decode_signed_value_v2() 方法中调用解码字段
        key_version, timestamp, name_field, value_field, passed_sig = _decode_fields_v2(value)
        # 输出
        # print key_version, timestamp, name_field, value_field, passed_sig
        # 0 1535385317 park_code OTA4MDcwMDIzNw== 0b3c88533f62dcf8cfd8547cc1632c8a13887a966546f5d2bdbbb85464ab6500
    except ValueError:
        return None
    # _decode_signed_value_v2() 被签名字符串
    signed_string = value[:-len(passed_sig)]

    if isinstance(secret, dict):
        try:
            secret = secret[key_version]
        except KeyError:
            return None
    # 根据被签名字符串和服务器上保存秘钥计算出签名串
    expected_sig = _create_signature_v2(secret, signed_string)
    # 比对cookie中的签名是否和以服务器秘钥计算出的一致，不一致返回None
    if not _time_independent_equals(passed_sig, expected_sig):
        return None
    # 字段名不一致，返回None
    if name_field != utf8(name):
        return None

    # 注意下面的判断，max_age_days参数的值，如果在调用get_secure_cookie没有明确指定，就用使用其默认值：31
    # def get_secure_cookie(self, name, value=None, max_age_days=31, min_version=None):
    timestamp = int(timestamp)
    # 这里就会判断创建时间戳 timestamp 是否小于 当前时钟 - 最大有效天数的秒数；如果cookie在通过set_secure_cookie设置了一个很长的expires_days，但是这里没有指定一样，也拿不到对应cookie的值
    if timestamp < clock() - max_age_days * 86400:
        # The signature has expired.
        return None
    try:
        return base64.b64decode(value_field)
    except Exception:
        return None

# --------------------------- 测试验证 ---------------------------------
secret = '****TzKXQAGaYdkL5gEmGeJJFuYh7EQnp2XdTP1o****'
value = "2|1:0|10:1535385317|9:park_code|16:OTA4MDcwMDIzNw==|0b3c88533f62dcf8cfd8547cc1632c8a13887a966546f5d2bdbbb85464ab6500"
name = 'park_code'

print _decode_signed_value_v2(secret, name, value)

```


## Tornado设置cookie使用说明(避坑指南)
* `set_secure_cookie`方法中的参数：`expires_days`和`expires` 是定义cookie在浏览器端的过期时间
* `get_secure_cookie`方法中的参数：`max_age_days` 是定义最大有效天数，tornado在设置cookie时，在cookie信息中保存了一下冗余信息，其中就有创建时间 `|10:1535385317|`，通过`get_secure_cookie`获取时会比对这个时间和当前时间的差值，而源码中`max_age_days`默认是31天
* cookie是否有效取决于这两个参数中较小的一个，由于`max_age_days`默认是31天，所以如果我们只是在`set_secure_cookie`中设置`expires_days`只能保证浏览器存储次cookie的时长，服务器获取到此cookie解密时会默认以31天比对，此时我们设置的`expires_days`就失去了应有的效果
* tornado在设置包含了一些冗余信息，其中就有创建时间，也就是说tornado设置cookie不只是依靠浏览器的过期时间，还在设置cookie的值时自己保存了一个时间戳，以避免一些应浏览器客户端和服务器时间不匹配导致的问题。所以我们在取一个cookie时记得注意`max_age_days`的指定，以防默认 31 天时长坑了自己


---

<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="//music.163.com/outchain/player?type=2&id=357300&auto=1&height=66"></iframe>
