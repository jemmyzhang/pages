# Vim Tab设置
开发的时候，往往需要对Tab进行调整。这是由于Tab默认键入制表符，在一些编程语言中，尤其像Python这种缩进敏感的语言，直接使用Tab代替空格键就容易导致缩进不一致等问题。

## 调整属性的两种方式
如果您正在使用Vim编辑文件，不想中断退出，或是只想临时性临时性改变Tab的属性，那可以进入**命令行模式（Command line mode）**直接键入调整指令。进入命令行模式的方法是在**普通模式（Normal mode）**下键入`：`。  
如果您期望持久化您的设置，期待每次打开Vim都保留对Tab属性的设置，那可以您的在用户目录下（可以在shell命令行键入`cd ~`指令跳转到当前用户的目录），修改或者新增`.vimrc`文件，加入配置指令。

## 配置指令
### 设置Tab的跨度间距
```shell
set tabstop=4
```

或者
```shell
set ts=4
```

### 输入Tab时自动将其转化为空格
```shell
set expandtab
```

如果此时需要输入真正的制表符，则可以以此键入`Ctrl+V`，`Tab`，在 Windows下可以依次键入`Ctrl+Q`，`Tab`。

### 将文档中已存在的Tab进行转化
需要在Vim命令模式下执行，指令为：
```shell
:retab!
```

例如将已有的Tab替换为空格：
```shell
:set expandtab
:retab!
```

或是将已有的空格替换成Tab：
```shell
:set noexpandtab
:retab!
```

### 将 tab 显示为可见字符
```shell
set list listchars=tab:>-
```

### 在编辑不同类型的文件时采用不同的Tab属性
例如设置在编辑Python类型的文件，将Tab设置为空格，间距为4；而其他类型的文件，Tab为制表符，间距为2。可以将下面这段配置加入`.vimrc`文件：
```shell
autocmd FileType * set ts=2 | set noexpandtab
autocmd FileType python set ts=4 | set expandtab
```