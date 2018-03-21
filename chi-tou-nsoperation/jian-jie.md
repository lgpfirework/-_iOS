#### 1. NSOperation、NSOperationQueue 简介

NSOperation、NSOperationQueue 是苹果提供给我们的一套多线程解决方案。

实际上 NSOperation、NSOperationQueue 是基于 GCD 更高一层的封装，完全面向对象。

但是比 GCD 更简单易用、代码可读性也更高。

1. 相比GCD有什么不同之处：
   1. 添加任务之间的依赖关系，方便的控制执行顺序

   2. 设定操作执行的优先级；

   3. 可以很方便的取消一个操作的执行。

##### NSOperation：

* 我们使用NSOperation的子类**NSInvocationOperation/NSBlockOperation/自定义子类来封装操作（即执行的代码）**

##### NSOperationQueue：

* 存放操作的队列。（不同于GCD中的调度队列是遵守先进先出的原则）对于添加到队列中的操作，首先进入准备就绪的状态，然后由各操作的优先级决定开始执行顺序；

* 操作队列通过设置最大并发操作数**maxConcurrentOperationCount**来控制并发，串行；

* 操作队列有两种：主队列（主线程执行）&自定义队列（后台执行）。

#### 2.与线程的关系

##### 当操作不添加到队列中：

* 直接调用操作的start方法时，是在**当前线程**执行的。不开启新线程，（NSBlockOperation比较特殊，addExecutionBlock：添加的操作较多时，系统会开启新线程）

##### 当操作添加到队列中：

* 使用 NSOperation 子类创建操作，并使用`addOperation:`将操作加入到操作队列后能够开启新线程，进行并发执行。

#### 3.控制串行，并发

maxConcurrentOperationCount：控制的不是并发线程的数量，而是一个队列中同时能并发执行的最大操作数。而且一个操作也并非只能在一个线程中运行。

#### 4.操作依赖

* `- (void)addDependency:(NSOperation *)op;` 添加依赖，使当前操作依赖于操作 op 的完成。

* `- (void)removeDependency:(NSOperation *)op;`移除依赖，取消当前操作对操作 op 的依赖。
* `@property (readonly, copy) NSArray<NSOperation *>*dependencies;`在当前操作开始执行之前完成执行的所有操作对象数组。

#### 5.操作优先级

NSOperation 提供了`queuePriority`（优先级）属性

**注意：优先级不能取代依赖关系**





