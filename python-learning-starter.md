# Python基础
## Python中的变量和数据类型
Python中的数据类型包括整数，浮点数，布尔值，字符串和空值五类。
### 整数
整数的长度范围：  
**2.7**  
32位：-2^31~2^31-1 64位：-2^63~2^63-1  
**3.5**  
int长度理论无限

### 浮点数
可以用科学计数法表示，例如`1.23e9`表示`1.23*10^9`

### 布尔值
#### Python中的布尔类型
布尔类型用`True`和`False`表示。注意首字母大写。  
Python把**0**，**空字符串''**和**None**看成是`False`，**其他数值**和**非空字符串**看成是`True`。  
与运算：`True and True`  
或运算：`True or False`  
非运算：`not True`

#### 布尔运算
布尔运算遵循短路原则，能够提前确定计算结果，将不会继续往后计算。例如：

```python
a = True
print a and 'a=123' or ''
```

首先是表达式`a and 'a=123'`，由于a为True，值取决于`'a=123'`，`'a=123'`在布尔运算中值被当做True，因此表达式的结果输出`a=123`。然后看`'a=123' or ''`，由于`'a=123'`为True，根据短路原则，表达式结果为`'a=123'`。因此整体表达式结果为`a=123`。

### 字符串
#### 字符串表示
字符串用`''`或者`""`括起来，类比JavaScript。如果字符串里包含引号，需要进行转义。`r'...'`语法进行集中转义，如果字符串**不**包含`''`或者`""`可以集中用。

多行表示用`'''...'''`表示：

```python
'''Line 1
Line 2
Line 3'''
```

用`r`可以把多行字符串变成一个**raw字符串**。//raw字符串是什么？

#### 字符串编码
ASCII码的大小是1个字节，1个字节内最大表示字符的数量为255，显然不足以包含所有字符。为了解决中文，日文，韩文等出现的问题，出现了Unicode，把所有语言统一编到一套编码里，就不会有编码问题了。

但是Python诞生比Unicode还要早，因此最早的Python只能支持ASCII码，例如`'ABC'`就是以ASCII码编码的。

后来添加了对Unicode的支持，表示形式`u'...'`；多行显示`u'''...'''`；raw+多行显示`ur'''...'''`

如果中文字符串在Python环境下遇到 UnicodeDecodeError，这是因为.py文件保存的格式有问题。可以在第一行添加注释

```python
# -*- coding: utf-8 -*-
```

目的是告诉Python解释器，用UTF-8编码读取源代码。然后用Notepad++ 另存为... 并选择UTF-8格式保存。

### 空值
空值是Python里面特殊的值，用`None`表示。

## 变量
变量的命名规则：变量名为大小写英文，数字和下划线，且不能用数字开头。

### 列表
Python内置数据类型`list`，语法上用`[...]`来囊括元素。由于Python是动态语言，所以列表里面可以（混合）容纳各种类型的数据。

列表除了常规的下标索引以外，还可以使用倒序或者切片等手段进行访问。

#### 倒序访问
例如访问倒数第N个元素(注意数组不要越界)：

```python
print L[-N]
```

#### 添加新元素
方法一：使用list的`append()`方法，例如：

```python
list = ['Adam', 'Lisa', 'Bart']
list.append（'Tony'）
print list
# output:['Adam', 'Lisa', 'Bart','Tony']
```

方法二：使用list的`insert()`方法。list接收两个参数，第一个参数是索引，第二个参数是元素。  
例如在索引位置0加入一个元素：  

```python
>>> list=['Adam','Lisa']
>>> list.insert(0,'Tony')
>>> list
['Tony', 'Adam', 'Lisa']
```

Q：如果insert的索引位超过列表的最大索引？例如`list.insert(10,'Tony')`。  
A：此时元素将默认添加到列表末尾。  

### 删除元素
使用`pop()`方法删除列表尾部元素，利用`pop(N)`删除索引为N的元素。

### 替换元素
基于下标索引直接替换，例如`list[2]='Peter'`

## 有序列表Tuple
tuple是一种有序列表，一旦创建完成就无法修改。

```python
>>> t = ('Adam', 'Lisa', 'Bart')
```

tuple可以使用索引或者倒序索引访问，但是无法修改或者删除元素了。

### 单元素Tuple
如果没有元素，可以使用`t=()`语法。  
如果只有一个元素，需要使用`t=(1,)`语法。这是由于如果只是使用`t=(1)`这种语法，解释器会认为这是一个括号运算符。

### “可变”Tuple
Tuple中的元素还是区分值类型和引用类型的，例如一个Tuple中包含一个list数组，Tuple的元素位就会指向list的引用。list的内容是可变的。例如：

```python
>>> t = ('a', 'b', ['A', 'B'])
>>> L = t[2]
>>> L.append('C')
>>> print t
('a', 'b', ['A', 'B', 'C'])
```

如果要想Tuple不可变，那么应该保证Tuple中的所有元素不变。例如上例中的list应该替换成Tuple类型。

