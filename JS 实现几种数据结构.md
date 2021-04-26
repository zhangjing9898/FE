

<a name="IeRbS"></a>
# 🚀 栈

<br />用ES6 的语法 class 来描述非常简洁；记住前进后出就行<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/281196/1619145405831-01085d8f-40f0-4a76-9ebd-9d36c03ffd6f.png#clientId=u02367af6-91f9-4&from=paste&height=230&id=udb94fe95&margin=%5Bobject%20Object%5D&name=image.png&originHeight=460&originWidth=640&originalType=binary&size=22196&status=done&style=none&taskId=ue49f66bc-a284-4d39-89cf-c42d6245ae9&width=320)
```javascript
class Stack {
  constructor() {
    this.items = []
  }
  
  push(element) {
    this.items.push(element)
  }
  pop() {
    return this.items.pop()
  }
  get size() {
    return this.items.length
  }
  get isEmpty() {
    return !this.items.length
  }
  clear() {
    this.items = []
  }
  print() {
    console.log(this.items.toString())
  }
}
// 初始化一个栈
var s = new Stack()
s.push(1)
s.push(2)
s.push(3)
s.push(4)
console.log(s)
console.log(s.isEmpty)
console.log(s.size)
```


<a name="DF1um"></a>
# 🍳 队列

<br />队列和栈都是顺序存储结构，是用数组来模拟实现的，但是两者唯一的区别就在于栈是先进后出，队列是先进先出<br />
<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/281196/1619145394367-7edf0eb2-6a5a-40ae-9ba7-61a7a26d0142.png#clientId=u02367af6-91f9-4&from=paste&height=210&id=u362381ef&margin=%5Bobject%20Object%5D&name=image.png&originHeight=419&originWidth=640&originalType=binary&size=43416&status=done&style=none&taskId=u720b967a-35cc-4681-b571-3b4b6ef071b&width=320)
```javascript
class Queue {
  constructor() {
    this.items = []
  }
  enqueue(element) {
    this.items.push(element)
  }
  shift() {
    return this.items.shift()
  }
  get size() {
    return this.items.length
  }
  get isEmpty() {
    return !this.items.length
  }
  clear() {
    this.items = []
  }
  print() {
    console.log(this.items.toString())
  }
}
// 初始化一个队列
var s = new Queue()
s.enqueue(1)
s.enqueue(2)
s.enqueue(3)
s.enqueue(4)
console.log(s)
console.log(s.isEmpty)
console.log(s.size)
```

<br />

<a name="BP0en"></a>
# 🛀 链表

<br />链表的 JS 实现逻辑，也是链式存储最典型的例子<br />
<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/281196/1619145365254-8f50a8ba-fa81-4bf9-b6ce-96f5d91c8079.png#clientId=u63da74cb-0d04-4&from=paste&height=89&id=uf1ab9457&margin=%5Bobject%20Object%5D&name=image.png&originHeight=178&originWidth=1060&originalType=binary&size=44600&status=done&style=none&taskId=u378dfac6-73dc-47b6-bfc9-a5eba1bd790&width=530)
```javascript
class Node {
  constructor(element) {
    this.element = element
    this.next = null
  }
}
class LinkedList {
  constructor() {
    this.head = null
    this.length = 0
  }
  append(element) {
    const node = new Node(element)
    let current = null
    if (this.head == null) {
      this.head = node
    } else {
      current = this.head
      while (current.next) {
        current = current.next
      }
      current.next = node
    }
    this.length++
  }
  insert(position, element) {
    if (position < 0 && position > this.length) {
       return false 
    } else {
      const node = new Node(element)
      let current = this.head
      let previous = null
      let index = 0
      if (position === 0) {
        this.head = node
        node.next = current
      } else {
        while (index++ < position) {
          previous = current
          current = current.next
        }
        node.next = current
        previous.next = node
      }
      this.length++
      return true
    }
  }
  
  removeAt(position) {
    if (position < 0 && position > this.length) {
      return false
    } else {
      let current = this.head
      let previous = null
      let index = 0
      if (position === 0) {
        this.head = current.next
      } else {
        while (index++ < position) {
          previous = current
          current = current.next
        }
        previous.next = current.next
      }
      this.length--
      return true
    }
  }
  findIndex(element) {
    let current = this.head
    let index = -1
    while (current) {
      if (current.element === element) {
        return index + 1
      }
      index++
      current = current.next
    }
    return -1
  }
  remove(element) {
    const index = this.findIndex(element)
    return this.removeAt(index)
  }
  get size() {
    return this.length
  }
  get isEmpty() {
    return !this.length
  }
  toString() {
    let current = this.head
    let slink = ''
    while (current) {
      slink += `${current.element}-`
      current = current.next
    }
    return slink
  }
}
// 初始化一个链表
var s = new LinkedList()
s.append(1)
s.append(2)
s.append(3)
s.append(4)
console.log(s)
console.log(s.isEmpty)
console.log(s.size)
```


<a name="mCEkA"></a>
# 🏃‍♀️ 二叉树

