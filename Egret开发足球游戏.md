## soccerGame

## 项目介绍

soccerGame：一个基于egret(白鹭游戏引擎)开发的简单简单简单游戏(wu liao)项目，接着上面实现的卡牌游戏，趁热打铁，再次熟练eui(os: 拖拽操作)操作，同时使用tween来实现soccer的动画。

## 项目演示



![image](https://intranetproxy.alipay.com/skylark/lark/0/2019/jpeg/129546/1568709965048-baa6c36c-0633-4043-ad4e-2b7c64f810f4.jpeg)

## 项目功能结构



![image](https://intranetproxy.alipay.com/skylark/lark/0/2019/jpeg/129546/1568709964990-bab7ee5f-c6d7-4ee1-a7b7-5a46b59bad44.jpeg)

soccerGame.png

## 项目运行

```
$ egret build xxx(项目名)
$ egret startserver xxx(项目名) -a
```

## 项目剖析

简单来说就是用eui搭建好主要的场景页面，然后在用ts去控制之间的交互。

## eui组成页面

**拖拖拖点点点**即可



![image](https://intranetproxy.alipay.com/skylark/lark/0/2019/jpeg/129546/1568709964987-6b59f6e2-6f14-4192-8332-c5c4619407ef.jpeg)

image.png

## ts控制交互

主要通过按钮状态控制游戏进度，和足球的随机进球，这里的随机进球很简单，通过**Math.random**，如果 > 0.5,则上半边win，否则下半边win

## 注意

下面介绍几个有意思的点。

## 1.tween

这里使用了tween来做soccer上下滚动的动画，参考对应[TweenApi](https://link.zhihu.com/?target=https%3A//developer.egret.com/cn/article/index/id/576)。

## 2.按钮状态

在通过按钮控制游戏进度的时候，我们用到了这行：

```
this.btn_start.currentState  =  "xxx(可选：up pause resume和reset)";
```



![image](https://intranetproxy.alipay.com/skylark/lark/0/2019/jpeg/129546/1568709964979-9deee80b-e598-4848-9abb-ce4044ac9b96.jpeg)

image.png

我们在按钮的源码中通过设置`source.xxx`来控制按钮不同状态下使用哪张图片。

## 3.按钮的大小缩放效果（不使用tween）

> 步骤：

```
1.找到对应button，在wing中点击上方的源码
2.找到对应源码，加上红框中的代码
```



![image](https://intranetproxy.alipay.com/skylark/lark/0/2019/jpeg/129546/1568709964994-e61f531f-727a-4687-ac55-a34ab15d8975.jpeg)

image.png



## 接下来说这几行的意思：

width和height为90%，表示其最初大小只有原本的90%；width.down和height.down为100%，表示点击后按钮的大小会变为100%，也就是增大10%；horizontalCenter和verticalCenter为0，则表示其缩放点为**正中心**

## 项目源码

代码中写了很多注释，基本都能看懂就不再赘述啦~

[源码地址](
