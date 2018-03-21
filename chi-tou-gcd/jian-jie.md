#### 1.GCD简介

**Grand Central Dispatch\(GCD\)**

是 Apple 开发的一个多核编程的较新的解决方法。

特点：

1. 可用于多核的并行计算；

2. 自动管理线程的生命周期\(创建线程，调度任务，销毁线程）

#### 2.GCD的任务和队列

任务：**同步\(sync\)执行和异步\(async\)执行**，两者主要区别在于：是否等待队列的任务执行结束以及是否具备开启新线程的能力；

队列：指执行任务的等待队列，FIFO先进先出

![](https://upload-images.jianshu.io/upload_images/1877784-01267bd211719167.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

队列分为：串行队列和并发队列，两者都符合FIFO原则，两者主要区别在于：执行的顺序不同，以及开启的线程数不同

![](https://upload-images.jianshu.io/upload_images/1877784-4faca27116209f35.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

![](https://upload-images.jianshu.io/upload_images/1877784-97f3931d1b187b11.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)



