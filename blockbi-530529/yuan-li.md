#### 如何理解：

可以理解为一个匿名的函数，就是预先准备好一段代码，在需要的时候使用。

///////////////////////////////

* 准备一个main.m文件
* 执行指令：clang -rewrite-objc main.m

* 查看底层实现的C++源码

///////////////////////////////

#### 底层实现原理：

block是一个指针结构体。

其中有几个重要的结构体和函数，如下：

\_\_block\_impl：这是一个结构体，也是C面向对象的体现，可以理解为block的基类;

\_\_main\_block\_impl\_0: 可以理解为block变量;

\_\_main\_block\_func\_0: 可以理解为匿名函数；

\_\_main\_block\_desc\_0:block的描述, Block\_size;

#### block捕获的外部变量分为4种：

* 局部变量
* 静态变量
* 全局变量
* 静态全局变量

#### 其中：

* 对于全局变量和静态全局变量被Block捕获后，可在Block内部修改其值，原理很简单，他们是全局变量，作用域很广的；

* 静态变量传递给Block是内存地址值，所以能在Block里面直接改变值。

* 而至于局部变量，则需要通过添加\_\_block修改其值了。

底层实现按照\_\_block可以分两种:

* 不添加\_\_block，在block捕获外部局部变量时，传递的是外部变量值，所以无法在block内部修改值；
* 添加\_\_block，在block捕获外部局部变量时，传递的是由外部变量生成的结构体的指针地址，所以可在block内部修改局部变

**（备注：无论有无\_\_block，当外部变量为对象时，会进行retain+1操作，故而会引起循环引用）**

### Block有三种形式：

* \_NSConcreteStackBlock（栈区block）
* \_NSConcreteMallocBlock \(堆区block\)

* \_NSConcreteGlobalBlock \(全局区block\)

对于以上三者的区别，可以从两个角度来看：

#### 1.从捕获外部变量的角度看：

\_NSConcreteStackBlock：只用到外部局部变量、成员属性变量，且没有强指针引用的block都是StackBlock。

\_NSConcreteMallocBlock：有强指针引用或copy修饰的成员属性引用的block会被复制一份到堆中成为MallocBlock

\_NSConcreteGlobalBlock：没有用到外界变量或只用到全局变量、静态变量的block为\_NSConcreteGlobalBlock

#### 2.从持有对象的角度上看：

\_NSConcreteStackBlock：是不持有对象的。

\_NSConcreteMallocBlock：是持有对象的。

\_NSConcreteGlobalBlock：也不持有对象。

#### 以下4种情况系统都会默认调用copy方法把Block赋复制到堆上

1.手动调用copy

2.Block是函数的返回值

3.Block被强引用，Block被赋值给\_\_strong或者id类型

4.调用系统API入参中含有usingBlcok的方法

**备注：当Block为函数参数的时候，就需要我们手动的copy一份到堆上了**

