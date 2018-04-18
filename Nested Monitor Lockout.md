
## 管程的理解
管程（英文：Monitor，字面上理解，Monitor，看着就像是一种监视进程或者线程的东西。）是一种程序结构。在这种结构内的子程序，对象或者模块，能够在多线程情况下**互斥**访问共享资源。所谓的共享资源，可能是一堆线程共享的变量，或是一些共享的硬件设备。管程做的事情，就是实现了在一个时间点内，最多让一个线程执行管程结构内的某个子程序。
管程还有个机制，就是在多个线程竞争条件下，可以临时放弃互斥访问。等到某些条件满足后，重新回复它的互斥访问。
一个管程包含：
* 多个彼此交互并共用资源的线程。
* 多个与资源使用有关的变量。
* 一个互斥锁
* 一个用来避免[竞态条件](https://zh.wikipedia.org/wiki/竞态条件)的不变量。

### Java对管程的支持
Java中提供了synchorinzed关键字来提供对管程的支持，通过对方法或者代码块加synchorinzedl来保证同一时间仅允许一个线程来调用代码块。

## 参考文献
[https://zh.wikipedia.org/zh-cn/管程](https://zh.wikipedia.org/zh-cn/管程)
[https://www.zhihu.com/question/30641734](https://www.zhihu.com/question/30641734)
[https://blog.csdn.net/louxuez/article/details/38964089f](https://blog.csdn.net/louxuez/article/details/38964089)
[https://www.cnblogs.com/youxin/p/3587038.html](https://www.cnblogs.com/youxin/p/3587038.html)