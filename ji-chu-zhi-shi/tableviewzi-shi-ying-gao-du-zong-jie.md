### 1.固定高度：

self.tableView.rowHeight =88;

- \(CGFloat\)tableView:\(UITableView\*\)tableView heightForRowAtIndexPath:\(NSIndexPath\*\)indexPath {

    // return xxx

}

### 2.自适应高度

##### Frame布局时代：

用数据内容反算高度：

| CGFloatheight = textHeightWithFont\(\) + imageHeight + topMargin + bottomMargin + ...; |
| :--- |


供 UITableViewDelegate 调用时很可能是个 cell 的类方法：

| @interfaceBubbleCell:UITableViewCell+ \(CGFloat\)heightWithEntity:\(id\)entity;@end |
| :--- |


各种魔法 margin 加上耦合了屏幕宽度。

#####  iOS7之后：

self.tableView.estimatedRowHeight = 88;

// or

- \(CGFloat\)tableView:\(UITableView \*\)tableView estimatedHeightForRowAtIndexPath:\(NSIndexPath\*\)indexPath {

   // return xxx

}

##### estimatedRowHeight缺点：

1. 设置估算高度后，contentSize.height 根据“cell估算值 x cell个数”计算，这就导致滚动条的大小处于不稳定的状态，contentSize 会随着滚动从估算高度慢慢替换成真实高度，肉眼可见滚动条突然变化甚至“跳跃”。
2. 若是有设计不好的下拉刷新或上拉加载控件，或是 KVO 了 contentSize 或 contentOffset 属性，有可能使表格滑动时跳动。
3. 估算高度设计初衷是好的，让加载速度更快，那凭啥要去侵害滑动的流畅性呢，用户可能对进入页面时多零点几秒加载时间感觉不大，但是滑动时实时计算高度带来的卡顿是明显能体验到的，个人觉得还不如一开始都算好了呢（iOS8更过分，即使都算好了也会边划边计算）

##### iOS8之后：

# self-sizing cell

旨在让 cell 自己负责自己的高度计算，使用

frame layout和auto layout都可以享受到：self.tableView.estimatedRowHeight =213;

self.tableView.rowHeight = UITableViewAutomaticDimension;

autoLayout要求cell内部的子控件使用Msaonry或者AutoLayout设置好相互之间的约束

PS：iOS8 系统中 rowHeight 的默认值已经设置成了 UITableViewAutomaticDimension，所以第二行代码可以省略。

注：

- \(CGFloat\)tableView:\(UITableView \*\)tableView heightForRowAtIndexPath:\(NSIndexPath \*\)indexPath {}中。

IOS8之前：上面方法返回

CGFloat height = \[cell systemLayoutSizeFittingSize: UILayoutFittingCompressedSize\].height;

iOS8之后：return UITableViewAutomaticDimension;

Framelayout 要求重载-sizeThatFits:方法。

UITableView的cell高度计算优化

\#import &lt;UITableView+FDTemplateLayoutCell.h&gt;

- \(CGFloat\)tableView:\(UITableView\*\)tableView heightForRowAtIndexPath:\(NSIndexPath\*\)indexPath {

return \[tableView fd\_heightForCellWithIdentifier:@"identifer" cacheByIndexPath:indexPath configuration:^\(id

cell\) {

      //配置cell的数据源，和"cellForRow"干的事一致，比如：

      cell.entity =self.feedEntities\[indexPath.row\];

  }\];

}

  


上面代理方法中的cell只是为了参加高度计算，不会真正显示到屏幕上。

1。此类cell跟显示到屏幕的cell的生成原理相同，具备唯一标识，   

-dequeueCellForReuseIdentifier:方法lazy创建并保存

2.根据autolayout约束自动计算高度；

-systemLayoutSizeFittingSize:

3.根据index path的一套高度缓存机制，每个cell真正的高度计算只会发生一次；

4.自动的缓存失效机制， -reloadData或者 -deleteRowsAtIndexPath 等任何触发tableView刷新时，已有的高度缓存将以最小的代价执行失效，如删除一个indexPath为【0：5】的cell时，0~4的cell的高度缓存不收受影响，5~之后的所有缓存都向前移动一个位置

5.预缓存机制 在tableView没有滑动的空闲时刻执行，计算和缓存那些没有显示到屏幕中的cell  原理关键字：RunLoop,NSDefaultRunLoopMode,注册观察者，RunLoop即将进入休眠状态，分解成多个RunLoop Source任务（n个cell计算任务分解到n个RunLoop中迭代执行）。

CFRunLoopRef runLoop = CFRunLoopGetCurrent\(\);

CFStringRef runLoopMode = kCFRunLoopDefaultMode;

CFRunLoopObserverRef observer = CFRunLoopObserverCreateWithHandler\(kCFAllocatorDefault, kCFRunLoopBeforeWaiting,true,0, ^\(

CFRunLoopObserverRef observer,

CFRunLoopActivity\_\) {

// TODO here

}\);

CFRunLoopAddObserver \(runLoop, observer, runLoopMode\);

