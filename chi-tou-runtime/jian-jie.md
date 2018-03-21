#### 1.1什么是RunTime?

OC底层的一套C语言的API,编译器最终都会将OC代码转化为**运行时代码，**

#### 1.2RunTime的日常开发运用

* 比如将OC转为运行时的代码，在探索Block底层实现的时候就是通过终端编译命令clang -rewrite-objc XX.m实现的；

* 比如调用OC方法的本质就是通过objc\_sgSend\(\)发消息；

* 比如动态交换两个方法的实现/拦截系统方法&lt;利用分类Category&gt;;

  * class\_getClassMethod\(\)/class\_getInstanceMethod\(\)/method\_exchangeImplementations\(\)

* 比如实现分类Category也可以增加属性；

  * objc\_setAssociatedObject\(\)/objc\_getAssociatedObject\(\)

* 比如动态的添加对象的成员变量和成员方法；

  * class\__addIvar\(\)/class\_addMethod\(\)_
  * 动态添加方法，在像一个对象发送对象没有实现的方法时，在崩溃前会有三次拯救的机会，在这三次机会中可以动态像对象添加方法（resolveInstanceMethod\(\)返回YES,则通过class\_addMethod动态添加方法）

* 比如获取某个类的所有成员方法，所有成员变量。

* * class\_copyIvarList\(\)；
  * 比如实现NSCoding的自动归档/解档；
  * 比如实现字典和模型的自动转换；



