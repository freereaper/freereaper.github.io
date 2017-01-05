---
title: python元类浅析(第二季)
date: 2016-11-20
layout: post
comments: true
tags: [python, metaclass]
---

<p id="div-border-top-blue">上一篇文章中已经讲述了什么是metaclass，metaclass类似创建类的模板，继承自type类，
通过重写<span id="inline-green">\_\_new\_\_</span>方法控制类的创建行为。</p>


### metacalss原理

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
		
class MyClass(object):
    __metaclass__ = MetaClass
	
    def fun(self, param):
        print param

'''
Allocating memory for class MyClass
<class '__main__.MetaClass'>
(<type 'object'>,)
{'fun': <function fun at 0x02E19BB0>, '__module__': '__main__', '__metaclass__': <class '__main__.MetaClass'>}
initializing class MyClass
<class '__main__.MyClass'>
(<type 'object'>,)
{'fun': <function fun at 0x02E19BB0>, '__module__': '__main__', '__metaclass__': <class '__main__.MetaClass'>}
'''
```

<!-- more -->

- cls: 将要创建的类。
- name: 类的名字，也就是通常使用__name__获取的属性。
- bases: 基类。
- attrs: 属性字典，可以是类变量，也可以是类函数。

在`MyClass`定义中通过指定`__metaclass__ = MetaClass`来告诉python解释器，在创建`MyClass`类时，
要通过`MetaClass`来创建，而不再使用内置的`type`。这样在`MetaClass.__new__()`方法中即可对类的定义进行修改，比如加上新的方法或者
属性。

### metaclass使用

在采用`__metaclass__`创建类的时候，默认的查找顺序如下（摘自官方文档）：
> If dict['__metaclass__'] exists, it is used.
> Otherwise, if there is at least one base class, its metaclass is used (this looks for a __class__ attribute first and if not found, uses its type).
> Otherwise, if a global variable named __metaclass__ exists, it is used.
> Otherwise, the old-style, classic metaclass (types.ClassType) is used.

#### 委托
<span id="inline-blue">委托(delegate)</span>是设计模式中常用的一种方法，通常利用委托类为需要委托的方法
去定义一个方法，以下是摘自网上的例子：
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

以上是比较常规的做法，而使用元类的实现就显得格外简单和优雅：
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

#### 接口

```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
class Field(object):
    def __init__(self, fname, ftype):
        self.fname = fname
        self.ftype = ftype
    def __str__(self):
        return '{%s: (%s, %s)}' % (self.__class__.__name__, self.fname, self.ftype)   
    
class StringField(Field):
    def __init__(self, fname):
        super(StringField, self).__init__(fname, 'varchar(100)')

class IntegerField(Field):
    def __init__(self, fname):
        super(IntegerField, self).__init__(fname, 'bigint')
    
class ModelMetaclass(type):
    def __new__(cls, name, bases, attrs):
        print cls
        print "creating for:", name
        if name == "Model":
            attrs['author'] = "reaper"
            return super(ModelMetaclass, cls).__new__(cls, name, bases, attrs)
        else:
            mapping = {}
            print "Create Model for:", name
            for k, v in attrs.items():
                if isinstance(v, Field):
                    print "mapping %s with %s" %(k, v)
                    mapping[k] = v
            attrs['_table'] = name 
            attrs['_mapping'] = mapping 
            return type.__new__(cls, name, bases, attrs)

    
class Model(dict):
    __metaclass__ = ModelMetaclass
    
    def __init__(self, **kwargs):
        for key in kwargs.keys():
            if key not in self.__class__._mapping.keys():
                print "Key '%s' is not defined for %s" %(key, self.__class__.__name__)
                return 

        super(Model, self).__init__(**kwargs)
        
    def save(self):
        fields = []
        params = []
        args = []
        for k, v in self.__class__._mapping.items():
            fields.append(k)
            params.append("'{0}'".format(self[k]))
        sql = 'insert into %s (%s) values (%s)' % (self.__class__._table, ','.join(fields), ','.join(params))
        print 'SQL: %s' %sql 

class Student(Model):
    id = IntegerField('id_c')
    name = StringField('username_c')
    email = StringField('email_c')

print Student.author

print "-------------------------------------------------"
print Student._table
print Student._mapping
print "-------------------------------------------------"
s1 = Student(id = 1, name = "Wilber", email = "wilber@sh.com")
print s1.id
s1.save()

```