NSTimer受runloop的影响，由于runloop需要处理很多任务，导致NSTimer的精度降低，在日常开发中，如果我们需要对定时器的精度要求很高的话，可以考虑dispatch\_source\_t去实现 。dispatch\_source\_t精度很高，系统自动触发，系统级别的源。下面是通过dispatch\_source\_t 创建 计时器的例子

 dispatch\_queue\_t queue = dispatch\_get\_global\_queue\(0, 0\);

self.timer = dispatch\_source\_create\(DISPATCH\_SOURCE\_TYPE\_TIMER, 0, 0, queue\);

//开始时间

 dispatch\_time\_t start = dispatch\_time\(DISPATCH\_TIME\_NOW, 3.0 \* NSEC\_PER\_SEC\);

//间隔时间

 uint64\_t interval = 2.0 \* NSEC\_PER\_SEC;

 dispatch\_source\_set\_timer\(self.timer, start, interval, 0\);

 //设置回调

  dispatch\_source\_set\_event\_handler\(self.timer, ^{

     NSLog\(@"----self.timer---"\);

}\);

//启动timer

 dispatch\_resume\(self.timer\);

![](/assets/import3.png)

