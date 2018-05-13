# Ubuntu下没有apt包管理工具的解决办法

由于公司的开发环境虚拟机是基于精简版的Ubuntu制作，不附加任何软件，连apt（Advanced Packaging Tool）都没有，这给下载软件及工具包造成了很大的麻烦。

因此我们可以借助aptitude来安装apt工具，然后再基于apt下载安装其他软件。

首先更新源：

```shell
aptitude update //更新源
```

然后可以借助aptitude安装apt：

```shell
aptitude install apt //安装apt
```

安装完apt之后，我们就可以愉快地基于apt-get安装软件了。