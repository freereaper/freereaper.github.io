---
title: python元类浅析(第二季)
date: 2016-11-17
layout: post
comments: true
tags: [python, metaclass]
---

<p id="div-border-top-blue">嘿，我最近接到一个 Web 项目，不过老实说，我这两年没怎么接触 Web 
编程，听说 Web 技术已经发生了一些变化。听说你是这里对新技术最了解的 Web 开发工程师？</p>

<a id="newstyle">听说你是这里对新技术
最了解的 Web 开发工程师？近接到一个 Web 项目，不过老实说，我这两年没怎么接触</a>
<a id="newstyle">听说你是这里对新技术最了解的 Web 开发工程师？</a>

上一篇文章中已经讲述了什么是`metaclass`，那么<span id="inline-purple">metaclass</span>具体运用在哪些使用场景，通常有
- https://zhuanlan.zhihu.com/p/21379984
- http://www.tuicool.com/articles/N7fYNr

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