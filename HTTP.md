<a name="TYYfY"></a>
### 常考点：请求方法、首部的作用以及状态码的含义


<a name="GGwzP"></a>
# 🐒 HTTP 请求中的内容

<br />HTTP 请求由三部分构成，分别为：

- 请求行
- 首部
- 实体


<br />请求行：大概长这样 GET /images/logo.gif HTTP/1.1

- 基本由请求方法、URL、协议版本组成，这其中值得一说的就是请求方法了。



<a name="UEWJE"></a>
## 请求方法

<br />请求方法分为很多种，最常用的也就是Get和Post了。虽然请求方法有很多，但是更多的是传达一个语义，而不是说Post能做的事情Get就不能做了。如果你愿意，都使用Get请求或者Post请求都是可以的。更多请求方法的语义描述可以阅读[文档](https://link.juejin.im/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FHTTP%2FMethods)。<br />

<a name="FQqZc"></a>
## Q：Post 和 Get 的区别？

<br />首先先引入副作用和幂等的概念。<br />
<br />副作用指对服务器上的资源做改变，搜索是无副作用的，注册是副作用的。<br />
<br />幂等指发送 M 和 N 次请求（两者不相同且都大于 1），服务器上资源的状态一致，比如注册 10 个和 11 个帐号是不幂等的，对文章进行更改 10 次和 11 次是幂等的。因为前者是多了一个账号（资源），后者只是更新同一个资源。<br />
<br />在规范的应用场景上说，Get 多用于无副作用，幂等的场景，例如搜索关键字。Post 多用于副作用，不幂等的场景，例如注册。<br />
<br />在技术上说：

- Get 请求能缓存，Post 不能
- Post 相对 Get 安全一点点
   - 因为Get 请求都包含在 URL 里（当然你想写到body里也是可以的）
   - 且会被浏览器保存历史纪录。Post 不会，但是在抓包的情况下都是一样的。
- URL有长度限制，会影响 Get 请求，但是这个长度限制是浏览器规定的，不是 RFC 规定的
- Post 支持更多的编码类型且不对数据类型限制



<a name="goPRj"></a>
## 首部

<br />首部分为请求首部和响应首部，并且部分首部两种通用，接下来我们就来学习一部分的常用首部。<br />
<br />**通用首部**

| 通用字段 | 作用 |
| --- | --- |
| Cache-Control | 控制缓存的行为 |
| Connection | 浏览器想要优先使用的连接类型，比如keep-alive |
| Date | 创建报文时间 |
| Pragma | 报文指令 |
| Via | 代理服务器相关信息 |
| Transfer-Encoding | 传输编码方式 |
| Upgrade | 要求客户端升级协议 |
| Warning | 在内容中可能存在错误 |

**请求首部**

| 请求首部 | 作用 |
| --- | --- |
| Accept | 能正确接收的媒体类型 |
| Accept-Charset | 能正确接收的字符集 |
| Accept-Encoding | 能正确接收的编码格式列表 |
| Accept-Language | 能正确接收的语言列表 |
| Expect | 期待服务端的指定行为 |
| From | 请求方邮箱地址 |
| Host | 服务器的域名 |
| If-Match | 两端资源标记比较 |
| If-Modified-Since | 本地资源未修改返回 304（比较时间） |
| If-None-Match | 本地资源未修改返回 304（比较标记） |
| User-Agent | 客户端信息 |
| Max-Forwards | 限制可被代理及网关转发的次数 |
| Proxy-Authorization | 向代理服务器发送验证信息 |
| Range | 请求某个内容的一部分 |
| Referer | 表示浏览器所访问的前一个页面 |
| TE | 传输编码方式 |

**响应首部**

| 响应首部 | 作用 |
| --- | --- |
| Accept-Ranges | 是否支持某些种类的范围 |
| Age | 资源在代理缓存中存在的时间 |
| ETag | 资源标识 |
| Location | 客户端重定向到某个 URL |
| Proxy-Authenticate | 向代理服务器发送验证信息 |
| Server | 服务器名字 |
| WWW-Authenticate | 获取资源需要的验证信息 |

**实体首部**

| 实体首部 | 作用 |
| --- | --- |
| Allow | 资源的正确请求方式 |
| Content-Encoding | 内容的编码格式 |
| Content-Language | 内容使用的语言 |
| Content-Length | request body 长度 |
| Content-Location | 返回数据的备用地址 |
| Content-MD5 | Base64加密格式的内容 MD5检验值 |
| Content-Range | 内容的位置范围 |
| Content-Type | 内容的媒体类型 |
| Expires | 内容的过期时间 |
| Last_modified | 内容的最后修改时间 |

<a name="Nn3jq"></a>
## 
<a name="gJSHa"></a>
## 常见状态码

<br />状态码表示：响应的一个状态，可以让我们清晰的了解到这一次请求是成功还是失败，如果失败的话，是什么原因导致的，当然状态码也是用于传达语义的。如果胡乱使用状态码，那么它存在的意义就没有了。<br />

<a name="ZNyoT"></a>
### 2XX 成功


- 200 OK，表示从客户端发来的请求在服务器端被正确处理
- 204 No content，表示请求成功，但响应报文不含实体的主体部分
- 205 Reset Content，表示请求成功，但响应报文不含实体的主体部分，但是与 204 响应不同在于要求请求方重置内容
- 206 Partial Content，进行范围请求



<a name="YQ5Yt"></a>
### 3XX 重定向


- 301 moved permanently，永久性重定向，表示资源已被分配了新的 URL
- 302 found，临时性重定向，表示资源临时被分配了新的 URL
- 303 see other，表示资源存在着另一个 URL，应使用 GET 方法获取资源
- 304 not modified，表示服务器允许访问资源，但因发生请求未满足条件的情况
- 307 temporary redirect，临时重定向，和302含义类似，但是期望客户端保持请求方法不变向新的地址发出请求



<a name="Bdb7O"></a>
### 4XX 客户端错误


- 400 bad request，请求报文存在语法错误
- 401 unauthorized，表示发送的请求需要有通过 HTTP 认证的认证信息
- 403 forbidden，表示对请求资源的访问被服务器拒绝
- 404 not found，表示在服务器上没有找到请求的资源



<a name="Hs4oQ"></a>
### 5XX 服务器错误


- 500 internal sever error，表示服务器端在执行请求时发生了错误
- 501 Not Implemented，表示服务器不支持当前请求所需要的某个功能
- 503 service unavailable，表明服务器暂时处于超负载或正在停机维护，无法处理请求
