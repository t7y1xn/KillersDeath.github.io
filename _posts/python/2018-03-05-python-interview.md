---
layout: post
title: python面试题总结
categories: [Python, 面试]
description: Python相关面试题总结
keywords: Python, 装饰器
---

### 语言特性
#### 函数参数传递
当一个引用传递给函数的时候，函数自动复制一份引用，这个函数的引用和外边的引用没有关系。
```python
a = 1
def fun(a):
    a = 2
fun(a)
print a  # 1
a = []
def fun(a):
    a.append(1)
fun(a)
print a  # [1]
```

> 第一个例子里的函数把引用指向了一个不可变的对象，函数返回时，外面的引用不会改变（二者没关联）。

> 第二个列子中函数的引用指向的是可变对象，对它的操作和定位了指针一样，在内存里进行修改。

### @staticmethod 和 classmethod
Python有3个方法：**静态方法、类方法、实例方法**
> 静态方法，其实和普通方法一样，不需要对谁进行绑定，唯一的区别是调用的时候需要使用`a.static_foo(x)`或者`A.static_foo(x)`

```python
def foo(x):
    print "executing foo(%s)"%(x)

class A(object):
    def foo(self,x):
        print "executing foo(%s,%s)"%(self,x)

    @classmethod
    def class_foo(cls,x):
        print "executing class_foo(%s,%s)"%(cls,x)

    @staticmethod
    def static_foo(x):
        print "executing static_foo(%s)"%x

a=A()
```

### 类变量和实例变量
+ **类变量**
> 可以在所有实例之间共享的值(不是单独分配给每个实例的)，如下例中的`num_of_instance`

+ **实例变量**
> 实例化后，每个实例单独拥有的变量

```python
class Test(object):  
    num_of_instance = 0  
    def __init__(self, name):  
        self.name = name  
        Test.num_of_instance += 1  // 也可以通过test1.num_of_instance来修改
if __name__ == '__main__':  
    print Test.num_of_instance   # 0
    t1 = Test('jack')  
    print Test.num_of_instance   # 1
    t2 = Test('lucy')  
    print t1.name , t1.num_of_instance  # jack 2
    print t2.name , t2.num_of_instance  # lucy 2
```

### Python 自省
自省，就是运行时能够获得对象的类型。比如`type()、dir()、hasattr()、isinstance()`

### 单下划线和双下划线
+ `__foo__`：python内部名字的**约定**，用来区别其他用户自定义的命名，以防止冲突。比如`__init__、__del__、__call__`
+ `_foo`：指变量私有的**约定**。不能用`from module import *`导入，其他和公有一样访问
+ `__foo`：解析器用`_classname__foo`来替代这个名字，无法直接像公有成员一样随便访问，需要通过**对象名.\_类名__xxx** 形式访问

```python
class MyClass():
     def __init__(self):
          self.__superprivate = "Hello"
          self._semiprivate = ", world!"
>>> mc = MyClass()
>>> print mc.__superprivate
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: myClass instance has no attribute '__superprivate'
>>> print mc._semiprivate
, world!
>>> print mc.__dict__
{'_MyClass__superprivate': 'Hello', '_semiprivate': ', world!'}
```

### 迭代器和生成器 [点击详情](https://stackoverflow.com/questions/231767/what-does-the-yield-keyword-do)
+ **生成器**：Everything can use `for ... in ...` on is an iterable; like `lists, strings`。所有数据都在内存中，当数据比较多时，不太适合。
+ **生成器**：只能迭代使用一次的迭代器。生成器并不会把所有的元素都放进内存。
+ **yeild**：很像`return`，返回的是一个生成器。

### \*args 和 \*\*kwargs
`*args`接收参数保存为列表，`**kwargs`接收参数保存为字典
> `*args` 和`**kwargs`可以同时在函数定义中，但是`**kwargs`必须放在`*args`后面

```python
>>> def print_three_things(a, b, c):
...     print 'a = {0}, b = {1}, c = {2}'.format(a,b,c)
...
>>> mylist = ['aardvark', 'baboon', 'cat']
>>> print_three_things(*mylist)

a = aardvark, b = baboon, c = cat
```

### 面向切面编程AOP和装饰器
装饰器是一个很著名的设计模式，经常被用于有切面需求的场景，较为经典的有`插入日志、性能测试、事务处理等`，装饰器是此类问题的绝佳设计——可以抽离出大量函数中与函数功能本身无关的雷同代码，并继续重用。
> 装饰器的作用，就是为已经存在的对象添加额外功能

> python提供了一些自带的装饰器，比如`property，staticmethod`

+ 详解见：https://stackoverflow.com/questions/739654/how-to-make-a-chain-of-function-decorators
+ 中文版：https://taizilongxu.gitbooks.io/stackoverflow-about-python/content/3/README.html

### 鸭子类型
在python中有许多`file-like`的东西，比如`StringIO、GizpFile、socket`。他们都许多相同的方法，我们都把他们当做文件使用
> list.extend()方法，我们并不关系它的参数是不是list，只要是可以迭代的，就可以extend

### python的重载
**python不需要重载**
1. 可变参数类型：python可以接受任何类型参数
2. 可变参数个数：python可以处理缺省参数

### 新式类和旧式类
> 旧式类和新式类，都采用了 `从左到右`的规则，但是旧式类是`深度优先`，新式类是`广度优先`

> 在多重继承方面，这种深度和广度的差别，会带来一些重写被绕过的问题

