一般来讲，一个线程一次只能执行一个任务，执行完成后线程就会退出。如果我们需要一个机制，让线程能随时处理事件但并不退出，通常的代码逻辑是这样的：

### 1.1 什么是RunLoop？

可以理解为字面意思：Run表示运行，Loop表示循环。结合在一起就是运行的循环的意思。哈哈，我更愿意翻译为『跑圈』。直观理解就像是不停的跑圈。

RunLoop实际上是一个对象，这个对象在循环中用来处理程序运行过程中出现的各种事件（比如说触摸事件、UI刷新事件、定时器事件、Selector事件），从而保持程序的持续运行；而且在没有事件处理的时候，会进入睡眠模式，从而节省CPU资源，提高程序性能。

### 1.2 RunLoop和线程

RunLoop和线程是息息相关的，我们知道线程的作用是用来执行特定的一个或多个任务，但是在默认情况下，线程执行完之后就会退出，就不能再执行任务了。这时我们就需要采用一种方式来让线程能够处理任务，并不退出。所以，我们就有了RunLoop。

1. 一条线程对应一个RunLoop对象，每条线程都有唯一一个与之对应的RunLoop对象。
2. 我们只能在当前线程中操作当前线程的RunLoop，而不能去操作其他线程的RunLoop。
3. RunLoop对象在第一次获取RunLoop时创建，销毁则是在线程结束的时候。
4. 主线程的RunLoop对象系统自动帮助我们创建好了\(原理如下\)，而子线程的RunLoop对象需要我们主动创建。

### 1.3 默认情况下主线程的RunLoop原理

我们在启动一个iOS程序的时候，系统会调用创建项目时自动生成的main.m的文件。main.m文件中`UIApplicationMain`函数内部帮我们开启了主线程的RunLoop，`UIApplicationMain`内部拥有一个无线循环的代码。上边的代码中开启RunLoop的过程可以简单的理解为如下代码：

```
int main(int argc, char* argv[]) {        
    BOOL running = YES;
do
 {

   // 执行各种任务，处理各种事件
   // ......

   } 
while
 (running);
return 0 ;
}
```

从上边可看出，程序一直在do-while循环中执行，所以UIApplicationMain函数一直没有返回，我们在运行程序之后程序不会马上退出，会保持持续运行状态。

下图是苹果官方给出的RunLoop模型图。

![](https://upload-images.jianshu.io/upload_images/1877784-6ab632fc118e31f3.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/484)

官方RunLoop模型图

从上图中可以看出，RunLoop就是线程中的一个循环，RunLoop在循环中会不断检测，通过Input sources（输入源）和Timer sources（定时源）两种来源等待接受事件；然后对接受到的事件通知线程进行处理，并在没有事件的时候进行休息。

