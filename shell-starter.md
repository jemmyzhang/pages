## bash和sh的区别
sh就是Bourne shell，这个是UNIX标准的默认shell，对它评价是concise简洁 compact紧凑  fast高效，由AT&T编写，属于系统管理shell。  
bash是 Bourne Again SHell 是linux标准的默认shell ，它基于Bourne shell，吸收了C shell和Korn shell的一些特性。bash是Bourne shell的超集，bash完全兼容Bourne shell,也就是说用Bourne shell的脚本不加修改可以在bash中执行，反过来却不行，bash的脚本在sh上运行容易报语法错误。 
## 变量
### 命名规范
命名只能使用英文字母，数字和下划线，首个字符不能以数字开头。  
中间不能有空格，可以使用下划线（_）。  
不能使用标点符号。  
不能使用bash里的关键字（可用help命令查看保留关键字）。  
### 使用变量
只需要在变量名前加美元符号即可。  
```shell
your_name="dongqiqi"
echo $your_name
echo ${your_name}
```

变量名外面的花括号是可选的，加不加都行。但加花括号可以帮助解释器识别变量的边界。例如下面例子，skill是变量：  
```shell
for skill in Ada Coffe Action Java; do
    echo "I am good at ${skill}Script"
done
```

### 只读变量
用`readonly`命令，例如：  
```shell
#!/bin/bash
myUrl="http://www.google.com"
readonly myUrl
myUrl="http://www.runoob.com"
```

### 删除变量
使用`unset`命令，语法：
```shell
unset variable_name
```

### 变量类型
shell包含三种类型的变量，分别是局部变量，环境变量和shell变量。  
1. 局部变量。局部变量在脚本或命令中定义，仅在当前shell实例中有效，其他shell启动的程序不能访问局部变量。
2. 环境变量。所有的程序，包括shell启动的程序，都能访问环境变量，有些程序需要环境变量来保证其正常运行。必要的时候shell脚本也可以定义环境变量。
3. shell变量。shell变量是由shell程序设置的特殊变量。shell变量中有一部分是环境变量，有一部分是局部变量，这些变量保证了shell的正常运行

## Shell中的数据类型
主要分为三种：1、字符串。2、数字。3、数组。

### 字符串
字符串的写法与PHP类似，既可以用单引号，又可以用双引号。只不过不同用法下会有不同的限制。例如两种下面写法均可：  
```shell
str='this is a string'
str="this is a string"
```

### 单引号
单引号字符串有如下限制：  
1. 单引号里的任何字符都会原样输出，单引号字符串中的变量是无效的。
2. 单引号字串中不能出现单引号（对单引号使用转义符后也不行）。


### 双引号
双引号字符串有如下特点：  
1. 双引号里可以有变量
2. 双引号里可以出现转义字符

需要使用变量应该使用双引号。

### 拼接字符串
方法一：  
```shell
your_name="qinjx"
greeting="hello, "$your_name" !"
```

方法二：  
```shell
your_name="qinjx"
greeting="hello, ${your_name} !"
```

### 获取字符串长度
```shell
string="abcd"
echo ${#string} #输出 4
```
也可以用length指令：  
```shell
string="hello,everyone my name is xiaoming"
expr length "$string"
```

注意：string字符串里边有空格,所以需要添加双引号。   

> 在使用expr指令时，表达式左右必须包含空格，如果不包含空格，将会输出表达式本身。  
> ```shell
> expr 5+6    // 直接输出 5+6
> expr 5 + 6       // 输出 11
> ```
> 
> 对于某些运算符，还需要我们使用符号"\"进行转义，否则就会提示语法错误。  
> ```shell
> expr 5 * 6       // 输出错误
> expr 5 \* 6      // 输出30
> ```

### 提取子字符串
```shell
string="runoob is a great site"
echo ${string:1:4} # 输出 unoo
```

### 查找子字符串
```shell
string="runoob is a great company"
echo `expr index "$string" is`  # 输出 8
```