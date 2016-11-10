---
title: python元类浅析(第一季)
date: 2016-11-11
layout: post
comments: true
tags: [python, metaclass]
---
 
## 类也是对象

在介绍元类之前，先来了解python中的类是什么。python中的类和大多数编程语言一样，
就是用来描述如何生成一个对象的代码片段。
```python
class ObjectCreator(object):
    pass

myObj = ObjectCreator()
print myObj

#-----------console output-------------
<__main__.ObjectCreator object at 0x02BE1810>
```
<!-- more -->

在python中，一切皆对象，类同样也是一种对象。`ObjectCreator就是内存中的一个对象，这个对象（类）自身
拥有创建对象（类实例）的能力，而这就是为什么它是一个类的原因。`但是，因为它本质依然是一个对象，因此也
就具有对象的行为特性：  
*  可以被变量引用
*  可以拷贝
*  可以修改属性
*  可以作为函数参数进行传递

e.g.:

```python
# you can print a class because it's an object
print(ObjectCreator) 
<class '__main__.ObjectCreator'>

 # you can pass a class as a parameter
def echo(o):
    print(o)

echo(ObjectCreator) 
<class '__main__.ObjectCreator'>

# you can add attributes to a class
print(hasattr(ObjectCreator, 'new_attribute'))
False

ObjectCreator.new_attribute = 'foo' 
print(hasattr(ObjectCreator, 'new_attribute'))
True
print(ObjectCreator.new_attribute)
foo

# you can assign a class to a variable
ObjectCreatorMirror = ObjectCreator 
print(ObjectCreatorMirror.new_attribute)
foo
print(ObjectCreatorMirror())
<__main__.ObjectCreator object at 0x8997b4c>
```
## 动态创建类

当使用`class`关键字时，python能够自动的创建类。但就像python中其它大部分设计一样，
它也提供了能够手动创建类的方法。

在python中`type` 除了能够返回对象的类型，它还具有动态创建类的能力。`type`可以接受一
个类的描述作为参数，然后返回一个类。

（我知道，根据传入参数的不同，同一个函数拥有两种完全不同的用法是一件很傻的事情，
但这在Python中是为了保持向后兼容性。）

`type`创建类的用法如下：
```python
type(类名， 父类元祖(可以为空)，属性字典)
```
以下两种方式都可以等价地创建出MyClass类：
* 使用class关键字
```python
class MyClass(object):
    author = "reaper"
    def say_author(self):
        print self.author
 
my_class = MyClass()
print my_class.author
my_class.say_author()
 
--------------------console output------------------
reaper
reaper
````

* 使用type
```python
def say_author(self):
    print self.author
MyClass = type("MyClass", (), {'author':  'reaper',  'say_author': say_author}
my_class = MyClass()
print my_class.author
my_class.say_author()
 
--------------------console output------------------
reaper
reaper
```

你可以看到，在Python中，类也是对象，你可以动态的创建类。这就是当你使用关键字`class`时Python在
幕后做的事情，而这就是通过元类来实现的。

## 什么是元类
 什么是元类？元类就是用来创建类的“东西“，元类就是类的类，直观来说就是，元类的实例就是类。
 ```python
#元类的instance就是类
MyClass = MetaClass()
 
#类的instance就是类实例，好吧，有点绕~~！
MyObject = MyClass()
 ```
 
在上一节中，已经讲述了`type`可以动态地创建类。然而，`type`其实就是一个元类。`type`元类是python
幕后用来创造所有类的元类。

在python中，`str`是用来创造字符串对象的类，`int`是用来创造整形对象的类，`type`就是用来创造类对象的
类。正如之前所说，python中的一切都是对象，包括整形，字符串，函数和类，它们最终都是通过元类创造出
来的。
```python
age = 32
age.__class__
age.__class__.__class__

name = "reaper"
name.__class__
name.__class__.__class__

class MyClass(object):
    pass
my_class = MyClass()
my_class.__class__
my_class.__class__.__class__
```
 
 可以看出，最后的`__class__`属性都是`<type 'type'>`，python中使用`type`作为内置的元类。下一季将讲述
如何定制元类，以及如何使用元类。

Refs: [stackoverflow](http://stackoverflow.com/questions/100003/what-is-a-metaclass-in-python)
