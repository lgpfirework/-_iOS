下面我们来了解一下Core Foundation框架下关于RunLoop的5个类，只有弄懂这几个类的含义，我们才能深入了解RunLoop运行机制。

1. CFRunLoopRef：代表RunLoop的对象
2. CFRunLoopModeRef：RunLoop的运行模式
3. CFRunLoopSourceRef：就是RunLoop模型图中提到的输入源/事件源
4. CFRunLoopTimerRef：就是RunLoop模型图中提到的定时源
5. CFRunLoopObserverRef：观察者，能够监听RunLoop的状态改变

![](https://upload-images.jianshu.io/upload_images/1877784-2177aa2828b1ad34.png?imageMogr2/auto-orient/strip|imageView2/2/w/700)

一个RunLoop对象（CFRunLoopRef）中包含若干个运行模式（CFRunLoopModeRef）。而每一个运行模式下又包含若干个输入源（CFRunLoopSourceRef）、定时源（CFRunLoopTimerRef）、观察者（CFRunLoopObserverRef）。

* 每次RunLoop启动时，只能指定其中一个运行模式（CFRunLoopModeRef），这个运行模式（CFRunLoopModeRef）被称作CurrentMode。
* 如果需要切换运行模式（CFRunLoopModeRef），只能退出Loop，再重新指定一个运行模式（CFRunLoopModeRef）进入。
* 这样做主要是为了分隔开不同组的输入源（CFRunLoopSourceRef）、定时源（CFRunLoopTimerRef）、观察者（CFRunLoopObserverRef），让其互不影响 。



**CFRunLoopSourceRef**是事件产生的地方。Source有两个版本：Source0 和 Source1。  
• Source0 只包含了一个回调（函数指针），它并不能主动触发事件。使用时，你需要先调用 CFRunLoopSourceSignal\(source\)，将这个 Source 标记为待处理，然后手动调用 CFRunLoopWakeUp\(runloop\) 来唤醒 RunLoop，让其处理这个事件。  
• Source1 包含了一个 mach\_port 和一个回调（函数指针），被用于通过内核和其他线程相互发送消息。这种 Source 能主动唤醒 RunLoop 的线程，其原理在下面会讲到。

**CFRunLoopTimerRef**是基于时间的触发器，它和 NSTimer 是toll-free bridged 的，可以混用。其包含一个时间长度和一个回调（函数指针）。当其加入到 RunLoop 时，RunLoop会注册对应的时间点，当时间点到时，RunLoop会被唤醒以执行那个回调。

**CFRunLoopObserverRef**是观察者，每个 Observer 都包含了一个回调（函数指针），当 RunLoop 的状态发生变化时，观察者就能通过回调接受到这个变化。可以观测的时间点有以下几个：

| 12345678 | typedefCF\_OPTIONS\(CFOptionFlags,CFRunLoopActivity\){kCFRunLoopEntry=\(1UL&lt;&lt;0\),// 即将进入LoopkCFRunLoopBeforeTimers=\(1UL&lt;&lt;1\),// 即将处理 TimerkCFRunLoopBeforeSources=\(1UL&lt;&lt;2\),// 即将处理 SourcekCFRunLoopBeforeWaiting=\(1UL&lt;&lt;5\),// 即将进入休眠kCFRunLoopAfterWaiting=\(1UL&lt;&lt;6\),// 刚从休眠中唤醒kCFRunLoopExit=\(1UL&lt;&lt;7\),// 即将退出Loop}; |
| :--- | :--- |


上面的 Source/Timer/Observer 被统称为**mode item**，一个 item 可以被同时加入多个 mode。但一个 item 被重复加入同一个 mode 时是不会有效果的。如果一个 mode 中一个 item 都没有，则 RunLoop 会直接退出，不进入循环。

