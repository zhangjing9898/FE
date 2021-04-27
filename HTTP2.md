HTTP/2 相比于 HTTP/1，可以说是大幅度提高了网页的性能。<br />
<br />在 HTTP/1 中，为了性能考虑，我们会引入雪碧图、将小图内联、使用多个域名等等的方式。这一切都是因为浏览器限制了同一个域名下的请求数量（Chrome 下一般是限制六个连接），当页面中需要请求很多资源的时候，队头阻塞（Head of line blocking）会导致在达到最大请求数量时，剩余的资源需要等待其他资源请求完成后才能发起请求。<br />
<br />在 HTTP/2 中引入了多路复用的技术，这个技术可以只通过一个 TCP 连接就可以传输所有的请求数据。<br />多路复用很好的解决了浏览器限制同一个域名下的请求数量的问题，同时也接更容易实现全速传输，毕竟新开一个 TCP 连接都需要慢慢提升传输速度。<br />
<br />大家可以通过[该链接](https://link.juejin.im/?target=https%3A%2F%2Fhttp2.akamai.com%2Fdemo)感受下 HTTP/2 比 HTTP/1 到底快了多少。<br />
<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/281196/1619418956825-f1da6010-6bf3-4518-bdc3-be30dafe3a66.png#clientId=u84afb989-7d9a-4&from=paste&height=390&id=u5f1cbcb5&margin=%5Bobject%20Object%5D&name=image.png&originHeight=780&originWidth=1344&originalType=binary&size=1179668&status=done&style=none&taskId=u223966d8-0615-473e-aa6f-3fa96bf0e1a&width=672)<br />
<br />在 HTTP/1 中，因为队头阻塞的原因，你会发现发送请求是长这样的<br />
<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/281196/1619418981546-dfa34e12-576b-4072-8579-b97bf71c22b9.png#clientId=u84afb989-7d9a-4&from=paste&height=642&id=ud19ab1da&margin=%5Bobject%20Object%5D&name=image.png&originHeight=642&originWidth=518&originalType=binary&size=178502&status=done&style=none&taskId=ua5277964-b296-416e-9bec-86a711f5673&width=518)<br />
<br />在 HTTP/2 中，因为可以复用同一个 TCP 连接，你会发现发送请求是长这样的<br />
<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/281196/1619419000708-40bde116-5edb-4d94-8602-3da219c93e8a.png#clientId=u84afb989-7d9a-4&from=paste&height=488&id=uda1196df&margin=%5Bobject%20Object%5D&name=image.png&originHeight=616&originWidth=900&originalType=binary&size=192719&status=done&style=none&taskId=ua41370d3-cc1e-40e2-a358-f412ad80541&width=713)<br />

<a name="zTcbF"></a>
## 🍳 二进制传输

<br />HTTP/2 中所有加强性能的核心点在于此。在之前的 HTTP 版本中，我们是通过文本的方式传输数据。<br />
<br />在 HTTP/2 中引入了新的编码机制，所有传输的数据都会被分割，并采用二进制格式编码<br />
<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/281196/1619419171431-47fbda7d-963d-4906-81c4-6245e1f2b822.png#clientId=u84afb989-7d9a-4&from=paste&height=316&id=u22d443f2&margin=%5Bobject%20Object%5D&name=image.png&originHeight=459&originWidth=874&originalType=binary&size=125944&status=done&style=none&taskId=ue9b04866-e89c-49e4-a886-32c811cc197&width=601)<br />

<a name="JSze0"></a>
## 🏊 多路复用

<br />在 HTTP/2 中，有两个非常重要的概念，分别是帧（frame）和流（stream）。<br />
<br />帧代表着最小的数据单位，每个帧会标识出该帧属于哪个流，流也就是多个帧组成的数据流。<br />
<br />多路复用，就是在一个 TCP 连接中可以存在多条流。换句话说，也就是可以发送多个请求，对端可以通过帧中的标识知道属于哪个请求。通过这个技术，可以避免 HTTP 旧版本中的队头阻塞问题，极大的提高传输性能。<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/281196/1619419231683-5a79a755-a49f-46b0-a47a-6e1e97c3d246.png#clientId=u84afb989-7d9a-4&from=paste&height=138&id=ub35aab49&margin=%5Bobject%20Object%5D&name=image.png&originHeight=138&originWidth=494&originalType=binary&size=14935&status=done&style=none&taskId=u777ca682-5f00-4749-8f45-1d79e85fcc0&width=494)<br />

<a name="Dorn2"></a>
## 🏃‍♀️ Header 压缩

<br />在 HTTP/1 中，我们使用文本的形式传输 header，在 header 携带 cookie 的情况下，可能每次都需要重复传输几百到几千的字节。<br />
<br />在 HTTP /2 中，使用了 HPACK 压缩格式对传输的 header 进行编码，减少了 header 的大小。<br />并在两端维护了索引表，用于记录出现过的 header ，后面在传输过程中就可以传输已经记录过的 header 的键名，对端收到数据后就可以通过键名找到对应的值。<br />

<a name="B0jH9"></a>
## 🐻 服务端 Push
在 HTTP/2 中，服务端可以在客户端某个请求后，主动推送其他资源。<br />
<br />可以想象以下情况，某些资源客户端是一定会请求的，这时就可以采取服务端 push 的技术，提前给客户端推送必要的资源，这样就可以相对减少一点延迟时间。当然在浏览器兼容的情况下你也可以使用 prefetch <br />
<br />

<a name="HNipQ"></a>
# 🍔 总结


- HTTP/2 通过多路复用、二进制流、Header 压缩等等技术，极大地提高了性能，但是还是存在着问题的
- QUIC 基于 UDP 实现，是 HTTP/3 中的底层支撑协议，该协议基于 UDP，又取了 TCP 中的精华，实现了即快又可靠的协议
