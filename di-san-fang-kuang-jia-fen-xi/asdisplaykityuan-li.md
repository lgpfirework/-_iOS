[关于界面流畅和ASDiaplayKit的原理分析](https://blog.ibireme.com/2015/11/12/smooth_user_interfaces_for_ios/)

阻塞主线程的任务大致分为以下三类：

1.文本的宽高计算，视图的布局计算

2.文本渲染，图片解码，图形绘制

3.对象的创建，调整，销毁。

而前两种可以通过各种方式异步执行，\(UIKit&Core Animation要求在主线程执行\)

ASDK的目的就是尽量把这些任务从主线程挪走，而挪不走的，就尽量优化性能。

根据控件是否需要响应触摸事件：layerBacked==YES  -&gt;

ASDisplayNode充当了UIView，节省资源，提高效率

线程安全问题：UIView&CAlayer  : 线程不安全，只能在主线程创建销毁

ASDisplayNoe     : 线程安全，可以在后台线程创建修改和销毁

Node刚创建时 ：

1.内部不会生成UIView和CALayer,  直到第一次在主线程访问view或者layer属性时才会在内部生成对应的对象。

2.当它的属性（frame/transform）改变后，不会立刻同步到其持有的view或者layer，而是将被改变的属性保存到一个内部的中间变量上去，稍后需要时通过某种机制一次性设置到内部的view和layer上去

以下对上面提到的某种机制做解释：

原理：ASDK模仿Core Animation的动画机制

1. Core Animation在Runloop中注册一个Observer，监听事件

2. 当一个触摸事件到来，Runloop被唤醒，代码执行相应的操作

3.操作结果最终被CALayer捕获，通过CATransaction提交到一个中间状态去

4.操作结束后，Runloop即将进入休眠或退出状态，此状态会被1中注册的Observer捕获，此时在

回调中就会把所有的中间状态合并提交到GPU去显示；

5.如果此处有动画，CA会通过DisplayLink等机制多次触发相关流程

ASDK模仿上面的CA机制，所有针对ASNode的修改和提交，对于其中需要放到主线程去执行的

ASNode会把任务用ASAsyncTransaction\(Group\)封装并提交到一个全局容器中去，ASDK也在

Runloop中注册了一个类似CA的Observer，但优先级比CA的低，当CA处理完事件后，ASDK就

会执行该loop内提交的所有任务

通过以上这种机制，ASDK可以在合适的机会把异步，并发的操作同步到主线程去，并获得不错的性能

* \(void\)viewDidLoad {

  \[super viewDidLoad\];

  // 创建观察者

  CFRunLoopObserverRef observer = CFRunLoopObserverCreateWithHandler\(CFAllocatorGetDefault\(\), kCFRunLoopAllActivities, YES, 0, ^\(CFRunLoopObserverRef observer, CFRunLoopActivity activity\)

  { NSLog\(@"监听到RunLoop发生改变---%zd",activity\); }\);

  // 添加观察者到当前RunLoop中

  CFRunLoopAddObserver\(CFRunLoopGetCurrent\(\), observer, kCFRunLoopDefaultMode\);

  // 释放observer，最后添加完需要释放掉

  CFRelease\(observer\);

  }



