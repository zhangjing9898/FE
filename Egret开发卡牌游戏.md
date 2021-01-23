## CardGame

## 项目介绍

cardGame是一个基于egret(白鹭游戏引擎)开发的简单入门(练手)项目，参考白鹭官网卡牌游戏demo，通过实现这个demo，可以快速入门eui操作，并试着自己开发一些交互简单的游戏。



演示：http://developer.egret.com/cn/article/index/id/833



## 项目演示

![image](https://intranetproxy.alipay.com/skylark/lark/0/2019/jpeg/129546/1568709998933-420580cb-8b4c-4078-875a-c0deaed222fc.jpeg)

## 项目功能结构



![image](https://intranetproxy.alipay.com/skylark/lark/0/2019/jpeg/129546/1568709998951-75bd4206-2b00-48e3-afe8-b011673960fb.jpeg)

功能结构图.png

## 项目运行

```
$ egret build xxx(项目名)
$ egret startserver xxx(项目名) -a
```

## 项目剖析

cardGame简单来说就是用eui搭建好5个场景页面，然后在用ts去控制之间的交互。

## eui组成页面

**拖拖拖点点点**即可



![image](https://intranetproxy.alipay.com/skylark/lark/0/2019/jpeg/129546/1568709998919-ab8cc5e4-6a5b-4616-a999-10a55b0d28a9.jpeg)

image.png

## ts控制交互

交互很简单，就是来回切换，和一些按钮的放大缩小。

## 注意

下面介绍几个有意思的点。

## 1.按钮的大小缩放效果（不使用tween）

> 步骤：

```
1.找到对应button，在wing中点击上方的源码
2.找到对应源码，加上红框中的代码
```



![image](https://intranetproxy.alipay.com/skylark/lark/0/2019/jpeg/129546/1568709998972-137e9769-d9ca-4ad9-af5a-4316adf77b16.jpeg)



## 接下来说这几行的意思：

width和height为90%，表示其最初大小只有原本的90%；width.down和height.down为100%，表示点击后按钮的大小会变为100%，也就是增大10%；horizontalCenter和verticalCenter为0，则表示其缩放点为**正中心**

## 2.scroller

eui本身提供Scroller组件，所以我们不需要自己开发，直接拖过来就可以使用了，但是在使用的过程中，需要注意**2个属性**，点击所有属性，然后找到scrollPolicyH(横向)和scrollPolicyV(纵向)，使用off或者on来控制。



![image](https://intranetproxy.alipay.com/skylark/lark/0/2019/jpeg/129546/1568709998958-4db9742d-cd01-40f1-bd2e-8bfd1ac04d62.jpeg)



## 项目源码

代码中写了很多注释，基本都能看懂就不再赘述啦~

[源码地址](https://link.zhihu.com/?target=https%3A//github.com/zhangjing9898/cardGame/tree/master/cardGame)
