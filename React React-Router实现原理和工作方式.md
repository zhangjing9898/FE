React Router 路由的基础实现原理分为两种

1. 切换 Hash 的方式，那么依靠浏览器 Hash 变化即可
2.  HTML5中的History pushState - pushState、replaceState



## 🚶 实现原理



#### MPA多页应用时代



在过去的MPA时代，路由完全是由服务端进行控制的。



#### SPA单页面应用时代



现在的网页大部分是以单页应用（SPA）的方案完成交付。通俗点来讲，通过浏览器中的 **JavaScript 控制**页面**路由**。



#### 前端路由的四次变化



- Java 的 JSP  (PS：前后端相互严重依赖

- AJAX 技术 （PS：前端不再与后端直接耦合，问题是：会存在多个 HTML 页面，由 Nginx 等静态文件服务完成托管
- 路由 通过 JavaScript 来控制，后面又提出用 Hash 中添加路由路径的方式控制前端路由 (PS: http://example.com#12 这种
- HTML5中的History pushState （PS：不再写 #12 这样的路由了，而是回归到最初的写法——[http://example.com/12](http://example.com/a)





## ☁️ 工作方式
