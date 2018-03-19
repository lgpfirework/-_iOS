Pod install --no-repo-update

Pod update --no-repo-update

--no-repo-update :表示不升级CocoaPods的spec仓库

##### 升级Cocoapods：

1.sudo gem install cocoapods —pre或者

```
sudo gem install -n /usr/local    /bin cocoapods
```

2.pod setup

##### 添加/删除库：

:pod install  （不会影响其他的库）

##### 更新库的版本：

：pod update \[库名\] （无指定库名默认更新所有的库）

##### 升级CocoaPods的spec仓库：

： Pod repo update

##### 查看本地的spec仓库：

：pod repo

##### 查看当前ruby源：

：gem source -l

##### 查看哪些库有可更新版本：

：pod outdated

##### gem换源：

1.gem sources -- remove [https://\[rubygems.org/\]\(http://rubygems.org/](https://[rubygems.org/]%28http://rubygems.org/)\)

2.gem sources -a [http://ruby.\[taobao.org/\]\(http://taobao.org/](http://ruby.[taobao.org/]%28http://taobao.org/)\)

（或者[https://gems.ruby-china.org/）](https://gems.ruby-china.org/）)

3.gem sources -l

##### Pod repo换源：

1.pod repo remove master

2.pod repo add master [https://gitcafe](https://gitcafe).com/akuandev/Specs.git

3.pod repo update

\(Podfile上面的source '[https://github.com/CocoaPods/Specs.git](https://github.com/CocoaPods/Specs.git)' 也要替换为上面的repo源\)

根据这两个命令的功能差异，以及CocoaPods官网的建议，我总结它们的正确用法是：

1、第一次获取pod库时，应使用pod install。

2、需要更新依赖库时，先使用pod outdated查看有哪些库有更新，再使用pod update PODNAME有目的的更新指定库。

3、提交代码时，请注意一定同时提交Podfile.lock文件，以便其他人能同步到与你相同的pod库版本。

4、同步其他团队成员的修改时，请使用pod install。

注意，pod outdated和pod update都会更新spec仓库，但是pod install不会，所以对于经常使用的pod库，建议经常pod outdated关注更新情况。

##### 官网提到的：为什么直接在Podfile文件中指定版本的方法不够用？

原因是你所依赖的库可能还依赖于其他的库。

如果指定pod A的版本（如在Podfile中指定pod 'A', '1.0.0'），但是pod A依赖于pod A2（

通过在A.podspec中的dependency 'A2', '~&gt;3.0'声明）。在这种情况下，Podfile的确会强制所有用户使用pod A的1.0.0版本，但是，用户1可能会使用A2的3.4版本，而在这之后A2有新版发布，若无Podfile.lock的帮助，用户2无论使用pod install还是pod update都不可避免的会使用A2的3.5版本。

一般情况下pod A对pod A2依赖关系你是不可见的，或者并不由你维护的。所以，直接在Podfile文件中指定版本的方法并不能保证所有用户都使用相同版本的pod库。

除非你的工程所依赖的所有pod库在对其他库进行依赖时也都采用指定版本的方法，而这只有在所有pod库都由你来维护时才能够得到保证。

#### 通过CocoaPod集成第三方:

创建 podfile 文件.`pod init`

* `pod install`与`pod update`区别

  * `pod install`
    , 会按照 Podfile.lock 文件安装。
  * `pod update`
    , 会按照 Podfile 文件安装, 并且生成新的 podfile.lock 文件。

* cocoaPods 的工作流程。

![](https://note.youdao.com/yws/api/personal/file/WEBd50919a5f5ff88057b236f10939dd6fa?method=download&shareKey=aed0112a817ec583590d3a0bfe5ffb9c "image")

`.spec`文件是框架的描述信息文件，包含了框架的名称、版本、代码地址。

cocoaPods 的远程索引库\([https://github.com/CocoaPods/Specs](https://github.com/CocoaPods/Specs)\)中存的就是这些`.spec`文件.

`pod setup`

将远程索引库下载到本地，生成本地索引库\(/Users/apple/.cocoapods/repos/master/Specs\)，然后在生成

检索文件\( /Users/apple/Library/Caches/CocoaPods/search\_index.json\)。

`pod search xxx`就是在这个文件中去查找到对应的`.spec`。

`pod install`按照找到的`.spec`中的代码地址去下载代码。



# 创建自己的代码仓库 {#创建自己的代码仓库}

将代码放到远程仓库。

1. 创建`.spec`文件

   * `pod spec create xxx`, xxx 是工程名

   * 修改 spec 文件

     * s.description 的内容一定要长于 s.summary
     * s.homepage 是代码仓库的首页
     * s.source = { :git =&gt;"[https://github.com/GR-harry/GRUtils.git](https://github.com/GR-harry/GRUtils.git)", :tag =&gt;"\#{s.version}" }， 前者是代码仓库的地址，后者是 tag
     * s.source\_files = "GRUtils/Classes/\*_/_.{h,m}"，用于指明安装哪个目录下的文件。
     * s.platform = :ios, '9.0', 一定要指定平台。
     * s.frameworks = 'UIKit', 指定引用的框架，如果引用的话一定要写。

   * 注册 truck：`pod trunk register xxx@126.com 'xxx' --verbose`, 执行命令得到下面一段提示后，去邮箱验证。

              ![](/assets/import7.png)

2. 将这个`.spec`文件上传到 cocoaPods 的远程索引库中。

   * `pod spec lint`分析spec文件是否合法.
   * `pod trunk push xxx.podspec`执行这个命令将 spec 文件上传到远程库中。
   * 最后用`pod search xxx`,验证是否上传成功。

### Pod常用指令：

### [常用指令的链接](http://note.youdao.com/)

* 删除缓存文件.`pod cache clean xxx --all`
  , xxx是podname.

```
eg : pod cache clean GRLib --all
```

* 验证spec文件.
  `pod spec lint name.podspec`
  and
  `pod lib lint name.podspec`
  , pod spec相对于pod lib会更为精确，pod lib相当于只验证一个本地仓库，pod spec会同时验证本地仓库和远程仓库。

注意：有时候删除了tag，修改了代码然后重新添加了跟之前一样的tag的时候，如果pod spec lint可能会报错： 因为它验证的时候会重远程下载下来新的代码，然后进行验证，但是如果~/Library/Caches/CocoaPods/里有对应tag的源代码缓存的话，是不会再重新下载覆盖这个缓存的，所以在验证的时候还是验证的老代码，导致可能的报错。 所以一般最好是不要删除tag，而要更新tag。如果要删除tag的话需要去删除~/Library/Caches/CocoaPods/里的缓存。  




