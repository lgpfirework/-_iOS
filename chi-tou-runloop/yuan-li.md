![](https://upload-images.jianshu.io/upload_images/1877784-94c6cdb3a7864593.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

                                                                                     RunLoop运行逻辑图



在每次运行开启RunLoop的时候，所在线程的RunLoop会自动处理之前未处理的事件，并且通知相关的观察者。

具体的顺序如下：

1. 通知观察者RunLoop已经启动
2. 通知观察者即将要开始的定时器
3. 通知观察者任何即将启动的非基于端口的源
4. 启动任何准备好的非基于端口的源
5. 如果基于端口的源准备好并处于等待状态，立即启动；并进入步骤9
6. 通知观察者线程进入休眠状态
7. 将线程置于休眠知道任一下面的事件发生：
   * 某一事件到达基于端口的源
   * 定时器启动
   * RunLoop设置的时间已经超时
   * RunLoop被显示唤醒
8. 通知观察者线程将被唤醒
9. 处理未处理的事件
   * 如果用户定义的定时器启动，处理定时器事件并重启RunLoop。进入步骤2
   * 如果输入源启动，传递相应的消息
   * 如果RunLoop被显示唤醒而且时间还没超时，重启RunLoop。进入步骤2
10. 通知观察者RunLoop结束 