```python
>>> t = ('a', 'b', ('A', 'B'))
```

## 判断
和其他语言不通，关键字`if`之后不加括号，并且判断条件后跟冒号`：`后换行。函数范围依靠缩进来保证，不需要括号。  
例如：

```python
age = 20
if age >= 18:
    print 'your age is', age
    print 'adult'
print 'END'
```

Python是严格缩进的语言，请使用4个空格的缩进，而不是Tab。

if..else示例：

```python
if age >= 18:
    print 'adult'
else:
    print 'teenager'
```

if..elif..else对应if..else if..else的逻辑，下面示例：

```python
age = 8
if age >= 6 and age <18:
    print 'teenager'
elif age >= 18:
    print 'adult'
else:
    print 'kid'
```

## 循环

### for循环
for循环采用`for .. in ...`这种语法，这里的`..`代表迭代的元素，`...`代表集合。  

```python
for value in [1,2,3,4]:
    print value
```

Q：如何一边迭代一边拿到迭代元素下标？  
A：可以采用python标准函数库内建的enumerate函数。enumerate的语法是enumerate(sequence,start=0)，其中sequence是可迭代对象，可以使列表，字典，文件对象等。start是计数枚举的起始值。例如：

```python
list=["Ted","Infi","Sky","Fly","Moon"]
enum_obj=enumerate(list,0)
for key,value in enum_obj:
    print key,value

# output below
0 Ted
1 Infi
2 Sky
3 Fly
4 Moon
```

### while循环
语法 `while ...`,`...`是判断条件，例如：

```python
N = 10
x = 0
while x < N:
    print x
    x = x + 1
```

### break和continue
使用`break`可以中断循环，利用`continue`可以跳过此轮进入后续循环，这个和其他语言大同小异。

## 字典Dict
字典表现形式就是键值对集合。类似于Java中的Map，语法格式：`d={'Adam':98,'Tony':100,'Bart':95}`

可以用`len`函数计算dict的长度。例如`len(d)`。

> 所有的集合都可以用len来计算长度，包括list，tuple，dict。


### Dict的访问
可以用`key`查找对应的value，与list很像。不同的是，list用的是下标索引，而dict用的是key。例如：

```python
>>> print d['Adam']
95
```

如果key存在，dict会返回具体的value，如果key不存在，会报KeyError。

dict查找速度很快（底层应该是Hash结构，有待了解）。

dict存储没有顺序，存储过程中key不可变，不能重复。例如list是可变的，因此不能作为key。

### Dict更新
更新可以基于key-value直接赋值。如果key已经存在，新的value会替换原有的value。

```python
d['Paul'] = 72
```

### Dict迭代
Dict可以用`for`循环对key进行迭代，例如：

```python
>>> dict = { 'Tony': 100, 'Steve': 85, 'Bill': 59 }
>>> for key in dict:
...     print key
... 
Lisa
Adam
Bart
```

### 迭代Dict的value
可以将dict转化成一个包含所有的list，这样就可以对dict的每一个value进行迭代：

```python
dict = { 'Tony': 100, 'Steve': 85, 'Bill': 59 }
for value in dict.values():
    print value
```

## Set
dict是建立一组key和value的映射关系，dict的key不能重复。  
而set持有一系列元素，但是set里面元素没有重复，而且是无序的。set和dict的key很像。 

### set添加
语法采用`set()`函数，例如`s = set(['A', 'B', 'C'])`。如果传入的元素中包含重复的元素，set会自动去重。  
set包含`add`方法，可以通过`s.add(1)`语法来添加元素。如果是重复的元素就会自动去重。

### set删除
可以通过`s.remove()`函数删除元素。删除前需要判断元素是否存在，如果元素不存在还进行喊出，remove方法会报错。

### Set元素访问
由于Set是无序集合，所以不能通过索引来访问。我们可以用in来判断元素是否存在：

```python
s = set(['a', 'b', 'c', 'd'])

>>> 'a' in s
True
```

### Set遍历
语法参考list等集合

### Set特点总结
Set内部结构和dict很像，区别是不存储value。  
Set存储元素和dict的key，类似，必须是不变对象。  
set元素也是没有顺序的。

## 函数
### 定义函数
定义函数使用`def`关键字，然后跟`：`，在缩进块中编写函数体。遇到return语句返回。

```python
def my_abs(x):
    if x >= 0:
        return x
    else:
        return -x
```

### 返回值
Python一个特点是**语法上**支持多返回值。在写法上支持多返回值，其实本质上返回值却是返回一个tuple，例如：  

```python
def move(x,y):
    return x+100,y+200

x,y=move(100,200)
print x,y
# ouput 200,400
r=move(100,200)
print r
# ouput (200,400)
```

### 递归函数
递归就是自己调自己，和其他语言一样，这里不再赘述，注意递归函数要添加退出条件。

### 默认参数
定义函数的时候，可以包含默认参数。语法例子如下：

```python
def power(x, n=1):
    return x+n
```

