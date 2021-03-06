# 关于iframe

- 下面是W3School对<iframe>的定义：
*iframe 元素会创建包含另外一个文档的内联框架（即行内框架）。*
不过按我的理解就是：iframe提供了一个简单的方式吧一个网站的内容嵌入到另一个网站中。 

- 那iframe一般用在什么方面呢？

目前用得最多的是管理后台类网站，左边一个菜单list，右边就是iframe的tabs,可以随时打开、关闭页面。在这种系统中，如果把iframe替换成div，那么大量页面中相同类型的表格、表单等就要用不同的id、class。因为js是针对body下的所有对象的，所以这种系统用iframe比较好。可以在iframe里面自由使用js和标签id的定义，而且父页面和菜单列表一般不会刷新，所以iframe带来的内容也就和一般页面的刷新一样。

如果我们需要独立的浏览上下文，那就用iframe，否则不用。

- iframe常被用于复用部分界面，比较早期的网站使用 iframe，主要是用于导航栏（navigator）。为什么？

因为一个网站很多页面的导航栏部分是相同的，在避免切换页面的时候重复下载，将导航栏和正文分开在 iframe 中，是一个方便的做法。同时带来的不利是，默认情况下，使用了 iframe 的网站的 URL 不会随着页面的变化而变化。这就意味着一旦刷新，网站可能又回到首页。

- 那么现在什么时候会用到 iframe 呢？

因为 iframe 的页面和父页面（parent）是分开的，所以它意味着，这是一个独立的区域，不受 parent 的 CSS 或者全局的 JavaScript 的影响。典型的，比如所见即所得的网页编辑器（WYSIWYG Online HTML Editor），因为它们需要 reset 自己的 CSS 到自己的标准，而不被 parent CSS 的 override。

使用 iframe 是不是一个好的用法（good practice），不能一概而论，但是可以肯定是，现在的大部分网站避免采用这种方式的。

# iframe的优点
------
* 隔离上下文，便于更改，模块分离

# iframe的缺点
-------
* iframe会阻塞主页面的Onload事件
* 搜索引擎的检索程序无法解读这种页面，不利于SEO
* iframe和主页面共享连接池，而浏览器对相同域的连接有限制，所以会影响页面的并行加载
* 使用iframe之前需要考虑这两个缺点。如果需要使用iframe，最好是通过javascript动态给iframe添加src属性值，这样可以绕开以上两个问题
