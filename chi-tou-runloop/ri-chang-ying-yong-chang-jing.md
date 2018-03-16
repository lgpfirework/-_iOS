### 4.0 AFNetworking的使用

[AFURLConnectionOperation](https://github.com/AFNetworking/AFNetworking/blob/master/AFNetworking%2FAFURLConnectionOperation.m) 这个类是基于 NSURLConnection 构建的，其希望能在后台线程接收 Delegate 回调。为此 AFNetworking 单独创建了一个线程，并在这个线程中启动了一个 RunLoop：

![](/assets/import.png)

RunLoop 启动前内部必须要有至少一个 Timer/Observer/Source，所以 AFNetworking 在 \[runLoop run\] 之前先创建了一个新的 NSMachPort 添加进去了。通常情况下，调用者需要持有这个 NSMachPort \(mach\_port\) 并在外部线程通过这个 port 发送消息到 loop 内；但此处添加 port 只是为了让 RunLoop 不至于退出，并没有用于实际的发送消息。

![](/assets/import1.png)

当需要这个后台线程执行任务时，AFNetworking 通过调用 \[NSObject performSelector:onThread:..\] 将这个任务扔到了后台线程的 RunLoop 中。

### 4.1 NSTimer的使用

NSTimer的使用方法在讲解`CFRunLoopTimerRef`类的时候详细讲解过，具体参考上边 **2.3 CFRunLoopTimerRef**。

### 4.2 ImageView推迟显示

有时候，我们会遇到这种情况：  
 当界面中含有UITableView，而且每个UITableViewCell里边都有图片。这时候当我们滚动UITableView的时候，如果有一堆的图片需要显示，那么可能会出现卡顿的现象。

怎么解决这个问题呢？

这时候，我们应该推迟图片的显示，也就是ImageView推迟显示图片。有两种方法：

#### 1. 监听UIScrollView的滚动

因为UITableView继承自UIScrollView，所以我们可以通过监听UIScrollView的滚动，实现UIScrollView相关delegate即可。

#### 2. 利用PerformSelector设置当前线程的RunLoop的运行模式

利用`performSelector`方法为UIImageView调用`setImage:`方法，并利用`inModes`将其设置为RunLoop下NSDefaultRunLoopMode运行模式。代码如下：

```
[
self
.imageView performSelector:
@selector
(setImage:) withObject:[
UIImage
 imageNamed:
@"tupian"
] afterDelay:
4.0
 inModes:
NSDefaultRunLoopMode
];
```

* \(void\)touchesBegan:\(NSSet&lt;UITouch\*&gt;\*\)touches withEvent:\(UIEvent\*\)event { \[

self.imageView performSelector:@selector\(setImage:\) withObject:\[UIImage imageNamed:@"tupian"\] afterDelay:4.0 inModes:@\[NSDefaultRunLoopMode\]\];

}

### 4.3 后台常驻线程（很常用）

我们在开发应用程序的过程中，如果后台操作特别频繁，经常会在子线程做一些耗时操作（下载文件、后台播放音乐等），我们最好能让这条线程永远常驻内存。

那么怎么做呢？

添加一条用于常驻内存的强引用的子线程，在该线程的RunLoop下添加一个Sources，开启RunLoop。

具体实现过程如下：

1. 在项目的ViewController.m中添加一条强引用的thread线程属性，如下图：

![](https://upload-images.jianshu.io/upload_images/1877784-ffce8301e3bd4736.png?imageMogr2/auto-orient/strip|imageView2/2/w/700)

在viewDidLoad中创建线程self.thread，使线程启动并执行run1方法，代码如下。在[Demo](https://link.jianshu.com?t=https%3A%2F%2Fgithub.com%2Flianai911%2FYSC-RunLoopDemo)中，请在viewDidLoad调用

`[self showDemo4];`

方法。

```
- (void)viewDidLoad {
    [super viewDidLoad];    
// 创建线程，并调用run1方法执行任务
self.thread = [[NSThread alloc] initWithTarget:self selector:@selector(run1) object:nil];

// 开启线程

    [self.thread start];    
}

- (void) run1
{

// 这里写任务
    NSLog(@"----run1-----");

// 添加下边两句代码，就可以开启RunLoop，之后self.thread就变成了常驻线程，可随时添加任务，并交于RunLoop处理

    [[NSRunLoop currentRunLoop] addPort:[NSPort port] forMode:NSDefaultRunLoopMode];
    [[NSRunLoop currentRunLoop] run];

// 测试是否开启了RunLoop，如果开启RunLoop，则来不了这里，因为RunLoop开启了循环。
    NSLog(@"未开启RunLoop");
}
```

1. 运行之后发现打印了
   **----run1-----**，而**未开启RunLoop**则未打印。

这时，我们就开启了一条常驻线程，这样我们就实现了常驻线程的需求。

