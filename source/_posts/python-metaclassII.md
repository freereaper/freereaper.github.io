---
title: python元类浅析(第二季)
date: 2016-11-17
layout: post
comments: true
tags: [python, metaclass]
---

<p id="div-border-top-blue">上一篇文章中已经讲述了什么是metaclass，metaclass类似创建类的模板，继承自type类，通过重写<span id="inline-green">\_\_new\_\_</span>方法来定制类。</p>

## metacalss原理

```python
class MetaClass(type):
    def __new__(cls, name, bases, attrs):
        print "Allocating memory for class", name
        print cls
        print bases
        print attrs
        return super(MetaClass, cls).__new__(cls, name, bases, attrs)
		
    def __init__(cls, name, bases, attrs):
        print "initializing class", name
        print cls
        print bases
        print attrs
        return super(MetaClass, cls).__init__(name, bases, attrs)
		
calss MyClass(object):
    __metaclass__ = MetaClass
	
    def fun(self, param):
        print param

```

- cls: 将要创建的类。
- name: 类的名字，也就是通常使用__name__获取的属性。
- bases: 基类。
- attrs: 属性字典，可以是类变量，也可以是类函数。