OC&lt;=&gt;C

\_\_bridge只做类型转换，但是不修改对象（内存）管理权；

OC=&gt;C

\_\_bridge\_retained（也可以使用CFBridgingRetain）将Objective-C的对象转换为Core Foundation的对象，同时将对象（内存）的管理权交给我们，后续需要使用CFRelease或者相关方法来释放对象；

C=&gt;OC

\_\_bridge\_transfer（也可以使用CFBridgingRelease）将Core Foundation的对象转换为Objective-C的对象，同时将对象（内存）的管理权交给ARC。

