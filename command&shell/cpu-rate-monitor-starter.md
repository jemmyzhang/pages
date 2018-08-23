# 定位CPU占用过高的原因
最近遇到一个网上问题，某局点的22台服务器集群，有俩台CPU占用率居高不下。因此在定位过程中，首先使用top指令查看各个进程的CPU占用率。
```shell
$ top
```

![top.png](cpu-rate-monitor-starter/top.png)

然而`top`指令只能细化到进程这个粒度，如果一个进程包含多个线程，那就需要借助到其他指令了。这里可以用下面的指令揪出CPU占用率比较高的线程。
```shell
$ ps H -eo user,pid,ppid,tid,time,%cpu,cmd --sort=%cpu
```

![ps.png](cpu-rate-monitor-starter/ps.png)

`H`是进程状态码参数，表示的是像展示进程一样展示线程，也就是每一条线程都是一条记录。`-e`表示选择了所有进程，`-o`表示以用户的格式化模板输出。后面跟的user，pid等就是输出的模板。`--sort`表示排序，可以是可以用+-代表是正序还是逆序。例如`--sort=%cpu`表示按照cpu占用率从小到大排列线程（+号可以省略），而`--sort=-%cpu`则代表CPU利用率从大到小排列。

