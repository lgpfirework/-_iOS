一是主动断开循环引用，

二是使用弱引用的方式避免循环引用

弱引用实现原理：

系统对于每一个有弱引用的对象，都维护一个表来记录它所有的弱引用的指针地址。这样，当一个对象的引用计数为 0 时，系统就通过这张表，找到所有的弱引用指针，继而把它们都置成 nil。