<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/281196/1619145420127-7dd1a15b-d7c0-4a00-9d1b-e6c062a6658b.png#clientId=u02367af6-91f9-4&from=paste&height=134&id=u773da6f1&margin=%5Bobject%20Object%5D&name=image.png&originHeight=267&originWidth=320&originalType=binary&size=18139&status=done&style=none&taskId=uaefaf436-5c35-4b70-a1a9-63ce381b506&width=160)<br />
<br />树拥有很多种结构，二叉树是树中最常用的结构，同时也是一个天然的递归结构。<br />二叉树拥有一个根节点，每个节点至多拥有两个子节点，分别为：左节点和右节点。<br />树的最底部节点称之为叶节点，当一颗树的叶数量数量为满时，该树可以称之为满二叉树。
```javascript
class Node {
  constructor(element) {
    this.element = element
    this.left = null
    this.right = null
  }
}
class BinarySearchTree {
  constructor() {
    this.root = null
  }
  insert(element) {
    const temp = new Node(element)
    var insertNode = function(root, node) {
      if (node.element > root.element) {
        if (root.right === null) {
          root.right = node
        } else {
          insertNode(root.right, node)
        }
      } else {
        if (root.left === null) {
          root.left = node
        } else {
          insertNode(root.left, node)
        }
      }
    }
    if (!this.root) {
      this.root = temp
    } else {
      insertNode(this.root, temp)
    }
  }
  // 中序遍历
  inOrderTraverse(callback) {
    const inOrderTraverseNode = (node, callback) => {
      if (node !== null) {
        inOrderTraverseNode(node.left, callback)
        callback(node.element)
        inOrderTraverseNode(node.right, callback)
      }
    }
    inOrderTraverseNode(this.root, callback)
  }
  // 前序遍历
  preOrderTraverse(callback) {
    const preOrderTraverseNode = (node, callback) => {
      if (node !== null) {
        callback(node.element)
        preOrderTraverseNode(node.left, callback)
        preOrderTraverseNode(node.right, callback)
      }
    }
    preOrderTraverseNode(this.root, callback)
  }
  // 后序遍历
  postOrderTraverse(callback) {
    const postOrderTraverseNode = (node, callback) => {
      if (node !== null) {
        postOrderTraverseNode(node.left, callback)
        postOrderTraverseNode(node.right, callback)
        callback(node.element)
      }
    }
    postOrderTraverseNode(this.root, callback)
  }
  min() {
    const minNode = node => {
      return node ? (node.left ? minNode(node.left) : node) : null
    }
    return minNode(this.root)
  }
  max() {
    const maxNode = node => {
      return node ? (node.right ? maxNode(node.right) : node) : null
    }
    return maxNode(this.root)
  }
  search(key) {
    const searchNode = (node, key) => {
      if (node === null) return
      if (node.element === key) {
        console.log(node)
        return node
      } else {
        return searchNode((key < node.element) ? node.left : node.right, key)
      }
    }
    searchNode(this.root, key)
  }
}
// 初始化一个BST
const tree = new BinarySearchTree()
tree.insert(11)
tree.insert(7)
tree.insert(5)
tree.insert(3)
tree.insert(9)
tree.insert(8)
tree.insert(10)
tree.insert(13)
tree.insert(12)
tree.insert(14)
tree.insert(20)
tree.insert(18)
tree.insert(25)
console.log(tree)
// 调用二叉树对应地获取最大最小，以及搜索的方法
var m = tree.min()
console.log(m.element)
var max = tree.max()
console.log(max)
var r = tree.search(12)
console.log(r)
```
<a name="TSxwm"></a>
# 
<a name="UcnSi"></a>
# 🥛 反转单向链表

<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/281196/1619145830918-ab219e92-c1ea-45b9-a2eb-a3c08ac01435.png#clientId=ue22fb94b-5a5a-4&from=paste&height=89&id=u1337197c&margin=%5Bobject%20Object%5D&name=image.png&originHeight=178&originWidth=1060&originalType=binary&size=44600&status=done&style=none&taskId=u2d3449d6-47d5-4a1f-813c-cee07efe4f6&width=530)<br />题目：需要将一个单向链表反转。<br />思路很简单：使用三个变量分别表示 当前节点 和 当前节点的前后节点
```javascript
const reverseList = function(head) {
    // 判断下变量边界问题
    if (!head || !head.next) return head
    // 初始设置为空，因为第一个节点反转后就是尾部，尾部节点指向 null
    let pre = null
    let current = head
    let next
    // 判断当前节点是否为空
    // 不为空就先获取当前节点的下一节点
    // 然后把当前节点的 next 设为上一个节点
    // 然后把 current 设为下一个节点，pre 设为当前节点
    while(current) {
        next = current.next
        current.next = pre
        pre = current
        current = next
    }
    return pre
};
```
<a name="YFR4K"></a>
# 
<a name="wnnlp"></a>
# 🍗 二叉树的先序，中序，后序遍历

<br />先序遍历表示先访问根节点，然后访问左节点，最后访问**右节点**。<br />中序遍历表示先访问左节点，然后访问根节点，最后访问**右节点**。<br />后序遍历表示先访问左节点，然后访问**右节点**，最后访问根节点。<br />
<br />

<a name="r65TY"></a>
## 🌞 递归实现
```javascript
function TreeNode(val) {
  this.val = val;
  this.left = this.right = null;
}
var traversal = function(root) {
  if (root) {
    // 先序
    console.log(root); 
    traversal(root.left);
    // 中序
    // console.log(root); 
    traversal(root.right);
    // 后序
    // console.log(root);
  }
};
```
