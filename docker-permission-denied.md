# 非管理员用户使用Docker命令出现的Permission Denied问题
## 问题描述
非管理员用户，安装完Docker以后，执行任何Docker相关的指令，都出现以下提示：
```
Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get http://%2Fvar%2Frun%2Fdocker.sock/v1.39/images/json?filters=%7B%22reference%22%3A%7B%22redis%22%3Atrue%7D%7D: dial unix /var/run/docker.sock: connect: permission denied
```

查到根本原因是由于Docker的守护进程绑定了Unix套接字，而非TCP端口。而默认情况下Unix套接字的所有者是root，所以必须要sudo权限才能访问。
```
Manage Docker as a non-root user

The docker daemon binds to a Unix socket instead of a TCP port. By default that Unix socket is owned by the user root and other users can only access it using sudo. The docker daemon always runs as the root user.

If you don’t want to use sudo when you use the docker command, create a Unix group called docker and add users to it. When the docker daemon starts, it makes the ownership of the Unix socket read/writable by the docker group.
```

## 解决方法
基于文中描述，可以用root用户操作或者sudo指令解决，也可以将现有用户加入docker组解决。这是由于在docker的守护进程启动时，会赋予docker组Unix套接字的读写权限。

具体解决步骤如下：
```shell
sudo groupadd docker #添加docker用户组，一般情况下装完docker就已经有了这个组
sudo gpasswd -a $USER docker #将当前登陆用户加入到docker用户组中
newgrp docker #更新用户组
docker ps #测试docker命令是否可以使用sudo正常使用
```

大功告成。
## 参考文献
> [https://www.cnblogs.com/informatics/p/8276172.html](https://www.cnblogs.com/informatics/p/8276172.html "https://www.cnblogs.com/informatics/p/8276172.html")

