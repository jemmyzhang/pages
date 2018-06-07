# Python进阶知识点总结

## 函数式编程
### 函数式编程的特点
1. 语言的高度抽象，更贴近于计算而非计算机。
2. 把计算视为函数而非指令。
3. 纯函数式编程不需要变量，没有副作用。多少次执行结果都是一样的，测试简单。
4. 支持高阶函数，代码比较简洁。

### Python支持的函数式编程
1. 允许变量，非纯函数式。
2. 支持高阶函数。
3. 支持闭包，有了闭包就能返回函数。
4. 有限度支持匿名函数。

### 高阶函数
能够接受函数做参数的函数，称为高阶函数。

1. 因为变量名可以指向函数。
2. 函数通过一个变量传给另一个函数。
3. 能够接受函数作为参数的函数就称为高阶函数。

### Python中返回函数

## 面向对象进阶

### \_\_new\_\_和\_\_init\_\_
`__new__`方法会在类初始化的时候被调用，只能用于从object继承的新式类。按照Python官方文档的说法，`__new__`方法主要是当你继承一些不可变的class时(比如int, str, tuple)， 提供给你一个自定义这些类的实例化过程的途径。还有就是实现自定义的metaclass。

#### 方法\_\_init\_\_和\_\_new\_\_的区别：

1. `__init__` 通常用于初始化一个新实例，控制这个初始化的过程，比如添加一些属性， 做一些额外的操作，发生在类实例被创建完以后。它是实例级别的方法。
2. `__new__`通常用于控制生成一个新实例的过程。它是类级别的方法。

从执行顺序上说，`__new__`会在`__init__`之前被调用。

#### Python包中\_\_init\_\_.py的作用
1. 作为package的标识。
2. 可以用来定义`__all__`来支持模糊倒入。  
Python的包和模块有两种导入方式，精确导入和模糊导入。  
精确导入的语法如下：
`from Root.Pack1 import Pack1Class`  
`import Root.Pack1.Pack1Class`  
模糊导入的语法，例如：  
`from package1 import *`  
里面的`*`就是由`__all__`来定义。  
例如：  
`__all__ = ["Pack1Class","Pack1Class1"]`代表导入语句能够导入`Pack1Class`和`Pack1Class1`这两个类。  
所以`__init__.py`的另一个作用就是定义package的`__all__`来限制模糊导入的范围。
3. 编写Python代码(不建议在__init__中写python模块，可以在包中在创建另外的模块来写，尽量保证__init__.py简单）


## 三方库

### Click

> [https://blog.csdn.net/lihua_tan/article/details/54869355?utm_source=itdadao&utm_medium=referral](https://blog.csdn.net/lihua_tan/article/details/54869355?utm_source=itdadao&utm_medium=referral "https://blog.csdn.net/lihua_tan/article/details/54869355?utm_source=itdadao&utm_medium=referral")