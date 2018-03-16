RunLoop 的核心是基于 mach port 的，其进入休眠时调用的函数是 mach\_msg\(\)。为了解释这个逻辑，下面稍微介绍一下 OSX/iOS 的系统架构。  
[![](https://blog.ibireme.com/wp-content/uploads/2015/05/RunLoop_3.png "RunLoop\_3")](https://blog.ibireme.com/wp-content/uploads/2015/05/RunLoop_3.png)

苹果官方将整个系统大致划分为上述4个层次：  
应用层包括用户能接触到的图形应用，例如 Spotlight、Aqua、SpringBoard 等。  
应用框架层即开发人员接触到的 Cocoa 等框架。  
核心框架层包括各种核心框架、OpenGL 等内容。  
Darwin 即操作系统的核心，包括系统内核、驱动、Shell 等内容，这一层是开源的，其所有源码都可以在[opensource.apple.com](http://opensource.apple.com/)里找到。

我们在深入看一下 Darwin 这个核心的架构：  
[![](https://blog.ibireme.com/wp-content/uploads/2015/05/RunLoop_4.png "RunLoop\_4")](https://blog.ibireme.com/wp-content/uploads/2015/05/RunLoop_4.png)

其中，在硬件层上面的三个组成部分：Mach、BSD、IOKit \(还包括一些上面没标注的内容\)，共同组成了 XNU 内核。  
XNU 内核的内环被称作 Mach，其作为一个微内核，仅提供了诸如处理器调度、IPC \(进程间通信\)等非常少量的基础服务。  
BSD 层可以看作围绕 Mach 层的一个外环，其提供了诸如进程管理、文件系统和网络等功能。  
IOKit 层是为设备驱动提供了一个面向对象\(C++\)的一个框架。

Mach 本身提供的 API 非常有限，而且苹果也不鼓励使用 Mach 的 API，但是这些API非常基础，如果没有这些API的话，其他任何工作都无法实施。在 Mach 中，所有的东西都是通过自己的对象实现的，进程、线程和虚拟内存都被称为”对象”。和其他架构不同， Mach 的对象间不能直接调用，只能通过消息传递的方式实现对象间的通信。”消息”是 Mach 中最基础的概念，消息在两个端口 \(port\) 之间传递，这就是 Mach 的 IPC \(进程间通信\) 的核心。

Mach 的消息定义是在 &lt;mach/message.h&gt; 头文件的，很简单：

| 12345678910111213 | typedefstruct{mach\_msg\_header\_theader;mach\_msg\_body\_tbody;}mach\_msg\_base\_t;typedefstruct{mach\_msg\_bits\_tmsgh\_bits;mach\_msg\_size\_tmsgh\_size;mach\_port\_tmsgh\_remote\_port;mach\_port\_tmsgh\_local\_port;mach\_port\_name\_tmsgh\_voucher\_port;mach\_msg\_id\_tmsgh\_id;}mach\_msg\_header\_t; |
| :--- | :--- |


一条 Mach 消息实际上就是一个二进制数据包 \(BLOB\)，其头部定义了当前端口 local\_port 和目标端口 remote\_port，  
发送和接受消息是通过同一个 API 进行的，其 option 标记了消息传递的方向：

| 12345678 | mach\_msg\_return\_tmach\_msg\(mach\_msg\_header\_t \*msg,mach\_msg\_option\_toption,mach\_msg\_size\_tsend\_size,mach\_msg\_size\_trcv\_size,mach\_port\_name\_trcv\_name,mach\_msg\_timeout\_ttimeout,mach\_port\_name\_tnotify\); |
| :--- | :--- |


为了实现消息的发送和接收，mach\_msg\(\) 函数实际上是调用了一个 Mach 陷阱 \(trap\)，即函数mach\_msg\_trap\(\)，陷阱这个概念在 Mach 中等同于系统调用。当你在用户态调用 mach\_msg\_trap\(\) 时会触发陷阱机制，切换到内核态；内核态中内核实现的 mach\_msg\(\) 函数会完成实际的工作，如下图：  
[![](https://blog.ibireme.com/wp-content/uploads/2015/05/RunLoop_5.png "RunLoop\_5")](https://blog.ibireme.com/wp-content/uploads/2015/05/RunLoop_5.png)

这些概念可以参考维基百科: [System\_call](http://en.wikipedia.org/wiki/System_call)、[Trap\_\(computing\)](http://en.wikipedia.org/wiki/Trap_%28computing%29)。

RunLoop 的核心就是一个 mach\_msg\(\) \(见上面代码的第7步\)，RunLoop 调用这个函数去接收消息，如果没有别人发送 port 消息过来，内核会将线程置于等待状态。例如你在模拟器里跑起一个 iOS 的 App，然后在 App 静止时点击暂停，你会看到主线程调用栈是停留在 mach\_msg\_trap\(\) 这个地方。

