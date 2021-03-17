最近在准备面试和笔试，然后去学习了一下JS原型链和继承方面的东西。
画图最容易理解：

![IMG_1709.JPG](https://upload-images.jianshu.io/upload_images/3378252-28d098ec2d54e196.JPG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果改变了孩子“吃饭”的方法，那么老人的“吃饭”方法也会改变，但是猴子的“吃饭”方法不会改变。

JS中万物皆对象。原型链就是一链一链的找上去，直到null。
__proto__ . __proto__
