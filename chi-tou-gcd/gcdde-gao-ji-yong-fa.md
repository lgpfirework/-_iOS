#### 1.GCD的栅栏方法：dispatch\_barrier\_async

`dispatch_barrier_async`函数会等待前边追加到并发队列中的任务全部执行完毕之后，再将指定的任务追加到该并发队列中。然后在`dispatch_barrier_async`函数追加的任务执行完毕之后，并发队列才恢复为一般动作，接着追加任务到该并发队列并开始执行。具体如下图所示：

![](https://upload-images.jianshu.io/upload_images/1877784-4d6d77fafd3ad007.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

#### 2.GCD的延时执行方法：dispatch\_after

注意：`dispatch_after`函数并不是在指定时间之后才开始执行处理，而是在指定时间之后将任务追加到主队列中让其执行

#### 3.GCD的一次性执行代码：dispatch\_once

常用在创建单例中。

 能保证某段代码在程序运行过程中只被执行1次，并且即使在多线程的环境下，`dispatch_once`也可以保证线程安全。

#### 4.GCD的快速迭代方法：dispatch\_apply

* 如果是在串行队列中使用 `dispatch_apply`，那么就和 for 循环一样，按顺序同步执行。可这样就体现不出快速迭代的意义了。

* 我们可以利用并发队列进行异步执行。比如说遍历 0~5 这6个数字，`dispatch_apply`可以 在多个线程中同时（异步）遍历多个数字。

*  还有一点，无论是在串行队列，还是异步队列中，dispatch\_apply 都会等待全部任务执行完毕，这点就像是同步操作，也像是队列组中的 `dispatch_group_wait`方法。

#### 5.GCD的队列组：dispatch\_group

* 应用场景：分别异步执行2个耗时任务，然后当2个耗时任务都执行完毕后再回到主线程执行任务。
* 实现：

 调用队列组的 `dispatch_group_async`先把任务放到队列中，然后将队列放入队列组中。或者使用队列组的 `dispatch_group_enter、dispatch_group_leave`组合 来实现`dispatch_group_async`。

调用队列组的 `dispatch_group_notify`回到指定线程执行任务。或者使用 `dispatch_group_wait`回到当前线程继续向下执行（会阻塞当前线程）

#### 6.GCD的信号量：dispatch\_semaphore

应用场景：1.保持线程同步，将异步执行任务转换为同步执行任务 2.保证线程安全，为线程加锁



