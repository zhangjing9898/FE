1. 1. ## 项目介绍

      iceBucketChallenge：类似打地鼠类的冰桶挑战游戏(ps:素材均来自网络)，基于egret白鹭游戏引擎开发，主要分为2个大页面，用EUI build相应的页面，用tween来控制动画，ts控制交互。

      ## 项目演示

      

      ![image](https://intranetproxy.alipay.com/skylark/lark/0/2019/jpeg/129546/1568709815167-2bba1c30-bd03-44b7-8cb4-957d6c087098.jpeg)

      iceBucketChallenge.gif

      ## 项目功能结构

      

      ![image](https://intranetproxy.alipay.com/skylark/lark/0/2019/jpeg/129546/1568709815126-0b530dad-818f-4aaf-be82-6db858ab8244.jpeg)

      iceBucketChallenge.png

      ## 项目运行

      ```
      $ egret build xxx(项目名)
      $ egret startserver xxx(项目名) -a
      ```

      ## 项目搭建教程

      该游戏比较简单，就随便记录一下大概步骤

      ### 1.EUI build页面

      游戏最直观呈现的就是页面，egret给我们提供了一个wing的编辑器(个人感觉和vscode超级像)，wing方便我们对图片和组件进行拖拽和build，具体就不再赘述这里的教程，参考官网即可快速入门 —— [EUI入门手册](https://link.zhihu.com/?target=https%3A//links.jianshu.com/go%3Fto%3Dhttps%3A%2F%2Fdeveloper.egret.com%2Fcn%2Farticle%2Findex%2Fid%2F518)

      记住要点：**拖拖拖点点点**即可

      示例图：

      

      ![image](https://intranetproxy.alipay.com/skylark/lark/0/2019/jpeg/129546/1568709815227-53b148c6-fa44-4434-98e8-da7c4d03aad6.jpeg)

      image.png

      ### 2.tween制作动画

      这里主要使用tween缓动来控制人头上下出没的动画（简单来说：控制‘地鼠’的出没）

      

      ![image](https://intranetproxy.alipay.com/skylark/lark/0/2019/jpeg/129546/1568709815253-4ba11dbd-d38e-4d19-8880-e9744c0bd51d.jpeg)

      IMG_20190410_171639.jpg

      ### mask遮罩

      在做人头出没的时候，用到遮罩

      ```
      img.mask = g.getChildAt(0);
      ```

      为什么要这么使用？下面慢慢说。

      ### 什么是遮罩？

      遮罩是游戏中非常常见的一个视觉处理手段。简单来说：所谓的遮罩就是指定一个**显示对象**哪些部分可以显示，哪些部分不可以显示。

      ### 推荐一个直观的实践办法

      光看文字，应该会觉得一头雾水，可以尝试在代码中这么玩一下：`改变rect的高度和宽度，观察这时候人头出没时的效果`，这样可以最直观的了解到遮罩的意思。

      ### 3.发布游戏

      当整个游戏制作完成后，我们需要将它进行发布，这里只涉及H5方面发布。

      一句命令行而已的事：

      ```
      $ egret publish xxx(项目名)
      ```

      打包成功后，会出现一个bin-release的文件夹

      

      ![image](https://intranetproxy.alipay.com/skylark/lark/0/2019/jpeg/129546/1568709815251-e0f37be0-6a59-4cef-8648-0791ea0c46a2.jpeg)

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

      ### 踩过的小坑

      在开发中，我因为1个小错误，导致进度卡壳了一会儿，这里记录一下，以防下次重犯：

      - eui中的布局

      ### egret.startTick思考

      绘制人头出没的时候，考虑到用帧事件处理，这里使用了egret.startTick和egret.stopTick。

      `startTick`（停止对应的则是stopTick）全局函数将以60帧的速度去 -> 回调函数。

      > 需要注意的是如果要使用 startTick 全局函数需要 Egret 2.5 以上的版本。Ticker 类将被废弃。

      在game.ts中的init fn中，我是这么使用的：

      ```
      egret.startTick(this.update, this);
      ```

      startTick函数涉及到2个入参：

      1. 回调函数，要求有返回值，如果返回**true**，将在回调函数执行完成之后立刻重绘，如果返回**false**，则不会重绘
      2. this对象，通常传入this即可

      ### module模块

      es6中提到了一个module的概念，简单来说：模块就是一个独立的文件，该文件内部的所有变量，外部无法获取。如果你希望外部能够读取模块内部的某个变量，就必须使用export关键字输出该变量。

      这次在ts中采用了一个新的写法：

      ```
      module GameUtil {
          export class Constant {
              public static score = 0;
          }
          export enum peopleEnemy {
              "person-fs_png" = 0,
              "person-lj_png",
              "person-ldh_png"
          }
          export enum peopleEnemyWet {
              "person-fs-wet_png" = 0,
              "person-lj-wet_png",
              "person-ldh-wet_png"
          }
      }
      ```

      ### 泛型扩展

      之前在一篇博客《typescript常用点》中提了一部分泛型，通过这个项目，抽象了一句更直白的话：

      ```
      泛型：参数化的类型，一般用于限制集合的内容
      ```

      ### toString拓展

      toString() 函数返回一个表示该对象的字符串

      - 数值

      ```
      console.log((4).toString())  // '4'
      ```

      - 字符串

      ```
      console.log('nwd'.toString())  // 'nwd'
      ```

      - 布尔值

      ```
      console.log(true.toString()) // 'true'
      ```

      - 对象 非数组

      ```
      console.log({}.toString())  // [object Object]
      ```

      - 对象 数组

      ```
      console.log([4,6].toString()) // 4,6
      ```

      - null和undefined

      ```
      console.log(null.toString())  // Uncaught TypeError: Cannot read property 'toString' of null
      console.log(undefined.toString())  // Uncaught TypeError: Cannot read property 'toString' of undefined
      ```

      ## 项目源码

      代码中写了很多注释，基本都能看懂就不再赘述啦~

      [源码地址](https://link.zhihu.com/?target=https%3A//links.jianshu.com/go%3Fto%3Dhttps%3A%2F%2Fgithub.com%2Fzhangjing9898%2FiceBucketChallenge)

