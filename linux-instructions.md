# Linux常用指令
## initctl
initctl： Upstart 事件管理器。可被应用程序用来通知 init 哪些事件发生。initctl list 可类比 Sysvinit 的 chkconfig工具。

## 起停服务
启停服务，除了使用`service xxx-service start|stop|restart`，还可以使用`start|stop|restart xxx-service`的形式。