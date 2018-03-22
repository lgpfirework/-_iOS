#### 1.runtime如何实现weak属性？

首先weak属性表示的是一种非拥有关系，在为weak属性设置新值时，既不保留新值，也不释放旧值；

weak的底层runtime实现原理就是内存中会有一个哈希表去存储所有weak对象，将weak指向的对象的内存地址（比如a）作为key，当weak对象的引用计数为0的时候，在此表中找到所有的以a为key的weak引用对象并将其置为nil

### 2.runtime如何通过selector找到对应的IMP地址？

* 对象方法列表是存放在isa指针指向的类对象中的；
* 类方法列表是存放在isa指针指向的元类对象中的；
* 方法列表（哈希表）中每个方法结构体中记录着**方法名，方法实现，参数类型等，**selector的本质就是方法名，通过方法名就可以在方法列表中找到对应的方法实现。

#### 3. \_objc\_msgForward函数是做什么的？直接调用它将会发生什么？

消息转发。当向一个对象发送一个对象没有实现的消息时，\_objc\_msgForward会尝试做消息转发。

直接调用是非常危险的，是一把双刃刀，用不好就是crash，JSPatch用到了这个

#####          `_objc_msgForward`在进行消息转发的过程中会涉及以下这几个方法

* 调用`resolveInstanceMethod:`方法 \(或`resolveClassMethod:`\)。允许用户在此时为该 Class 动态添加实现。如果有实现了，则调用并返回YES，那么重新开始`objc_msgSend`流程。这一次对象会响应这个选择器，一般是因为它已经调用过`class_addMethod`。如果仍没实现，继续下面的动作。

* 调用`forwardingTargetForSelector:`方法，尝试找到一个能响应该消息的对象。如果获取到，则直接把消息转发给它，返回非 nil 对象。否则返回 nil ，继续下面的动作。注意，这里不要返回 self ，否则会形成死循环。

* 调用`methodSignatureForSelector:`方法，尝试获得一个方法签名。如果获取不到，则直接调用`doesNotRecognizeSelector`抛出异常。如果能获取，则返回非nil：创建一个 NSlnvocation 并传给`forwardInvocation:`。

* 调用`forwardInvocation:`方法，将第3步获取到的方法签名包装成 Invocation 传入，如何处理就在这里面了，并返回非ni。

* 调用`doesNotRecognizeSelector:`，默认的实现是抛出异常。如果第3步没能获得一个方法签名，执行该步骤。

  


#### 4.OC调用方法的过程

OC是动态进行时语言，每个方法在运行时都会被动态转为消息发送，即objc\_msgSend\(\);

过程如下：

\* objc在向一个对象发送消息时，runtime库会根据对象的isa指针找到该对象实际所属的类

\* 然后在该类中的方法列表以及其父类方法列表中寻找方法运行

\* 如果，在最顶层的父类（一般也就NSObject）中依然找不到相应的方法时，程序在运行时会挂掉并抛出异常unrecognized selector sent to XXX

\* 但是在这之前，objc的运行时会给出三次拯救程序崩溃的机会

