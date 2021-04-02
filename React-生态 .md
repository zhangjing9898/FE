React 的一大优势就是**生态的丰富性**，有众多可以满足各式各样需求的轮子。即便当前的轮子不能满足我们的需求，在对生态足够了解的情况下，我们依然可以根据已有的轮子，站在巨人的肩膀上去改造它。





### 初始化



**初始化工程：**项目一般用官方维护的 create-react-app，这个工具使用起来简单便捷，但 create-react-app 的配置隐藏比较深，修改配置时搭配 react-app-rewired 更为合适。国内的话通常还会用 dva 或者 umi 初始化项目，它们提供了一站式解决方案。dva 更关心数据流领域的问题，而 umi 更关心前端工程化。



**初始化库：**一般会用到 create-react-library，也基本是零配置开始开发，底层用 rollup 进行构建。如果是维护大规模组件的话，通常会使用[ StoryBook](https://storybook.js.org/)，它的交互式开发体验可以降低组件库的维护成本。



### 开发



开发通常会有路由、样式、基础组件、功能组件、状态管理等五个方面需要处理。



- 路由：使用 React Router 解决，它底层封装了 HTML5 的 history API 实现前端路由，也支持内存路由。
- 样式：有两个解决方案

- - **CSS 模块化**

- - - CSS 模块化主要由 css-loader 完成

- - **CSS in JS** 

- - - 而 CSS in JS 比较流行的方案有 emotion 和  styled-components
    - [emotion](https://emotion.sh/) 提供 props 接口消灭内联样式（或者这么说：通过操作 React 的 props 操作 CSS），[例子](https://www.jianshu.com/p/d54853c92e76)
    - [styled-components](https://zhuanlan.zhihu.com/p/28876652) 通过模板字符串提供基础的样式组件

![image](https://intranetproxy.alipay.com/skylark/lark/0/2021/png/129546/1617333085719-f73ee170-0ad8-45a8-a5cb-9796a595817c.png)

- 基础组件库：中后台使用 [Antd](https://ant.design/)；To C的话，还是得看组内有什么基础组件了...
- 功能组件：

- - 实现拖拽：react-dnd 和 react-draggable，react-dnd 相对于 react-draggable，在拖放能力的抽象与封装上做得更好，下层差异屏蔽更完善，更适合做跨平台适配
  - PDF 预览：react-pdf-viewer
  - 视频播放：Video-React
  - 长列表：react-window 与 react-virtualized，两者的作者是同一个人，react-window 相对于 react-virtualized 体积更小，也被作者推荐。

- 状态管理：主要是 Redux 与 Mobx，这两者的区别就很大了，Redux 主要基于全局单一状态的思路，Mobx 主要是基于响应式的思路，更像 Vue。



### 构建



构建主要是 webpack、Rollup 与 esBuild

- webpack 久经考验，更适合做大型项目的交付
- Rollup 常用于打包小型的库，更干净便捷

- esBuild 自身定位于打包及压缩工具，性能十分优异，核心代码由 Go 语言编写，与传统构建器相比，性能最大可以跑出 100 倍的差距，值得长期关注，尤其是与 webpack 结合使用这点，便于优化 webpack 构建性能。





### 检查



检查主要是代码规范与代码测试编写



1. 代码规范检查一般是 ESLint，再装插件，属于常规操作。
2. 编写代码测试会用到 jest、enzyme、react-testing-library、react-hooks-testing-library

1. 1. jest 是 Facebook 大力推广的测试框架
   2. enzyme 是 Aribnb 大力推广的测试工具库，基本完整包含了大部分测试场景
   3. react-testing-library 与 react-hooks-testing-library 是由社区主推的测试框架，功能上与 enzyme 部分有所重合。



### 发布



工程静态资源主要托管在 CDN 上，所以需要在 webpack 中引入上传插件
