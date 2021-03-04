这篇简单讲讲用于传输层中用于可靠性的TCP和UDP协议

# UDP

UDP协议主要讲 端口 + 校验和

优点：**简单 快**

## 端口

端口用于区别不同的程序或者进程

## 特殊端口

![image.png](https://upload-images.jianshu.io/upload_images/3378252-03f676bb8464dd92.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 校验和

![image.png](https://upload-images.jianshu.io/upload_images/3378252-cf682fbd3c72c82b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# TCP

TCP的header比UDP 复杂很多

![image.png](https://upload-images.jianshu.io/upload_images/3378252-4d0cef5e03a5fd47.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如何连接？tcp的三次握手

![image.png](https://upload-images.jianshu.io/upload_images/3378252-6b18d0eb4aef7fa0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

进行第一次数据传输

![  ](https://upload-images.jianshu.io/upload_images/3378252-2a7a96c0e261448d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

关闭连接

![image.png](https://upload-images.jianshu.io/upload_images/3378252-da3d209da5260bff.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

说到这里，大家都知道TCP因为比较复杂繁琐，所以它的速度没有UDP快，为了解决这个问题，然后引入了一个**滑动窗口**的策略机制

## 滑动窗口 

简单来说，滑动窗口就是增加并行，让数据发送量*3，可以同时发送3份数据

## 拥塞控制

慢启动

![image.png](https://upload-images.jianshu.io/upload_images/3378252-8e0f151672caa69d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


