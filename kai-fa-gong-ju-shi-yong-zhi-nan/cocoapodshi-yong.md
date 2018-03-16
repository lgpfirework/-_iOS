Pod install --no-repo-update

Pod update --no-repo-update

--no-repo-update :表示不升级CocoaPods的spec仓库

##### 升级Cocoapods：

 1.sudo gem install cocoapods —pre或者

    sudo gem install -n /usr/local    /bin cocoapods

2.pod setup

##### 添加/删除库：

:pod install  （不会影响其他的库）

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

1.gem sources -- remove https://[rubygems.org/](http://rubygems.org/)

2.gem sources -a http://ruby.[taobao.org/](http://taobao.org/)

  （或者https://gems.ruby-china.org/）

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

注意，pod outdated和pod update都会更新spec仓库，但是pod install不会，所以对于经常使用的pod库，建议经常pod outdated关注更新情况。

##### 官网提到的：为什么直接在Podfile文件中指定版本的方法不够用？

原因是你所依赖的库可能还依赖于其他的库。

如果指定pod A的版本（如在Podfile中指定pod 'A', '1.0.0'），但是pod A依赖于pod A2（

通过在A.podspec中的dependency 'A2', '~&gt;3.0'声明）。在这种情况下，Podfile的确会强制所有用户使用pod A的1.0.0版本，但是，用户1可能会使用A2的3.4版本，而在这之后A2有新版发布，若无Podfile.lock的帮助，用户2无论使用pod install还是pod update都不可避免的会使用A2的3.5版本。

一般情况下pod A对pod A2依赖关系你是不可见的，或者并不由你维护的。所以，直接在Podfile文件中指定版本的方法并不能保证所有用户都使用相同版本的pod库。

除非你的工程所依赖的所有pod库在对其他库进行依赖时也都采用指定版本的方法，而这只有在所有pod库都由你来维护时才能够得到保证。



