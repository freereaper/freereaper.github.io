---
layout: post
title: C++ Singleton Design Pattern
date: 2015-08-02
comments: true
tags: [c++, design pattern]
---

### 单例模式

一个类仅允许创建一个实例，这通常使用在类似于日志或者其他只有单一资源可供使用的情况。单例设计模式保证类本身提供单一实例创建的方法。

使用c++实现单例模式的方法如下:  

1. 构造函数声明为私有;
2. 将拷贝构造函数与等号运算符声明为私有，并不提供他们的实现;
3. 在类中声明一个静态的全局访问接口;

<!-- more -->

### 通过懒汉模式实现

```c++
class Singleton {
public:
    static Singleton& Instance() {
        static Singleton theSingleton;
        return theSingleton;
    }

    /* more (non-static) functions here */

private:
    Singleton();                            // ctor hidden
    Singleton(Singleton const&);            // copy ctor hidden
    Singleton& operator=(Singleton const&); // assign op. hidden
    ~Singleton();                           // dtor hidden
};

```

懒汉模式（第一次调用创建该类实例的时候才产生一个该类的实例）通过静态局部变量创建实例，并在之后都只返回此实例。这样做的好处是，程序会在退出时自动析构该实例。


### 通过饿汉模式实现

```c++
class SingletonStatic  
 {  
 private:  
     static const SingletonStatic* m_instance;  
     SingletonStatic(){}  
 public:  
     static SingletonStatic* getInstance()  
     {  
         return m_instance;  
    }  
};  
  
//外部初始化 before invoke main  
const SingletonStatic* SingletonStatic::m_instance = new SingletonStatic;  

```

饿汉模式（程序一开始就产生一个该类的实例），通过这种方式实现的单例模式保证了线程安全性，但是无法自动执行析构函数，比如有关闭文件，释放外部资源等。这可以通过在程序结束时通过delete getInstance()来实现，这种方式显得过于笨重，并且容易遗忘。利用系统在程序结束后会自动析构所有的全局变量和类的静态成员变量，我们可以在单例类中定义一个这样的静态成员变量，而它的唯一目的就是在程序结束后能够析构单例类中的实例。代码如下：

```c++
#include <iostream>>  
  
using namespace std;  
  
class Singleton  
{  
  
public:    
    static Singleton *GetInstance();  
  
private:  
    Singleton()  
    {   
        cout << "Singleton ctor" << endl;   
    }  
  
    ~Singleton()   
    {  
        cout << "Singleton dtor" << endl;  
    }  
  
    static Singleton *m_pInstance;  
  
    class Garbo  
    {  
  
    public:  
        ~Garbo()   
        {  
            if (Singleton::m_pInstance)  
            {  
                cout << "Garbo dtor" << endl;  
                delete Singleton::m_pInstance;    
            }  
        }
    };  
    static Garbo garbo;  
};  
  
Singleton::Garbo Singleton::garbo;  // 一定要初始化，不然程序结束时不会析构garbo  
Singleton *Singleton::m_pInstance = NULL;  
  
Singleton *Singleton::GetInstance()  
{  
  
    if (m_pInstance == NULL)  
        m_pInstance = new Singleton;  
  
    return m_pInstance;  
  
}  

```

程序运行结束后会调用CSingleton的静态成员Garbo的析构函数，该析构函数会删除单例的唯一实例。这种方式的特点如下：  

* 在单例类内部定义专有的嵌套类；
* 在单例类内定义私有的专门用于释放的静态成员；
* 利用程序在结束时析构全局变量的特性，选择最终的释放时机;
* 使用单例的代码不需要任何操作，不必关心对象的释放

___Ref___:  [C++ Singleton design pattern](http://www.yolinux.com/TUTORIALS/C++Singleton.html)
