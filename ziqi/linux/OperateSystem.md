# 特点
并发

共享 

虚拟 
    

异步
# 内核
稳定、高效、可移植

上层是用户空间，下层是内核，中间靠GNU C Library连接

内核3层：

    系统调用接口SCI
    独立于体系结构的代码
    依赖于体系结构的代码

组件：

    系统调用接口

    进程管理
        没有区分线程和进程
        多线程的调度都是O(1)
    
    内存管理
        内存页
        内存被耗光，可以移到磁盘上（交换）

    VFS层次：
        API（open、close、……）
        VFS
        文件系统抽象（定义了上层函数的实现方式）、插件
        缓冲区（优化对无力设备的访问）
        设备驱动（访问物理设备的接口）
    
    网络堆栈
        SOCKET（SCI）
        TCP
        IP
    
    设备驱动程序

# 进程 线程
[进程线程区别](https://www.zhihu.com/question/25532384)
# 进程状态
https://www.jianshu.com/p/ac9ce2afd126

# 进程间通信
https://www.jianshu.com/p/c1015f5ffa74

高级管道通信？

所以管道是在内存中还是内核中？

# 死锁 活锁 饥饿
[死锁 活锁 饥饿](https://www.cnblogs.com/ktgu/p/3529143.html)
