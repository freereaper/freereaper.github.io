---
title: python元类浅析(第二季)
date: 2016-11-13
layout: post
comments: true
tags: [python, metaclass]
---

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