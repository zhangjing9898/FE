# vue 源码探究（第二弹）

接着上一篇，继续来讲一个非常有意思的东西`documentFragment`

### 解析

#### 六、DocumentFragment: 文档碎片(高效批量更新多个节点)

这里先甩出 2 个概念，什么是 document，以及什么是 documentFragment

- document: 对应显示的页面, 包含 n 个 element 一旦更新 document 内部的某个元素界面更新
- documentFragment: 内存中保存 n 个 element 的容器对象(不与界面关联), 如果更新 framgnet 中的某个 element, 界面不变

如果说，我们想要批量更新多个节点，可不可以这样理解，放入 documentFragment 中去，更新完了之后，再插入 document 中，因为 documentFragment 也是 document 下的一个节点。

举一个简单的例子：

```HTML
  <ul id="fragment_test">
    <li>test1</li>
    <li>test2</li>
    <li>test3</li>
  </ul>
```

如果我们想把ul中li中的文本信息，全部由testx变成zhangjing123，原始的方式是不是这样做：
1. 遍历
2. 更改

这样，是不是document刷新了3次，如果说有1万个li呢，document是不是要刷新1万次？
明显这样很不好，那么文档碎片出场了。

```js
  const ul = document.getElementById('fragment_test')
  // 1. 创建fragment
  const fragment = document.createDocumentFragment()
  // 2. 取出ul中所有子节点取出保存到fragment

  // 这里提一个小问题，如果这样的代码，会变成啥样？
  // let child
  // while(child=ul.firstChild) { 
  // }
  // 答案：死循环
  // 那为什么加了一行 fragment.appendChild(child) 就可以呢？
  // 因为一个节点只能有一个父亲
  // NOTICE：ul和li中的换行也是一个文本节点，所以我们使用
//   firstChild用法
//   此属性能够获取指定元素节点下的第一个子节点。
//   如果元素下没有子节点，那么返回null。
//   特别说明：
//  （1）.子节点并不一定都是元素节点，还有可能包含文本节点或者注释节点等节点。
//  （2）.空格或者换行被看做文本节点。
  let child
  while(child=ul.firstChild) { 
    // 一个节点只能有一个父亲
    fragment.appendChild(child)  
    // 先将child从ul中移除, 添加为fragment子节点
  }

  // 3. 更新fragment中所有li的文本
  Array.prototype.slice.call(fragment.childNodes).forEach(node => {
    if (node.nodeType===1) { // 元素节点 <li>
      node.textContent = 'zhangjing123'
    }
  })

  // 4. 将fragment插入ul
  ul.appendChild(fragment)

```

未完待续... 
接下来，还有一个更有趣的东西

**下一章继续~**
