# 网络分层模型和TCP/IP协议族 ——IP协议

这一篇文章主要记录IP协议相关内容。

## IP协议是干什么的？

寻址 + 路由选择

### 寻址中的址是什么？

首先，它不是mac地址，而是ip地址，那么ip地址又是个什么东西呢？

![image.png](https://upload-images.jianshu.io/upload_images/3378252-ad0d0205a7ce626d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我在这里，举一个简单的例子：
![](https://upload-images.jianshu.io/upload_images/3378252-50fc5fff63137f13.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
因为二进制数写起来和读起来都比较费劲，所以我们一般都换算为十进制。

其对应的子网掩码是这样的

11111111 11111111 11111111 00000000

转换为十进制，也就是：
`255 255 255 0`

接着我们来依次对应下，这张图可以清楚的表示：

![image.png](https://upload-images.jianshu.io/upload_images/3378252-e4fff26d00473eb2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**这里还提到了一个有意义的简写**

如果子网掩码的前24位都是1的话，那么我们可以这样简写：**/24** 

接下来补充一下，常见的ip地址列表。

## ip地址列表

![image.png](https://upload-images.jianshu.io/upload_images/3378252-ffb2f02d50ed5d2d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/3378252-d14090b3cb4e3e86.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这里的127.0.0.1 环回也就是 本机地址。

## 寻址+路由

ip协议中的路由分配以及寻址是怎么个过程呢？接下来慢慢分析。

### 同一个内网传递数据 

给出一个case：

> 同一个网段下，computerA想给computerB发送数据。

步骤：

-  首先，对应的是应用层
-   然后，把它扔进了TCP协议里，给它加上了端口，比如这里的80
- 再然后，扔进IP协议，包装上了ip地址
- 接着，扔进以太网，包装mac地址
`注意：在上一篇以太网中已经提到，如何通过ip地址找到**发送目标的**的mac地址，这里不再赘述`


![image.png](https://upload-images.jianshu.io/upload_images/3378252-b9db63d54aa6b550.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/3378252-3e37a5b5b4e11142.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/3378252-764067ffd69f59b5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这样打包完成后，我们就可以顺畅的把数据发出去啦~

### 网络间的数据传送 

说完了内网之间的数据传送，接下来我们说说非内网间，它是怎么进行数据传送的。

![image.png](https://upload-images.jianshu.io/upload_images/3378252-91d9ee2d23e49bee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> computerA（192.168.0.1）准备给不同网段的computerB（172.16.0.1）发送数据

步骤：
- 先发给网关，具体同上
- 然后发给路由器A
- 路由器上都有个路由表
![image.png](https://upload-images.jianshu.io/upload_images/3378252-4bbf4cfa761e2886.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 查看是否直连
- 同上进行发送

## IP分片和重组

![image.png](https://upload-images.jianshu.io/upload_images/3378252-65c92d5292b0619b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**路径发现MTU**
我觉得这个机制还是挺有意思，简单来说，我们在发送数据帧的时候，如果它的大小超过网络MTU，那么它会停止发送，并自动调整路径，再重新发送。这个还是挺智能的。

## IP相关协议

![image.png](https://upload-images.jianshu.io/upload_images/3378252-bfa62392c194c836.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

其实常用的也就是dns，其他的做个了解就可以了。




