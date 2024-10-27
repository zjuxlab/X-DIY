# Python入门

[TOC]

> 本文适用于已经有过高级编程语言基础（如已经学过C/C++，Java等语言），需要快速上手Python以完成一些特定tasks的读者。

## 环境配置

### Python解释器安装

可以参考[这篇文章](https://blog.csdn.net/EnjoyEDU/article/details/133762548)，Linux和Mac OS用户可以参考[这篇文章](https://blog.csdn.net/qq_46094651/article/details/133796768)。

### VSCode环境配置

可以参考[这篇文章](https://zhuanlan.zhihu.com/p/639597093)，但个人觉得无需配置调试，过程繁琐且不太能用得上，对于EE Coder来说，采用输出调试才是更优的解决方案。

### PyCharm介绍

PyCharm是JetBrains旗下的Python IDE产品，可以在[官网](https://www.jetbrains.com/pycharm/)下载安装，也可以参考[这篇教程](https://blog.csdn.net/thefg/article/details/128881507)，但个人认为作为一名EE Coder，先接触VSCode总是有益的。

## 语言特性

不同于C/C++的编译执行特性（详见[C语言进阶](./C语言进阶.md)），Python是一门解释执行语言，在运行时没有编译这一环节，而是通过解释器对代码逐行运行，在运行速度上比编译型语言更慢，但是好处是无需编译环节，调试方便。

总的来说，Python是一门动态类型的脚本语言，相比于C，python在语法方面有更高级的抽象，使得语言更接近与自然语言，拥有更好的可阅读性。

在底层实现上，Python的实现基于其他的编译型语言，平时我们常见的python是使用C编写的解释器，称为Cpython。也有基于Java的解释器，如Jython。

Python还支持其他的一些编程特性，例如函数式编程；此外还有一些语法糖的设计，例如装饰器等。这些本文不做介绍，感兴趣的读者可在掌握本文后自行学习。

## 基本语法

### 变量

#### 基本类型

* 字符串｜str

  在我们学习Python时，字符串是最先接触的一个数据类型，Python的字符串比C要灵活得多，可以支持常规的`+`、`*`运算符进行字符串拼接和字符重复，也可以像`list`一样进行切片。

  并且不同于C语言的格式化只能作为格式化函数`printf()`和`scanf()`中实现，Python中的字符串就可以实现格式化，这里介绍三种常用的方法：

  1. 使用`%`操作符

  ```python
  name = "XLaber"
  age = 23
  height = 180.12345
  string = "I am a %s, I'm %d years old and I'm %.3fcm." % (name, age, height)
  print(string) # The result is: I am a XLaber, I'm 23 years old and I'm 180.123cm.
  ```

  2. 使用`str.format()`方法

  ```python
  name = "XLaber"
  age = 23
  height = 180.12345
  string = "I am a {}, I'm {} years old and I'm {:.3f}cm.".format(name, age, height)
  print(string) # The result is: I am a XLaber, I'm 23 years old and I'm 180.123cm.
  ```

  3. 使用f-string (Python 3.6+)

  ```python
  name = "XLaber"
  age = 23
  height = 180.12345
  string = f"I am a {name}, I'm {age} years old and I'm {height:.3f}cm."
  print(string) # The result is: I am a XLaber, I'm 23 years old and I'm 180.123cm.
  ```

* 整型｜int

  Python中的`int`和C中并无太大区别，均支持`+`、`-`、`*`、`/`、`%`等运算。

* 浮点型｜float

  Python中的`float`与C中也无太大区别，值得注意的是，由于Python的解释特性，我们只有赋值一个变量时才确定它的类型，这也就意味着Python中的`int`和`float`是解释器自行判断的。

* 布尔型｜bool

  `bool`类型的变量在C语言中是没有的，在Python中一个`bool`类型的变量只有两个取值`True`和`False`，分别对应`int`的`1`和`0`。

#### 数据容器

* 列表｜list

  