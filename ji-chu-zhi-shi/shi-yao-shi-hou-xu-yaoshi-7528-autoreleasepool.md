* autorelease 机制基于 UI framework。因此写非UI framework的程序时，需要自己管理对象生存周期。
* autorelease 触发时机发生在下一次runloop的时候。因此如果在一个大的循环里不断创建autorelease对象，那么这些对象在下一次runloop回来之前将没有机会被释放，可能会耗尽内存。这种情况下，可以在循环内部显式使用@autoreleasepool {}将autorelease 对象释放。
* 自己创建的线程。Cocoa的应用都会维护自己autoreleasepool。因此，代码里spawn的线程，需要显式添加autoreleasepool。注意：如果是使用POSIX API 创建线程，而不是NSThread，那么不能使用Cocoa，因为Cocoa只能在多线程（multithreading）状态下工作。但可以使用NSThread创建一个马上销毁的线程，使得Cocoa进入multithreading状态