```python
class A(): // 旧式类的深度优先例子
    def foo1(self):
        print "A"
class B(A):
    def foo2(self):
        pass
class C(A):
    def foo1(self):
        print "C"
class D(B, C):
    pass

d = D()
d.foo1()
// A
```
详情参考：http://www.cnblogs.com/btchenguang/archive/2012/09/17/2689146.html

### \__new\__和\__init\__的区别
+ `__new__`是一个静态方法，`__init__`是一个实例方法
+ `__new__`方法返回一个创建的实例，而`__init__`什么都不返回
+ 只有在`__new__`返回一个cls的实例后面的`__init__`才会被调用
+ 当创建一个新的实例时调用`__new__`，初始化一个实例时用`__init__`

> 一般不需要重写`__new__`方法，除非你想继承一个不可变类型，如`str、int`等

详情参考：https://stackoverflow.com/questions/674304/why-is-init-always-called-after-new

### 单例模式
单例的核心结构只包含一个被称为**单例类**的特殊类。
> 通过单例模式可以保证系统中一个类只有一个实例而且该实例易于外界访问，从而方便对实例个数的控制并节约资源。

> 单例模式是指创建唯一对象，单例模式设计的类只能实例

+ Python的logger就是一个单例模式，用以日志记录
+ Windows的资源管理器是一个单例模式
+ 线程池，数据库连接池等资源池一般也用单例模式

> 总结一下什么情况下需要单例模式？

1. 当每个实例都会占用资源，而且实例初始化会影响性能，使用单例模式，则只有一个实例占用资源，并且只需初始化一次
2. 当有同步需要时，可以通过一个实例进行同步控制，比如对某个共享文件（如日志文件）的控制，对计数器的同步控制。

#### 使用`__new__`方法
> 不考虑多线程问题（当多个线程同时初始化对象时，就可能同时判断_instance is None）
```
class Singleton(object):
	def __new__(cls, *args, *kwargs):
		if not hasattr(cls, ‘_instance’):
			orig = super(Singleton, cls)
			cls._instance = orig.__new__(cls, *args, **kwargs)
		return cls._instance
class MyClass(Singleton):
	a = 1
```

#### 共享属性
创建实例时，把实例的`__dict__`指向同一字典，这样他们具有相同的属性和方法
```
class Borg(object):
    _state = {}
    def __new__(cls, *args, **kw):
        ob = super(Borg, cls).__new__(cls, *args, **kw)
        ob.__dict__ = cls._state
        return ob

class MyClass2(Borg):
    a = 1
```

#### 装饰器版本
```
def singleton(cls, *args, **kw):
    instances = {}
    def getinstance():
        if cls not in instances:
            instances[cls] = cls(*args, **kw)
        return instances[cls]
    return getinstance

@singleton
class MyClass:
  ...
```

#### import 方法
作为python的模块是天然的单例模式
```
// mysingleton.py
class My_Singleton(object):
    def foo(self):
        pass

my_singleton = My_Singleton()

// to use
from mysingleton import my_singleton

my_singleton.foo()
```

### python作用域
> 本地作用域（local）——> 当前作用域被嵌入的本地作用域（Enclosing loacl） ——> 全局/模块作用域（Global）——> 内置作用域（Build-in）

### GIL线程全局锁
即python为了保证线程安全而采取的独立线程运行的限制
> 实际就是一个核只能在同一时间运行一个线程。IO密集型任务，多线程起到作用。CPU密集型任务，多线程几乎没什么优势——可以借助多进程和下面的协程（协程也只是单CPU，但是能减少切换代价，提升性能）

### 协程（待深入理解）
协程是进程和线程的升级版，进程和线程都面临着内核态和用户态的切换问题，会耗费许多切换时间，而协程就是用户自己控制切换的时机，不需要陷入系统的内核态。
> yeild就是协程的思想

### 闭包
一个能记住嵌套作用域变量值的函数，尽管作用域已经不存在了。
```
def f1():
    x = 88
    def f2():
        print(x)
    return f2

action = f1()
action()

// output：88
```

详细见：①https://foofish.net/python-closure.html②https://www.zhihu.com/question/31792789
### 垃圾回收机制
Python GC主要使用**引用计数**跟踪回收垃圾，在此基础上，通过**标记-清除**解决容器对象可能产生循环引用的问题；通过**分代回收**以空间换时间的方法提升垃圾回收效率

#### 引用计数
> PyObject是每个对象必有的内容，其中`ob_refcnt`就是引用计数，当值为0时，该对象生命就结束了。

缺点：
+ 维护引用计数耗费资源
+ 循环引用

#### 标记清除
> 思想：按需分配。等到没有空闲内存的时候，从寄存器和程序栈上的引用出发，遍历以对象为节点，以引用为边构成的图，把所有可以访问的对象打上标记，然后清扫一遍内存空间，把所有没标记的对象释放掉。

#### 分代技术
> 思想：将系统中的所有内存块，根据存活时间划分为不同的集合，每个集合就成为一个“代”；垃圾收集频率随着“代”的存活时间的增大而减少，存活时间通常利用经过几次垃圾回收来度量。

> python 默认定义了三代对象集合，索引数越大，对象存活时间越长。

### urllib和urllib2的区别
1. urllib提供urlencode方法用来GET查询字符串的产生，而urllib2没有。这是为何urllib常和urllib2一起使用的原因。
2. urllib2可以接受一个Request类的实例来设置URL请求的headers，urllib仅可以接受URL。这意味着，你不可以伪装你的User Agent字符串等。
