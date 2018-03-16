typedef \_\_attribute\_\_\(\(NSObject\)\) CGGradientRef GradientObject;

@property \(nonatomic, strong\) GradientObject storedGradient;

CGGradientRef属于CoreFoundation框架的,也就是非OC对象,加上attribute\(\(NSObject\)\)后,

GradientObject的内存管理会被当做OC对象来对待.

\_\_attribute\_\_机制是GNU C的一大特色，增强编译器的功能，带来更多的检查，更多的优化。可以设置函数特性,变量特性,类型特性,标签特性,枚举特性，语法格式是\_\_attribute\_\_\(\(attributes\)\)。不同的编译器在细节上会有差别，持有的特性也不尽相同。下面介绍几种Objective-C中可以使用的特性。

* \_\_attribute\_\_\(\(deprecated\)\)
  可以用来修饰变量，方法，类和协议，表明被废弃，如果使用，编译器会发出警告。可以添加说明，用法\_\_attribute\_\_\(\(deprecated\("use Another class."\)\)\)。
* \_\_attribute\_\_\(\(unavailable\)\)
  可以用来修饰变量，方法，类和协议，表明不可用，如果使用，编译器会发出错误。同deprecated，可以添加说明。
* \_\_attribute\_\_\(\(NSObject\)\)
  用来修饰属性，表明Core Fundation类型的属性应该按照Objective-C的对象来进行内存管理。比如 @property\(retain\) \_\_attribute\_\_\(\(NSObject\)\) CFDictionaryRef myDictionary;
* \_\_attribute\_\_\(\(objc\_designated\_initializer\)\)
  用来修饰类的designated initializer初始化方法，如果修饰的方法里没有调用超类的 designated initializer，编译器会发出警告。可以简写成NS\_DESIGNATED\_INITIALIZER。
* \_\_attribute\_\_\(\(constructor\)\)
  用来修饰函数，函数的返回值为void，参数为void，会在main函数开始之前调用。可以指定优先级（大于100，0到100为系统保留），多个修饰的函数，按照优先级高低顺序执行。
* \_\_attribute\_\_\(\(destructor\)\)
  用来修饰函数，返回值和参数同constructor，会在main函数结束之后调用。优先级同constructor。

deprecated、unavailable、NSObject、objc\_designated\_initializer应该涵盖了大部分的应用场景，constructor/destructor 目前只在libextobjc中见到运用，在main函数之前可以干很多黑魔法的事。

更多\_\_attribute\_\_介绍，可以参考 

[http://blog.sunnyxx.com/2016/05/14/clang-attributes/](http://blog.sunnyxx.com/2016/05/14/clang-attributes/)

