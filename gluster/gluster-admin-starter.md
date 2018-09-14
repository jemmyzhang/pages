# GlusterFS 管理员手册
这篇管理员手册记录了GFS的日常操作和高级配置。主要基于Gluster CLI，本文总结自Gluster官方文档。

## 集群管理
### 管理glusterd服务
#### 手动启停glusterd服务
启动指令如下：
```shell
# /etc/init.d/glusterd start
```

停止指令如下：
```shell
# /etc/init.d/glusterd stop
```


## 存储设置

## 客户端设置

## 卷设置

## 

## CLI 参考
您可以使用Gluster CLI来安装和管理您的Gluster集群。您可以使用命令行，或者在CLI交互指令界面使用到gluster的命令。例如，直接使用命令：
```shell
#　gluster <command>
```

比如查看各个结点的状态可以用如下指令：
```shell
# gluster peer status
```

### 结点命令（Peer Commands）
结点命令可以管理Gluster的信任主机池（Trusted Server Pool，TSP）。

|命令|语法|描述|
|--|--|--|
|peer probe|peer probe *server-ip*|添加一个主机到TSP|
|peer detach|peer detach *server-ip*|从TSP池里删除一个主机|
|peer status|peer status|从TSP里查看所有主机的状态|
|pool list|pool list|列出所有TSP中所有的主机结点|

### 卷命令（Volume Commands）
卷命令可以用来安装和管理Gluster卷存储。

|命令|语法|描述|
|--|--|--|
|volume create|volume create *volname* [options] *bricks*|用指定的bricks创建一个名为volname的Gluster存储卷，并可以options对参数进行配置。|
|volume start|volume start *volname* [force]|启动一个存储卷|
|volume stop|volume stop *volname* [force]|停止一个存储卷|
|volume info|volume info [volname]|显示存储卷的信息|
|volume status|volume status [volname]|显示存储卷的状态|
|volume list|volume list|显示存储卷的列表|
|volume set|volume set *volname option value*|对名称为volname的存储卷设置参数|
|volume get|volume get *volname* &#60;option&#124;all&#62; |显示volname卷中某个option的值，或者所有option的值|
|volume add-brick|volume add-brick *brick-1...brick-n* |添加brick-1到brick-n到volume中以拓展volume|
|volume remove-brick|volume remove-brick *brick-1...brick-n* &#60;start&#124;stop&#124;status&#124;commit&#124;force&#62;|添加brick-1到brick-n到volume中以拓展volume|
|volume replace-brick|volume replace-brick *volname old-brick new-brick* |用新的brick来替换旧的brick|
|volume delete|volume delete *volname* |删除存储卷volname|