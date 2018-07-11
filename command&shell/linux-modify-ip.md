# 解决Ubuntu修改静态IP之后无法立即生效
Ubuntu在修改了IP地址之后，往往无法立即生效。修改静态IP之后尝试过重启指令`service network restart`，但是似乎并没有什么用。使用`ifconfig`指令查看会发现IP地址依然是旧的IP地址。
遇到这种情况有两个办法：
### 方法一：重启系统
重启系统最为直接，但是花费时间很长。特别服务器，系统重启需要花费大量时间进行自检。
### 方法二：清空原IP并重启网卡
指令如下：
```shell
ip addr flush dev ens3
ifdown ens33
ifup ens33
```
### 方法三：清空原有IP后重启网络服务
```shell
ip addr flush dev ens3
/etc/init.d/networking restart
```