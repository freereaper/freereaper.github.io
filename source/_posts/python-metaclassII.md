---
title: python元类浅析(第二季)
date: 2016-11-20
layout: post
comments: true
tags: [python, metaclass]
---

<p id="div-border-top-blue">上一篇文章中已经讲述了什么是metaclass，metaclass类似创建类的模板，继承自type类，
通过重写<span id="inline-green">\_\_new\_\_</span>方法控制类的创建行为。</p>

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

在`MyClass`定义中通过指定`__metaclass__ = MetaClass`来告诉python解释器，在创建`MyClass`类时，
要通过`MetaClass`来创建，而不再使用内置的`type`。这样在`MetaClass.__new__()`方法中即可对类的定义进行修改，比如加上新的方法或者
属性。

## metaclass使用

在采用`__metaclass__`创建类的时候，默认的查找顺序如下（摘自官方文档）：
> If dict['__metaclass__'] exists, it is used.
> Otherwise, if there is at least one base class, its metaclass is used (this looks for a __class__ attribute first and if not found, uses its type).
> Otherwise, if a global variable named __metaclass__ exists, it is used.
> Otherwise, the old-style, classic metaclass (types.ClassType) is used.

<span id="inline-blue">委托(delegate)</span>是设计模式中常用的一种方法，通常利用委托类为需要委托的方法
去定义一个方法。
```python
class ImmutableList(object):  
    def __init__(self, delegate):  
        self.delegate = delegate  
  
    def __getitem__(self, i):  
        return self.delegate.__getitem__(i)  
  
    def __getslice__(self, i, j):  
        return self.delegate.__getslice__(i, j)  
  
    def __len__(self):  
        return self.delegate.__len(self)  
  
    def index(self, v):  
        return self.delegate.index(v)  
```

上面的实现显得非常的枯燥，使用元类的实现就显得格外简单和优雅，如下所示：
```python
class DelegateMetaClass(type):  
    def __new__(cls, name, bases, attrs):  
        methods = attrs.pop('delegated_methods', ())   
        for m in methods:  
            def make_func(m):  
                def func(self, *args, **kwargs):  
                    return getattr(self.delegate, m)(*args, **kwargs)  
                return func  
  
            attrs[m] = make_func(m)  
        return super(DelegateMetaClass, cls).__new__(cls, name, bases, attrs)  
  
class Delegate(object):  
    __metaclass__ = DelegateMetaClass  
  
    def __init__(self, delegate):  
        self.delegate = delegate  

class ImmutableList(Delegate):  
    delegated_methods = ( '__contains__', '__eq__', '__getitem__', '__getslice__',   
                         '__str__', '__len__', 'index', 'count')  

class ImmutableDict(Delegate):  
    delegated_methods = ('__contains__', '__getitem__', '__eq__', '__len__', '__str__',   
            'get', 'has_key', 'items', 'iteritems', 'iterkeys', 'itervalues', 'keys', 'values')  

```