这样在传值的时候，可以只传一个参数：

```python
power(2):
# output: 3
```

**默认参数只能定义在必须参数后面**

### Python可变参数
如果想让函数能够接受任意参数，就可以定义可变参数。语法如下：

```python
def function(*args):
    ...
```

可变参数的本质还是一个tuple，args可以直接当做一个tuple来用。


## 切片
Python提供了切片操作大大简化了集合和字符串等的操作。切片的语法是`list[x:y:z]`函数。x，y，z指的是‘开始元素’：‘最后元素’：‘取元素间隔.  
### 集合切片操作
例如对list进行截取，可以采用`list[0:3]`这种语法。这表示从索引0开始取，直到索引3为止，但是不包括索引3。  
如果开始的索引是0，则0可以省略,语法为`list[:3]`。  
如果只用一个`：`，表示切片的范围是从头到尾。`list[:]`的含义就是完整复制出一个新的list。  
切片还能够指定第三个操作，第三个元素表示了取元素的间隔。例如`list[1:10:2]`这个代表从索引1开始取，每2个元素取1个，到索引10为止。

### 倒序切片
倒序切片和正序切片类似，只要按照切片语法的去理解，就很容易。例如：`L[-4:-1:2]`代表起始元素是索引倒数第四个元素，最后元素索引倒数第一个元素，取元素间隔是2。

### 字符串切片
字符串也是可以进行切片操作的，结果仍然是字符串。这种截取字符串的操作很简单。

```python
>>> 'ABCDEFG'[::2]
'ACEG'
```

## 迭代
在Python中，通过for循环来遍历这个list或tuple，这种遍历我们成为迭代（Iteration）。有序集合，无需集合，键值对dict俊可以进行迭代。  
迭代与按下标访问数组最大的不同是，后者是一种具体的迭代实现方式，而前者只关心迭代结果，根本不关心迭代内部是如何实现的。  
### 索引迭代
对于有序集合，元素是有索引的，如果想到到索引，就需要使用` enumerate() `函数。  
enumerate函数会将集合包装成一个tuple的集合。例如：

```python
['Adam', 'Lisa', 'Bart', 'Paul']
```

变成了类似：

```python
[(0, 'Adam'), (1, 'Lisa'), (2, 'Bart'), (3, 'Paul')]
```

### 迭代dict的value（两种方式对比）
前文已经提到了一种方式，即我们可以将dict转化成一个包含所有的list，这样我们就可以对dict的每一个value进行迭代：

```python
dict = { 'Tony': 100, 'Steve': 85, 'Bill': 59 }
for value in dict.values():
    print value
```

第二种方式是使用dict的itervalues方法。

```python
dict = { 'Tony': 100, 'Steve': 85, 'Bill': 59 }
for value in dict.itervalues():
    print value
```

两个方法的区别：

1. `values()`方法实际上把dict转化成了包含value的list。
2. `itervalues()`方法不会转换，一次从dict去除了value，因此比`values()`方法更节省内存。

### 迭代dict的key和value
可以对dict`items()`的返回值进行迭代，从而同时得到dict的key和value。dict的items方法返回了包含key和value的tuple组合的列表。例如：

```python
dict={'Tom':95,'Jim':90,'Bill':85}
print dict.items()
# output: [('Jim', 90), ('Bill', 85), ('Tom', 95)]
```

因此我们可以对items()的返回值进行迭代，同时获得key和value：

```python
dict={'Tom':95,'Jim':90,'Bill':85}
print dict.items()
for key,value in dict.items():
    print key,"-",value
```

和`itervalues()`类似，`items()`也有一个对应的`iteritems()`，也是不需要转化成list，所以`iteritems()`不需要占用额外内存。

## 列表生成式

### 生成列表
我们可以借助range函数。例如：

```python
range(1, 10)
[1, 2, 3, 4, 5, 6, 7, 8, 9]
```

如果要生成比较复杂的类型，使用range生成的语法就比较冗长，例如生成10以内平方数的列表集合：


```python
l=[]
for x in range(1, 10):
    l.append(x * x)
```

这时候我们可以借助**列表生成式**来实现上面的list：

```python
[x * x for x in range(1, 10)]
```

在这个表达式中，要生成的元素`x * x`放在表达式前面，后面跟for语句，我们就可以把list生成出来。

### 条件过滤
如果我们在列表生成式中想加入条件过滤，可以在for循环后面加入if判断，例如：

```python
[x * x for x in range(1, 10) if x % 2 ==0]
# output: [4, 16, 36, 64]
```
意为筛选出1到9区间内所有偶数的平方数	列表。

### 多层表达式
for循环可以嵌套，所以在列标生成式中也可以多层for循环来生成列表。

```python
[x + y for x in 'hello' for y in '123']
output: ['h1', 'h2', 'h3', 'e1', 'e2', 'e3', 'l1', 'l2', 'l3', 'l1', 'l2', 'l3', 'o1', 'o2', 'o3']
```