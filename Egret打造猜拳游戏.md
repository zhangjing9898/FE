## fingerGuessing

## 项目介绍

fingerGuessing：一款有趣的猜拳游戏，基于egret白鹭游戏引擎开发（ps：游戏中试玩榜后台数据均为随机虚假）。

## 演示视频

[![image](https://intranetproxy.alipay.com/skylark/lark/0/2019/jpeg/129546/1568709715828-3a4ccedb-8ab3-4469-ad1f-36d0779acb63.jpeg)](https://link.zhihu.com/?target=https%3A//links.jianshu.com/go%3Fto%3Dhttps%3A%2F%2Fwww.iqiyi.com%2Fv_19s7pnl5x1.html%3Fp1%3D2_22_222)

## 部分截图



![image](https://intranetproxy.alipay.com/skylark/lark/0/2019/jpeg/129546/1568709716069-3c2b4902-2e1c-4954-a257-2540565944fa.jpeg)

image.png

## 项目功能结构



![image](https://intranetproxy.alipay.com/skylark/lark/0/2019/jpeg/129546/1568709715879-59d65ac6-53a8-48ca-9087-66d1d4e5af8a.jpeg)

猜拳游戏.png

## 项目运行

```
$ egret build xxx(项目名)
$ egret startserver xxx(项目名) -a
```

## 项目搭建教程

该项目主要分为4个页面

### 1. 开始页面

创建bitmap对象，做背景图和**位图的绘制**，涉及到2个按钮（开始游戏按钮、试玩榜按钮）的**事件监听**（touch_begin、touch_end、touch_release_outside）。

```
startBtn涉及dispatchEvent，下面篇幅会有详解
```

### 2.游戏页面

- 背景和相关位图绘制

- - 出拳tween动画处理 + random出拳
  - 根据所剩时间，动态调整出拳动画频率



- timerPanel.ts 用于控制游戏倒计时

- - 倒计时栏目UI绘制
  - listen TimerEvent中的timer和timer——complete，做倒计时数字的update
  - 将default_time存入localStorage中，用于game页面使用



- 猜况按钮逻辑处理

- - 根据出拳情况，用户判断那边赢（左边赢、打平、右边赢）
  - 为了精简代码，抽象一个commonCallback，用于left、middle和right使用



### 3.结束页面

- 位图绘制

- - 抽象出commonImgConf函数，来绘制多个按钮



- 按钮事件派发、处理

- - 抽出commonCallback，分别控制3个gameEvent



### 4.试玩榜页面

- 返回按钮事件派发
- 文本绘制
- 抽出commonTxt用于控制多段文本绘制

```
private commonTxt(size: number, isCenter: boolean, isAssignment: boolean ,y: number, x?: number) {
        const txt = new egret.TextField();
        txt.width = egret.MainContext.instance.stage.stageWidth;
        this.addChild(txt);
        txt.y = y;
        if (x) txt.x = x;
        txt.textAlign = isCenter == true ? egret.HorizontalAlign.CENTER : egret.HorizontalAlign.LEFT;
        txt.size = size;
        txt.textColor = 0xffffff;
        isAssignment ? this.txt = txt : txt.text = "试玩榜";
    }
```

- 请求发送和请求解析

- - onComplete() + onGetComplete()



```
private onComplete(): void {
        const url: string = "xxxx";
        const loader: egret.URLLoader = new egret.URLLoader();
        loader.dataFormat = egret.URLLoaderDataFormat.TEXT;
        loader.addEventListener(egret.Event.COMPLETE, this.onGetComplete, this);
        const request: egret.URLRequest = new egret.URLRequest(url);
        request.method = egret.URLRequestMethod.GET;
        loader.load(request);
    }
    private onGetComplete(event: egret.Event):void {
        const loader:egret.URLLoader = <egret.URLLoader> event.target;
        let data:egret.URLVariables = loader.data;
        // 采用js解析方法
        let res = eval("("+data.toString()+")");
        ... // TODO: 其他处理
}
```

- easy-mock数据格式：

```
{
  "data": {
    "array|5-9": [{
      "score": "@integer(1,100)",
      "name": "@cname"
    }]
  }
}
```

### 3.发布游戏

当整个游戏制作完成后，我们需要将它进行发布，这里只涉及H5方面发布。

一句命令行而已的事：

> 参数说明：

```
egret publish [project_name] [--version [version]] [--runtime html5|native] [--passWorld]
$ egret publish xxx(项目名) --version 0.03(版本号)
```

**发布项目,如果是在项目文件夹下编译，可以不加项目名称**

打包成功后，会出现一个bin-release的文件夹



![image](https://intranetproxy.alipay.com/skylark/lark/0/2019/jpeg/129546/1568709715866-dca02101-6f51-4243-927f-cdb1b5b1197d.jpeg)

image.png

然后把我框出来的1903....这个文件夹里面的东西，拖到服务器上，然后对应访问，就可以啦。

### 敲黑板划重点

**如果说，我们平时在自我测试环节，起一个本地服务，然后手机在同一个网络下访问本机ip地址就可以**

这样应该会比较方便，简单介绍一下这个步骤：

### 简单的http服务

首先安装**http-server**模块。

```
$ npm i -g http-server
```

运行发布完成的包

```
$ cd bin-release/web/190311152632
$ ls
index.html  js  manifest.json  resource
$ hs -p 8088
Starting up http-server, serving ./
Available on:
 http://127.0.0.1:8088
 http://192.168.1.100:8088
Hit CTRL-C to stop the server
```

最后一级文件夹名字其实是时间戳。

这样我们便可以在pc浏览器或者手机浏览器中查看游戏了。

## 项目细究

**>=2次使用，或有大部分common的，都抽象出来使用，尽量尝试用新语法来简化代码量**

```
今日给自己灌的鸡汤：代码应该是一日写的比一日优雅才行，一个需求应思考多种写法，再找到最优解
```

### 精简繁琐for循环

在判断猜况的时候，需要使用到多重嵌套for循环，看着特别恶心，后面采用了这种方法用于简化：

```
const actions = () => {
            const answerFalse = () => { this.answer_type = false };
            const addScore = () => { this.score++; this.answer_type = true };
            return new Map([
                [{ left_type: 0, right_type: 0 }, answerFalse],
                [{ left_type: 0, right_type: 1 }, answerFalse],
                [{ left_type: 0, right_type: 2 }, addScore],
                [{ left_type: 1, right_type: 0 }, addScore],
                [{ left_type: 1, right_type: 1 }, answerFalse],
                [{ left_type: 1, right_type: 2 }, answerFalse],
                [{ left_type: 2, right_type: 0 }, answerFalse],
                [{ left_type: 2, right_type: 1 }, addScore],
                [{ left_type: 2, right_type: 2 }, answerFalse]
            ])
        }
            let action = [...actions()].filter(([key, value]) => (
                key.left_type == this.left_type && key.right_type == this.right_type
            ));
            action.forEach(([key, value]) => value.call(this));
```

### Egret核心显示类

需要重新梳理一下这些类，以便下次写的时候，有点混

类描述DisplObject显示对象基类，所有显示对象均继承自此类DisplObjectContainer显示对象容器接口，所有显示对象容器均实现此接口Bitmap位图，用来显示图片Shape用来显示矢量图，可以用里面的方法绘制矢量图形TextField文本类BitmapText位图文本类Sprite带有矢量绘制功能的显示容器Stage舞台类

这里面的startscene，使用继承sprite，而没用displaObjeContainer，在这里简单列出它们之间的继承关系：

```
egret.Sprite -> egret.DisplayObjectContainer -> egret.DisplayObject
```

**同时，提一点：shape与sprite的diff：**

- shape：显示对象，一般用于绘制图形
- sprite：显示容器，在此基础上可以添加子容器 + 显示对象，一般用于create可以装载content的游戏层或者游戏对象

[http://blog.sina.com.cn/s/blog_6b80d2ca0102vjx4.html](https://link.zhihu.com/?target=https%3A//links.jianshu.com/go%3Fto%3Dhttp%3A%2F%2Fblog.sina.com.cn%2Fs%2Fblog_6b80d2ca0102vjx4.html)

### Egret中事件处理

egret中的事件机制是一套比较标准的事件处理架构。

简单来说，数据的**提供者**只需要在意发出数据对象，以及确保数据对象是egret.event类或者子类的实例即可，这种数据对象，也就是`事件event`。

我们在游戏中涉及到按钮的一些交互效果，比如：点击**开始游戏**，进入游戏页面；点击**Rank icon**，弹出rank的弹框。

以上这些都会涉及倒egret的事件处理，我先简单罗列事件处理的一个基本流程：

- step1： 注册侦听器listener
- step2：发送事件
- step3：侦听事件
- step4：移除事件侦听器

这里我用startScene中点击**开始游戏**的事件处理，做一个简单事例：

**startscene .ts:**

```
// init fn
private init(){
  ...
  // 注册listener 
  rank_btn.addEventListener(egret.TouchEvent.TOUCH_BEGIN, this.rank_btnCallback, this);
  ...
}
// start_btnCallback fn
private start_btnCallback(evt:egret.TouchEvent):void {
  ...
  //  派发事件
  let event:GameEvent = new GameEvent(GameEvent.GAME_GO);
  this.dispatchEvent(event);
  ...
}
```

**main.ts:**

```
private createGameScene() {
  ....   
  //侦听事件
  startScene.addEventListener(GameEvent.GAME_GO, this.go, this);
  startScene.addEventListener(GameEvent.GAME_BLEED, this.startrank, this);
}
private go(){
  // 手动回收侦听器
  this.removeEventListener(GameEvent.GAME_GO, this.go, this);
  this.removeChildren();
  ...
}
```

**注意：这里存在一个问题：**

在显示对象容器里的子显示对象上增加了事件侦听器（addEventListener），如果通过父级显示对象的removeChild移除了这个容器，那么有必要在egret.Event.REMOVED_FROM_STAGE事件里手动删除所有子对象注册的事件侦听器吗（removeEventListener）？还是说引擎会自动处理？

**答案是：如果没有引用的话就会回收，但是为了保证一定回收最好手动移除，不然可能造成内存泄露**

### Egret支持ES6

在egret中默认设置是es5，所有很多es6的语法是不支持的，比如map这些...

yiwen那么如何更改配置项，让其支持es6语法呢？

### step1：

找到根目录下的tsconfig.json文件，这是默认生成的：

```
{
    "compilerOptions": {
        "target": "es5",
        "outDir": "bin-debug",
        "experimentalDecorators": true,
        "lib": [
            "es5",
            "dom",
            "es2015.promise"
        ],
        "types": []
    },
    "include": [
        "src",
        "libs"
    ]
}
```

### step2:

- 将lib下的`es5`改成`es2015`，也就是es6
- 在compilerOptions下加一行`"downlevelIteration": true,`，用于降级迭代器

更改后是这样：

```
{
    "compilerOptions": {
        "target": "es5",
        "outDir": "bin-debug",
        "experimentalDecorators": true,
        "downlevelIteration": true,
        "lib": [
            "es2015",
            "dom",
            "es2015.promise"
        ],
        "types":[]
    },
    "exclude": [
        "node_modules"
    ]
}
```

### step 3:

由于配置项更改了，需要重新build和run，再次执行：

```
> $ egret build xxx
> $ egret startserver xxx -a
之前...由于一些原因，本地的lib和远程github上的lib冲突了，导致项目跑不起来，然而各种...奇怪的方法尝试，最后找到问题，本地解决了，发现...无法把本地的lib推到远程的lib上...然和各种奇怪，反正那啥最后就强制了一把：git push -f ... 唉唉 真是粗暴
```

## 项目源码

代码中写了很多注释，基本都能看懂就不再赘述啦~

[源码地址](https://link.zhihu.com/?target=https%3A//links.jianshu.com/go%3Fto%3Dhttps%3A%2F%2Fgithub.com%2Fzhangjing9898%2FFingerGuessing)

