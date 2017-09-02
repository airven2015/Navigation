--- 
title: "python学习之aop装饰模式" 
layout: post 
date: 2017-09-02 13:22:00"
categories: python
---

实际开发过程当中可能要对某些方法或者流程做出改进，添加监控，添加日志记录等
所以我们要去改动已有的代码，自己的或者别人的，但改动后测试不周会引发不可控的异常，aop 模式解决了这类问题
引发重复代码大量积累，装饰器解决了些类问题

 1:基础篇

import functools

#采用functools.wraps的目的是为了使装饰器返回的类型始终是func的类型，否则将返回嵌套高阶函数的中的类型，例如返回的是wraper
#采用参数*args ,**kw是解决函数多参数的问题
```python
def log(func):
    @functools.wraps(func)
    def wrapper(*args, **kw):
        print 'call %s():' % func.__name__
        return func(*args, **kw)
    return wrapper

```
#修饰器参数的情况
```python
def logger(pram):
    def log(func):
        @functools.wraps(func)
        def wrapper(*args, **kw):
            print 'call %s(): and parsms is %s' %(func.__name__,pram);
            return func(*args, **kw);
        return wrapper;
    return log;


@log
def showMesage(name,age):
    print("my name is {0} and my age is {1}".format(name,age));


@logger("hello")
def getMessage(name,age):
    print("my name is {0} and my age is {1}".format(name,age));
```
#添加多个修饰器来拓展所需的功能
```python
@log
@logger("hello15466")
def recieveMessage(name,age):
    print("my name is {0} and my age is {1}".format(name,age));


if __name__=='__main__':
    showMesage("ddd",21);
    getMessage("aaa",43);
    recieveMessage("ccc",34);
```
 2:通过一个实用性的示例来继续描述下装饰器的使用场景,这次我将装饰器提取到类中,需要调用的地方通过类的引用来调取

下面这个示例演示的是一个监控程序运行时间的,如下:

一: 建立一个监控类[monitor.py]:
```python
import functools;
import time;

class monitor:
    @staticmethod
    def startmonitor():
        return time.clock();

    @staticmethod
    def endmonitor():
        return time.clock();



def listener(listen):
    def log(func):
        @functools.wraps(func)
        def wrapper(*args, **kw):
            startime=listen.startmonitor();
            f=func(*args, **kw);
            endtime=listen.endmonitor();
            print("total time is {0}".format(startime-endtime));
            return  f;
        return wrapper;
    return log;
```
二:在需要使用的地方来调用监控

```python
from  monitor import  *;

@listener(monitor)
def getinfo():
    print("hello ,welcome!");

if __name__=="__main__":
    getinfo();
```