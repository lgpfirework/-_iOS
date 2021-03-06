[http://www.jianshu.com/p/52d86558ca57](http://www.jianshu.com/p/52d86558ca57)

#### HTTP/1.1相较于 HTTP/1.0 协议的区别主要体现在：

1 缓存处理

2 带宽优化及网络连接的使用

3 错误通知的管理

4 消息在网络中的发送

5 互联网地址的维护

6 安全性及完整性

#### http建立长连接

Http1.0不支持长连接

http1.1默认长连接，

Connection:keep-alive

[http://blog.jobbole.com/93960/](http://blog.jobbole.com/93960/)

#### Https安全性分析

中间人攻击场景涉及三个角色，客户端，服务器，以及 CA（证书签发机构）。CA 主要用来解决 Client 和 Server 之间的信任问题，相当于一个背书的角色。CA 通过签发证书的方式，来确认 Client 和 Server 的身份，具体到 iOS 客户端，CA 一般向 Server 签发证书，告知 Client，持有某个证书的 Server，其身份是可以被信任的。那谁来确认 CA 所说的话是可以被信任的呢？操作系统会内置一些知名 CA 的公钥，这些知名 CA 在签发证书的时候会通过审核确认，确保 Server 的身份和其所宣称的一致。

对于 iOS 开发者来说，防中间人攻击可以从两方面着手。

一：通讯内容本身加密，无论是走 http 还是 https，request 和 response 的内容本身都要先做一次加密；

二：大家所熟知的 ssl pinning。在客户端进行代码层面的证书校验，校验方式也有两种，一是证书本身校验，二是公钥校验。这两种方式对应到 AFNetworking 中的代码如下：

enum {

     AFSSLPinningModeNone,

     AFSSLPinningModePublicKey,

     AFSSLPinningModeCertificate,

}

证书校验是文件级别的校验，客户端只信任若干个证书文件，这些证书文件是和客户端一起打包发布的，这种处理方式要面对的一个问题证书过期问题，为了避免证书过期导致的校验失败，客户端和服务器之间需要额外存在一个证书更新机制，其实做起来也比较简单，只需要服务器下发一个特定的错误码，触发一个客户端的新证书下载流程即可。

公钥校验模式可以免去上述的麻烦，公钥模式只校验证书中所包含的公钥是否匹配，即使证书过期了，只要服务器更新证书，保证公钥不变，依然能完成校验过程，但这个大前提是，服务器的公钥私钥对不能更换。



