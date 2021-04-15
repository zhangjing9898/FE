# **📀 背景介绍**



为了弥补 "电影演出" 营销游戏这块的空白，同时实现快速搭建，我和UED同学尝试联合落地基于Egret的营销游戏搭建方案，恰好借"哥斯拉大战金刚"这部片子，做一个试水尝试，该游戏基于egret引擎开发，用于提升影片营销的用户互动及留存。



### **玩法简介**

游戏模式：角色选择PK对战

选择怪兽or金刚，猛戳技能按钮可迅速击败对方

游戏胜利后可获得抽奖机会 使用领券组件进行承接



### **数据**

目前对数据进行了简单复盘(本文只展示可披露数据)：游戏成功完成率达到 **61%** 以上，通过游戏宣发的券，其核销率明显高于其他方式或平台



### **开发投入**

前端和视觉的投入控制在3-4人日，测试1-2人日



# **🚀 项目演示**

### **游戏链路**

![img](https://intranetproxy.alipay.com/skylark/lark/0/2021/png/129546/1618382106498-4194da99-cda1-4433-a51d-0fb862cf2dff.png)



### **体验二维码**

![img](https://gw.alicdn.com/imgextra/i4/O1CN01dhX56m1UtOXei6xNP_!!6000000002575-2-tps-200-200.png)

PC端的同学 可以点[这试试](https://g.alicdn.com/alipay-movie-client/movie-h5-cdn/1.0.94/egret/PKGame/index.html)



### **市场传播图**

![img](https://gw.alicdn.com/imgextra/i3/O1CN014wkTeS1s3ECp5JjXC_!!6000000005710-2-tps-750-2673.png)



# **🏄 如何4天完成搭建**



接下来，进行主要流程，如何快速搭建...我们的小游戏



### **Day 1：确定流程**



和UED还有运营同学，一起商讨完成整个游戏的流程设计。分工确认

- 前端：针对交互逻辑完成对应的流程设计 
- 视觉：构建画面



### **Day 2：EUI build页面**



游戏最直观呈现的就是页面，egret给我们提供了一个wing的编辑器(和vscode很像)，wing方便我们对图片和组件进行拖拽和build，具体就不再赘述这里的教程，参考官网即可快速入门 —— [EUI入门手册](https://link.zhihu.com/?target=https%3A//developer.egret.com/cn/article/index/id/518)



![img](https://gw.alicdn.com/imgextra/i3/O1CN01hY3wJJ1GztXh2cZ90_!!6000000000694-2-tps-1492-367.png)



该游戏涉及到主要涉及到4个场景：开始startScene、角色选择roleSelectScene、2个对战PKScene；场景涉及到的静态部分都是由UED搭建(也可以前端同学自己搭建，看团队资源情况而定)，剩余部分由前端同学开发。



### **Day 3：交互逻辑编写**



![img](https://intranetproxy.alipay.com/skylark/lark/0/2021/png/129546/1618385162467-15c4c056-4391-4746-a490-817fc28b0206.png)



**游戏对战逻辑：**

- 进入游戏，进入游戏倒计时，我方HP--，对应血槽值width--
- 监听**攻击按钮**，点击后，对方的HP--，对应血槽值width--
- 游戏成功与否判定？倒计时结束前 && 对方HP<=0 && 我方HP>=0，代表游戏成功；倒计时结束前 && 对方HP>=0 && 我方HP<=0，代表游戏失败；倒计时结束，也代表游戏失败
- 根据游戏结果，弹出不同弹框；如果本轮失败，诱导用户再玩一次；如果成功，引导用户前往抽奖专题页



### **Day 4：DragonBones动作特效**



一款游戏，最重要的是特效引人，场景炫酷。为了提高整体用户体感，我们采用DragonBones来做动作特效。



比如：游戏中涉及到的**打斗场景、****闪光****的**成功中奖弹框场景。



Q：这些特效如何实现呢？

A：UED同学会导出3份文件给你：xx_ske.json、xx_tex.json、xx_tex.png；我们把这三份文件放置 assets 文件夹下，并在default.res.json中做好对应配置，然后在代码中进行引入和播放即可



如何进行特效控制？

- build对应的cartoon
- 找到对应的动作名，使用xx.animation.play('动作名')来进行播放
- 播放的同时，也可以控制播放速度xx.animation.timeScale = x倍速(number)
- 停止播放 xx.animation.stop(‘动作名’) 如果这里不填，就停止该cartoon的所有动作



**Util.ts 公用方法类**

```
class Util {
  static buildCartoon = (_this, name = 'monkey', role = 'xx') => {
    let dragonbonesData = RES.getRes(`${name}_ske_json`);
    let textureData = RES.getRes(`${name}_tex_json`);
    let texture = RES.getRes(`${name}_tex_png`);

    let egretFactory: dragonBones.EgretFactory =
      dragonBones.EgretFactory.factory;
    egretFactory.parseDragonBonesData(dragonbonesData);
    egretFactory.parseTextureAtlasData(textureData, texture);

    let armatureDisplay: dragonBones.EgretArmatureDisplay = egretFactory.buildArmatureDisplay(
      role
    );

    _this.addChild(armatureDisplay);

    return armatureDisplay;
  };
}
```



**Game.ts 截取部分**

```
public constructor() {
    super();
    // build cartoon
    this.user = Util.buildCartoon(this, 'little-monster', 'armatureNameB');
    Util.setSize(this.user, 100, 290, 0.5);
 };

 private userStop = () => {
    this.user.animation.stop();
 };

private commonPlay = (el, name, speed = 1) => {
    el.animation.stop();
    el.animation.play(name);
    el.animation.timeScale = speed;
 };
```

# **🎬 最后**

最后，欢迎互相交流~ 

速度与激情9相关小游戏，也敬请期待~~
