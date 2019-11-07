# 一次Druid连接池连接泄漏排查
## 定位原因
一个项目中使用了Druid连接池，最近测试突然发现某些接口响应很慢。通过jstack打印堆栈后发现如下情况：

1. 大量线程等待在DruidDataSource的getConnnetion上。
2. Druid的Create线程也处于等待状态。

于是我们在CreateConnectionThread中打了断点，看看究竟是为什么线程处于WAIT状态。发现到的现象是：

由于activeCount数量过大，已经要超过maxActive，导致线程处于WAIT状态，没有办法创建新的连接。

但是activeCount我们认为已经设置的足够大了，设置为100。我们认为活跃的连接数超过100，是不正常的，基本判断上是连接出现泄漏了。

为了印证这个猜想，我们查看了MySQL的物理连接数，使用命令：

```shell
	netstat -apn | grep 3306 | grep java-pid | wc -l
```

发现实际建立的连接数量远小于activeCount。也就意味着应用层确实存在连接没有释放的问题。
## 排查问题代码
为了找到具体没有释放的连接，我们在Druid的配置文件中加入了以下配置：

```xml
	<!-- 超过时间限制是否回收 -->  
	<property name="removeAbandoned" value="true" />  
	<!-- 超时时间；单位为秒。180秒=3分钟 -->  
	<property name="removeAbandonedTimeout" value="180" />  
	<!-- 关闭abanded连接时输出错误日志 -->  
	<property name="logAbandoned" value="true" />   
```

这个配置的作用是让Druid强制执行超时回收连接。即使数据库连接没有关闭，Druid也会强制中断并回收连接。配置完以后重启容器，可以看到应用日志中出现了连接中断的错误。查看中断部分到代码，找到了没有关闭，泄漏的连接。
