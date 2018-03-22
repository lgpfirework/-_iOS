`__weak` 本身是可以避免循环引用的问题的，但是其会导致外部对象释放了之后，block 内部也访问不到这个对象的问题，我们可以通过在 block 内部声明一个 `__strong` 的变量来指向 weakObj，使外部对象既能在 block 内部保持住，又能避免循环引用的问题。

`__block` 本身无法避免循环引用的问题，但是我们可以通过在 block 内部手动把 blockObj 赋值为 nil 的方式来避免循环引用的问题。另外一点就是 `__block` 修饰的变量在 block 内外都是唯一的，要注意这个特性可能带来的隐患。

但是`__block`有一点：这只是限制在ARC环境下。在非arc下，\_\_block是可以避免引用循环的





\_\_block修饰对象时，传递的是指针，

NSMutableString \*a = \[NSMutableString stringWithString:@"Tom"\];

  NSLog\(@"\n 定以前：------------------------------------\n\

        a指向的堆中地址：%p；a在栈中的指针地址：%p", a, &a\);               //a在栈区

  void \(^foo\)\(void\) = ^{

      a.string = @"Jerry";

      NSLog\(@"\n block内部：------------------------------------\n\

       a指向的堆中地址：%p；a在栈中的指针地址：%p", a, &a\);               //a在栈区

      a = \[NSMutableString stringWithString:@"William"\];

  };

  foo\(\);

  NSLog\(@"\n 定以后：------------------------------------\n\

        a指向的堆中地址：%p；a在栈中的指针地址：%p", a, &a\);               //a在栈区

  [![](https://camo.githubusercontent.com/548c6cfd7a1d7084da25be9d4ee76e51a907fd84/687474703a2f2f6936362e74696e797069632e636f6d2f333465756863792e6a7067 "enter image description here")](https://camo.githubusercontent.com/548c6cfd7a1d7084da25be9d4ee76e51a907fd84/687474703a2f2f6936362e74696e797069632e636f6d2f333465756863792e6a7067)

这里的a已经由基本数据类型，变成了对象类型。**block会对对象类型的指针进行copy，copy到堆中**，但并不会改变该指针所指向的堆中的地址，所以在上面的示例代码中，block体内修改的实际是a指向的堆中的内容。

但如果我们尝试像上面图片中的65行那样做，结果会编译不通过，那是因为此时你在修改的就不是堆中的内容，而是栈中的内容。

上文已经说过：**Block不允许修改外部变量的值**，这里所说的外部变量的值，指的是栈中指针的内存地址。栈区是红灯区，堆区才是绿灯区。

