每一个UIView内部都默认关联着一个CALayer, UIView有frame、bounds和center三个属性，CALayer也有类似的属性，分别为frame、bounds、position、anchorPoint。frame和bounds比较好理解，bounds可以视为x坐标和y坐标都为0的frame，那position、anchorPoint是什么呢？先看看两者的原型，可知都是CGPoint点。

@property CGPoint position  
@property CGPoint anchorPoint

### anchorPoint:

从一个例子开始入手吧，想象一下，把一张A4白纸用图钉订在书桌上，如果订得不是很紧的话，白纸就可以沿顺时针或逆时针方向围绕图钉旋转，这时候图钉就起着支点的作用。我们要解释的anchorPoint就相当于白纸上的图钉，它主要的作用就是用来作为变换的支点，旋转就是一种变换，类似的还有平移、缩放。

继续扩展，很明显，白纸的旋转形态随图钉的位置不同而不同，图钉订在白纸的正中间与左上角时分别造就了两种旋转形态，这是由图钉（anchorPoint）的位置决定的。如何衡量图钉（anchorPoint）在白纸中的位置呢？在iOS中，anchorPoint点的值是用一种相对bounds的比例值来确定的，在白纸的左上角、右下角，anchorPoint分为为\(0,0\), \(1, 1\)，也就是说anchorPoint是在单元坐标空间\(同时也是左手坐标系\)中定义的。类似地，可以得出在白纸的中心点、左下角和右上角的anchorPoint为\(0.5,0.5\), \(0,1\), \(1,0\)。

然后再来看下面两张图，注意图中分iOS与MacOS，因为两者的坐标系不相同，iOS使用左手坐标系，坐标原点在左上角，MacOS使用右手坐标系，原点在左下角，我们看iOS部分即可。

![](http://wonderffee.github.io/images/anchorPointAndPosition/layer_coords_anchorpoint_position_2x.png "test")  
图1

![](http://wonderffee.github.io/images/anchorPointAndPosition/anchorpoint2.jpg "test")  
图2

像UIView有superView与subView的概念一样，CALayer也有superLayer与layer的概念，前面说到的白纸和图中的矩形可以理解为layer，书桌和图中矩形以外的坐标系可以理解成superLayer。如果各自以左上角为原点，则在图中有相对的两个坐标空间。

### position:

在图1中，anchorPoint有\(0.5,0.5\)和\(0,0\)两种情况，分别为矩形的中心点与原点。那么，这两个anchorPoint在superLayer中的实际位置分别为多少呢？简单计算一下就可以得到\(100, 100\)和\(40, 60\)，把这两个值分别与各自的position值比较，发现完全一致，该不会是巧合？

这时候可以大胆猜测一下，position是不是就是anchorPoint在superLayer中的位置呢？答案是确定的，更确切地说，position是layer中的anchorPoint点在superLayer中的位置坐标。因此可以说, position点是相对suerLayer的，anchorPoint点是相对layer的，两者是相对不同的坐标空间的一个重合点。

再来看看position的原始定义： The layer’s position in its superlayer’s coordinate space。  
中文可以理解成为position是layer相对superLayer坐标空间的位置，很显然，这里的位置是根据anchorPoint来确定的。

图2中是矩形沿不同的anchorPoint点旋转的形态，这就是类似于刚才讲的图钉订在白纸的正中间与左上角时分别造就了两种旋转形态。

### anchorPoint、position、frame

anchorPoint的默认值为\(0.5,0.5\)，也就是anchorPoint默认在layer的中心点。默认情况下，使用addSublayer函数添加layer时，如果已知layer的frame值，根据上面的结论，那么position的值便可以用下面的公式计算：

| 12 | position.x = frame.origin.x + 0.5 \* bounds.size.width；  position.y = frame.origin.y + 0.5 \* bounds.size.height；   |
| :--- | :--- |


里面的0.5是因为anchorPoint取默认值，更通用的公式应该是下面的：

| 12 | position.x = frame.origin.x + anchorPoint.x \* bounds.size.width；  position.y = frame.origin.y + anchorPoint.y \* bounds.size.height； |
| :--- | :--- |




下面再来看另外两个问题，如果单方面修改layer的position位置，会对anchorPoint有什么影响呢？修改anchorPoint又如何影响position呢？   
根据代码测试，两者互不影响，受影响的只会是frame.origin，也就是layer坐标原点相对superLayer会有所改变。换句话说，frame.origin由position和anchorPoint共同决定，上面的公式可以变换成下面这样的：

| 12 | frame.origin.x = position.x - anchorPoint.x \* bounds.size.width；  frame.origin.y = position.y - anchorPoint.y \* bounds.size.height； |
| :--- | :--- |




这就解释了为什么修改anchorPoint会移动layer，因为position不受影响，只能是frame.origin做相应的改变，因而会移动layer。



### 总结：

1、position是layer中的anchorPoint在superLayer中的位置坐标。  
2、互不影响原则：单独修改position与anchorPoint中任何一个属性都不影响另一个属性。  
3、frame、position与anchorPoint有以下关系：

| 12 | frame.origin.x = position.x - anchorPoint.x \* bounds.size.width；  frame.origin.y = position.y - anchorPoint.y \* bounds.size.height； |
| :--- | :--- |




第2条的互不影响原则还可以这样理解：position与anchorPoint是处于不同坐标空间中的重合点，修改重合点在一个坐标空间的位置不影响该重合点在另一个坐标空间中的位置。



