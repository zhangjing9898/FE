# 杂谈



## 诞生



## 先从Hybrid技术的**诞生**说起吧。



随着移动端的兴起，app的需求量继续增加，团队需要对开发效率提高，如果用传统**原生**的ios或者andriod进行开发的化，成本会有一点点高。这时候，H5的`低成本`、`高效率`、`跨平台`的特性就被利用起来了，Hybrid app —— 一个新的开发模式。



它是一种**混合开发**的模式，Hybrid底层依赖与native提供的容器-UIwebview，上层使用了html+css+hs做业务开发。



## 对比



### 接下来通过表格对Native app和Hybrid app的优缺点进行一个对比

| ...      | native      | html5           | hybrid    |
| -------- | ----------- | --------------- | --------- |
| 图形渲染 | 本地API渲染 | html+canvas+css | 混合      |
| 性能     | 快          | 慢              | 慢        |
| 原生界面 | 原生        | 模仿            | 模仿      |
| 发布     | app store   | web             | app store |



`个人意见`:hybrid更容易快速试错，native中ios中不支持热更新，android中因为碎片化，很多也不支持热更新，这时候hybrid的优越性就出来了，但是性能比原生确实差一些。为了强化hybrid，很多新的东西也诞生了，eg：`react native`和阿里的`weex`(感觉淘宝之前好像有些是用了weex，不是太了解，接下来去了解一下，hh)。



关于react native：它采用了ui界面通过原生来绘制，然后交互事件等用js来实现，原理上来说比hybrid会快一些，但是由于是新技术，个人感觉稳定性还是差一点点，具体未来的发展还是 期待，hh





# Native与H5的职责



1. H5做业务

1. Native提供基础能力



为了达到配合的完美效果，h5是可以提供一个webview的生命周期，然后定制事件的回调方法，然后native可以去调用.......
这里有一张图：
![image](https://upload-images.jianshu.io/upload_images/3378252-255b765067c01bf9.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/1240)



在考虑native和前端的设计的时候，会大概考虑到以下几点：



- 交互设计

- - 这里主要就是需要前端和native端好好配合，提供通过的接口，eg：h5与native之间的跳转、设备信息读取的接口等等

- 资源访问

- 账号信息设计

- hybrid开发调试



## hybrid交互



1. JavaScript core == js bridge

1. url schema



这里有一张图可以简单说明：
![image](https://upload-images.jianshu.io/upload_images/3378252-99b7d4e57413b849.png?imageMogr2/auto-orient/strip|imageView2/2/w/1240)



其实Hybrid的交互无非就2种方法：



- native调用前端页面中js方法

- 前端页面通过js调用native提供的接口



```
2个交互的bridge都是webview
```



![image](https://upload-images.jianshu.io/upload_images/3378252-a10d0fed8dca91f8.png?imageMogr2/auto-orient/strip|imageView2/2/w/1240)



从第一个开始讲解：web和native约定好，然后h5将一组api绑定在window对象上，app通过webview获取window对象然后调用js。这就是最初说的**native调用页面中js的方法**



第二个：web传递一个url，然后native那边可以截断url，通过url中的schema进行解析，然后做出相应的操作



然后app自身是可以自定义url schema的，并把自定义的url注册在调度中心，比如：



- weixin:// 打开微信

- ctrip：//xx  打开携程



js与native通信：创建这类url，native将其捕获，然后进行操作。



### api式交互



native api接口和ajax调用服务器提供的接口非常相似



![image](https://upload-images.jianshu.io/upload_images/3378252-438484e7600dd71f.png?imageMogr2/auto-orient/strip|imageView2/2/w/1240)



然后接下来这张图可以清楚的展示我们在设计hybrid交互模型的时候，通信的流程：



![image](https://upload-images.jianshu.io/upload_images/3378252-a3f0bcf34c210847.png?imageMogr2/auto-orient/strip|imageView2/2/w/1240)



### 格式约定



前面说了这个流程和ajax比较相似，这里就按照ajax做一个参考。



```
$.ajax(options) => XMLHttpRequest type(default val：“get”) http请求方法（“get”，“post”，其他） url(default val：当前url) 请求url的地址 data(default val：none)
```



与native约定的请求模型：

```
requestHybrid({
  //创建一个新的webview对话框窗口
  tagname: 'hybridapi',
  //请求参数，会被Native使用
  param: {},
  //Native处理成功后回调前端的方法
  callback: function (data) {
  }
});
```



这个方法就会形成一个url，比如：
hybridschema://hybridapi?callback=hybrid_1446276509894&param=%7B%22data1%22%3A1%2C%22data2%22%3A2%7D



然后数据会得到返回，一般的格式是这样的：

```
{
  data: {},
  errno: 0,
  msg: "success"
}
```



code：

```
window.Hybrid = window.Hybrid || {};
var bridgePostMsg = function (url) {
    if ($.os.ios) {
        window.location = url;
    } else {
        var ifr = $('<iframe style="display: none;" src="' + url + '"/>');
        $('body').append(ifr);
        setTimeout(function () {
            ifr.remove();
        }, 1000)
    }
};
var _getHybridUrl = function (params) {
    var k, paramStr = '', url = 'scheme://';
    url += params.tagname + '?t=' + new Date().getTime(); //时间戳，防止url不起效
    if (params.callback) {
        url += '&callback=' + params.callback;
        delete params.callback;
    }
    if (params.param) {
        paramStr = typeof params.param == 'object' ? JSON.stringify(params.param) : params.param;
        url += '&param=' + encodeURIComponent(paramStr);
    }
    return url;
};
var requestHybrid = function (params) {
    //生成唯一执行函数，执行后销毁
    var tt = (new Date().getTime());
    var t = 'hybrid_' + tt;
    var tmpFn;

    //处理有回调的情况
    if (params.callback) {
        tmpFn = params.callback;
        params.callback = t;
        window.Hybrid[t] = function (data) {
            tmpFn(data);
            delete window.Hybrid[t];
        }
    }
    bridgePostMsg(_getHybridUrl(params));
};
//获取版本信息，约定APP的navigator.userAgent版本包含版本信息：scheme/xx.xx.xx
var getHybridInfo = function () {
    var platform_version = {};
    var na = navigator.userAgent;
    var info = na.match(/scheme\/\d\.\d\.\d/);

    if (info && info[0]) {
        info = info[0].split('/');
        if (info && info.length == 2) {
            platform_version.platform = info[0];
            platform_version.version = info[1];
        }
    }
    return platform_version;
};
```



## 3种常见的hybird的通信方式



- 传统jsinterface

- jsbridge

- urlRouter



掘金中有一篇文章写的很详细 点击[这里](
