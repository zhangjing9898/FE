# debounce，throttle和requestAnimationFrame简介

DOM 上有些事件是会频繁触发的，在做搜索的时候，会出现这么一个问题，一输入了一点点就需要去发送请求，但是这样给服务器带来的压力是蛮大的，可以尝试使用一下lodash中的.debounce和.throttle。

### debounce

**防抖**

![img1](https://upload-images.jianshu.io/upload_images/1507403-2cb8aace22a69f63.png?imageMogr2/auto-orient/)

简单讲就是：不管上游事件触发了多少次，下游就产生了一次事件。也就是说当一次事件发生后，事件处理器要等一定阈值的时间，如果这段时间过去后 再也没有 事件发生，就处理最后一次发生的事件。假设还差 0.01 秒就到达指定时间，这时又来了一个事件，那么之前的等待作废，需要重新再等待指定时间。

这个就挺适合做搜索发起请求时候用的，等到用户keyup、keypress之后隔了xxms之后，没有下一个动作，说明他已经完成了输入，这时候，可以进行请求的发送，下面给一个最简单的例子来展示：

js部分:

```javascript
//引入
import debounce from 'lodash.debounce';
//设置函数去抖
var debounceFn=debounce(function(){
  //这里进行 你想做的操作 比如发请求 什么的
  var inputVal = $("#searchInput").val();
  self.searchcinemas(inputVal,city.code,{ "cityX": 121.47,"cityY": 31.23}, self.config.cinemaData,searchPageIndex,cityChange);
},300);
$('.schedules-block-container').find('header input').off().on('change input',function(){
  debounceFn();
}).on('click', function() {
  this.focus();
});
```

-------

### throttle

**节流**

throttle 也就是不允许方法在每xxx ms间执行超过一次，throttle和debounce的区别在于：throttle可以保证方法每xxx ms有规律的执行。

一个相当常见的例子，用户在你无限滚动的页面上向下拖动，你需要判断现在距离页面底部多少。如果用户快接近底部时，我们应该发送请求来加载更多内容到页面。

在此 _.debounce 没有用，因为它只会在用户停止滚动时触发，但我们需要用户快到达底部时去请求。通过_.throttle 我们可以不间断的监测距离底部多远。

--------

### requestAnimationFrame (rAF)

requestAnimationFrame是另一个频率限制的方法。requestAnimationFrame可以作为throttle的代替方法。

- 利：
  + 目标60fps（16ms每贞），但是内部使用最优的时间间隔来渲染
  + 使用简单并且是标准API，以后不会变动，不需要维护
- 弊：
  + rAF的开始或者取消需要我们自己处理，不像.debounce和.throttle内部实现
  + 浏览器Tag没有激活，它就不会执行
  + 即使多数现代浏览器支持，但是IE9，Opera Mini以及老版本Android依旧不支持。A polyfill到现在依旧需要
  + rAF在node.js中不支持

建议在JS执行”painting”或”animating”中直接操作属性和重新计算元素位置时使用rAF。

发送Ajax请求或者是否添加／删除class（触发一个CSS动画）时，我会考虑debounce和throttle，此时你可以降低执行频率（200ms而不是16ms）。

------ 

### 总结

使用debounce，throttle和requestAnimationFrame优化你的事件处理函数。每一个方法有一些细微的差别，三个都很有用而且互相弥补。

- debounce:把突然涌进的事件（键盘事件）归为一个
- throttle:保证持续执行方法分隔为每Xms执行一次。就像每200ms监测滚动位置来触发css动画。
- requestAnimationFrame:throttle的替代方案，当你的方法需要重新计算和渲染元素同时你需要更平滑的变动或动画。注意：IE9- 不支持。

