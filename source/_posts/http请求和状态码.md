---
title: http请求和状态码
date: 2022-10-20 21:10:06
categories: 计算机网络
tags:
- http
- CORS
---

# HTTP 请求方法

根据 HTTP 标准，HTTP 请求可以使用多种请求方法。

HTTP1.0 定义了三种请求方法： GET, POST 和 HEAD 方法。

HTTP1.1 新增了六种请求方法：OPTIONS、PUT、PATCH、DELETE、TRACE 和 CONNECT 方法。

| 序号 | 方法    | 描述                                                         |
| :--- | :------ | :----------------------------------------------------------- |
| 1    | GET     | 请求指定的页面信息，并返回实体主体。                         |
| 2    | HEAD    | 类似于 GET 请求，只不过返回的响应中没有具体的内容，用于获取报头。 |
| 3    | POST    | 向指定资源提交数据进行处理请求（例如提交表单或者上传文件）。数据被包含在请求体中。POST 请求可能会导致新的资源的建立和/或已有资源的修改。 |
| 4    | PUT     | 从客户端向服务器传送的数据取代指定的文档的内容。             |
| 5    | DELETE  | 请求服务器删除指定的页面。                                   |
| 6    | CONNECT | HTTP/1.1 协议中预留给能够将连接改为管道方式的代理服务器。    |
| 7    | OPTIONS | 允许客户端查看服务器的性能。**（预检请求）**                 |
| 8    | TRACE   | 回显服务器收到的请求，主要用于测试或诊断。                   |
| 9    | PATCH   | 是对 PUT 方法的补充，用来对已知资源进行局部更新 。           |

# 跨域资源共享 CORS

## 同源策略

同源策略(Same Origin Policy)是一种约定，它是浏览器最核心也是最基本的安全功能。同源策略会阻止一个域的javascrip脚本和另一个域的内容进行交互，是用于隔离潜在恶意文件的关键安全机制。

当一个请求url的协议、域名、端口三者之间任意一个与当前页面url不同即为跨域。

如果不是浏览器发起请求，就不会受到同源策略的影响。

同源策略目的就是为了保证用户信息的安全，防止恶意的网站窃取用户数据。**如果网页之间不满足“同源”的要求，那么它们之间：**

1. 不能共享Cookie、LocalStorage、SessionStorage、IndexedDB
2. 不能获取DOM
3. AJAX请求不能发送

**同源策略并不是浏览器不让请求发出去、或者后端拒绝返回数据。实际情况是请求正常发出去了，后端也正常相应了，只不过数据到了浏览器后浏览器不去作用加载而是丢弃了。**

不受同源策略影响的标签：

- img
- script
- style
- link
- iframe 引入资源可以允许不同域引入，但操作对应源里的dom是有跨域问题的

## 跨域解决方法

- 前端nginx反向代理
- 后端设置CORS允许跨域（Access-Control-Allow-Origin）
- jsonp：只适用于get请求

## CORS

**步骤一：客户端（浏览器）请求**

当浏览器发出跨域请求时，浏览器会添加一个带有当前源（方案、主机和端口）的 Origin 标头。

**步骤二：服务器响应**

在服务器端，当服务器看到此标头并希望允许访问时，它需要在响应中添加一个 **Access-Control-Allow-Origin** 标头，指定请求来源（或 * 以允许任何来源。）

**步骤三：浏览器收到响应**

当浏览器看到带有适当 Access-Control-Allow-Origin 标头的响应时，浏览器允许与客户端站点共享响应数据。

### CORS解决带cookie跨域问题

出于隐私原因，CORS 通常用于“匿名请求”——请求未识别请求者的请求。如果您想在使用 CORS（可以识别发送者）时发送 cookie，您需要向请求和响应添加额外的标头。

1. 请求

   添加`credentials: 'include'`到请求参数中即可实现带cookie进行跨域请求

   ```js
   fetch('https://example.com', {
     mode: 'cors',
     credentials: 'include'
   })
   ```

2. 响应

   `Access-Control-Allow-Origin`必须设置特定的值 (不能使用通配符`*`) 并且必须设置`Access-Control-Allow-Credentials 为 true`.

   ```http
   HTTP/1.1 200 OK
   Access-Control-Allow-Origin: https://example.com
   Access-Control-Allow-Credentials: true
   ```

### CORS相关字段	

**（1）Access-Control-Allow-Origin**

该字段是必须的。它的值要么是请求时`Origin`字段的值，要么是一个`*`，表示接受任意域名的请求。

**（2）Access-Control-Allow-Credentials**

该字段可选。它的值是一个布尔值，表示是否允许发送Cookie。默认情况下，Cookie不包括在CORS请求之中。设为`true`，即表示服务器明确许可，Cookie可以包含在请求中，一起发给服务器。这个值也只能设为`true`，如果服务器不要浏览器发送Cookie，删除该字段即可。

**（3）Access-Control-Expose-Headers**

该字段可选。CORS请求时，`XMLHttpRequest`对象的`getResponseHeader()`方法只能拿到6个基本字段：`Cache-Control`、`Content-Language`、`Content-Type`、`Expires`、`Last-Modified`、`Pragma`。如果想拿到其他字段，就必须在`Access-Control-Expose-Headers`里面指定。

# 简单请求和非简单请求

浏览器将CORS请求分成两类：`简单请求（simple request）`和`非简单请求（not-so-simple request`）。

只要同时满足以下两大条件，就属于简单请求。

1. 请求方法是以下三种方法之一：

   HEAD
   GET
   POST

2. HTTP的头信息不超出以下几种字段：
   Accept
   Accept-Language
   Content-Language
   Last-Event-ID
   Content-Type：只限于三个值`application/x-www-form-urlencoded`、`multipart/form-data`、`text/plain`

# 预检请求

非简单请求是那种对服务器有特殊要求的请求，比如请求方法是`PUT`或`DELETE`，或者`Content-Type`字段的类型是`application/json`。

非简单请求的CORS请求，会在正式通信之前，增加一次HTTP查询请求，称为"预检"请求（preflight）。

浏览器先询问服务器，当前网页所在的域名是否在服务器的许可名单之中，以及可以使用哪些HTTP动词和头信息字段。只有得到肯定答复，浏览器才会发出正式的`XMLHttpRequest`请求，否则就报错。

**预检请求实际上是对服务端的一种权限请求**

```http
OPTIONS /cors HTTP/1.1
Origin: http://api.aaa.com
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: X-Custom-Header
Host: api.ccc.com
Connection: keep-alive
User-Agent: Mozilla/5.0...
```

**预检请求**用的请求方法是`OPTIONS`，表示这个请求是用来询问的。头信息里面，关键字段是`Origin`，表示请求来自哪个源。

除了`Origin`字段，"预检"请求的头信息包括两个特殊字段。

**（1）Access-Control-Request-Method**

该字段是必须的，用来列出浏览器的CORS请求会用到哪些HTTP方法，上例是`PUT`。

**（2）Access-Control-Request-Headers**

该字段是一个逗号分隔的字符串，指定浏览器CORS请求会额外发送的头信息字段，上例是`X-Custom-Header`。

## 预检请求的回应

服务器收到预检请求以后，检查了`Origin`、`Access-Control-Request-Method`和`Access-Control-Request-Headers`字段以后，确认允许跨源请求，就可以做出回应。

> HTTP/1.1 200 OK
> Date: Mon, 01 Dec 2008 01:15:39 GMT
> Server: Apache/2.0.61 (Unix)
> Access-Control-Allow-Origin: [http://api.bob.com](https://link.zhihu.com/?target=http%3A//api.bob.com)
> Access-Control-Allow-Methods: GET, POST, PUT
> Access-Control-Allow-Headers: X-Custom-Header
> Content-Type: text/html; charset=utf-8
> Content-Encoding: gzip
> Content-Length: 0
> Keep-Alive: timeout=2, max=100
> Connection: Keep-Alive
> Content-Type: text/plain 

上面的HTTP回应中，关键的是`Access-Control-Allow-Origin`字段，表示`http://api.bob.com`可以请求数据。该字段也可以设为星号，表示同意任意跨源请求。

**如果浏览器否定了预检请求，会返回一个正常的HTTP回应，但是没有任何CORS相关的头信息字段。这时，浏览器就会认定，服务器不同意预检请求，因此触发一个错误，被`XMLHttpRequest`对象的`onerror`回调函数捕获。控制台会打印出如下的报错信息：**

```
XMLHttpRequest cannot load http://api.alice.com.
Origin http://api.bob.com is not allowed by Access-Control-Allow-Origin.
```

**一旦服务器通过了预检请求，以后每次浏览器正常的CORS请求，就都跟简单请求一样，会有一个`Origin`头信息字段。服务器的回应，也都会有一个`Access-Control-Allow-Origin`头信息字段。**

服务器回应的其他CORS相关字段如下：

**（1）Access-Control-Allow-Methods**

该字段必需，它的值是逗号分隔的一个字符串，表明服务器支持的所有跨域请求的方法。注意，返回的是所有支持的方法，而不单是浏览器请求的那个方法。这是为了避免多次"预检"请求。

**（2）Access-Control-Allow-Headers**

如果浏览器请求包括`Access-Control-Request-Headers`字段，则`Access-Control-Allow-Headers`字段是必需的。它也是一个逗号分隔的字符串，表明服务器支持的所有头信息字段，不限于浏览器在"预检"中请求的字段。

**（3）Access-Control-Allow-Credentials**

该字段与简单请求时的含义相同。

**（4）Access-Control-Max-Age**

该字段可选，用来指定本次预检请求的有效期，单位为秒。上面结果中，有效期是20天（1728000秒），即允许缓存该条回应1728000秒（即20天），在此期间，不用发出另一条预检请求

# HTTP状态码

TTP状态码（英语：HTTP Status Code），用以表示网页服务器超文本传输协议响应状态的3位数字代码。

它由 RFC 2616规范定义的，并得到 `RFC 2518`、`RFC 2817`、`RFC 2295`、`RFC 2774`与 `RFC 4918`等规范扩展。

简单来讲，`http`状态码的作用是服务器告诉客户端当前请求响应的状态，通过状态码就能判断和分析服务器的运行状态。

## 1xx

代表请求已被接受，需要继续处理。这类响应是临时响应，只包含状态行和某些可选的响应头信息，并以空行结束。

- 100（客户端继续发送请求，这是临时响应）：这个临时响应是用来通知客户端它的部分请求已经被服务器接收，且仍未被拒绝。客户端应当继续发送请求的剩余部分，或者如果请求已经完成，忽略这个响应。服务器必须在请求完成后向客户端发送一个最终响应
- 101：服务器根据客户端的请求切换协议，主要用于websocket或http2升级

## 2xx

代表请求已成功被服务器接收、理解、并接受。

- 200（成功）：请求已成功，请求所希望的响应头或数据体将随此响应返回
- 201（已创建）：请求成功并且服务器创建了新的资源
- 202（已创建）：服务器已经接收请求，但尚未处理
- 203（非授权信息）：服务器已成功处理请求，但返回的信息可能来自另一来源
- 204（无内容）：服务器成功处理请求，但没有返回任何内容
- 205（重置内容）：服务器成功处理请求，但没有返回任何内容
- 206（部分内容）：服务器成功处理了部分请求

## 3xx

表示要完成请求，需要进一步操作。 通常，这些状态代码用来重定向。

- 300（多种选择）：针对请求，服务器可执行多种操作。 服务器可根据请求者 (user agent) 选择一项操作，或提供操作列表供请求者选择
- 301（永久重定向）：永久重定向会缓存。请求的网页已永久移动到新位置。 服务器返回此响应（对 GET 或 HEAD 请求的响应）时，会自动将请求者转到新位置
- 302（临时重定向）： 临时重定向不会缓存。服务器目前从不同位置的网页响应请求，但请求者应继续使用原有位置来进行以后的请求
- 303（查看其他位置）：请求者应当对不同的位置使用单独的 GET 请求来检索响应时，服务器返回此代码
- 304（使用缓存）：协商缓存，告诉客户端有缓存，直接使用缓存中的数据，返回页面的只有头部信息，没有内容部分
- 305 （使用代理）： 请求者只能使用代理访问请求的网页。 如果服务器返回此响应，还表示请求者应使用代理
- 307 （临时重定向）： 服务器目前从不同位置的网页响应请求，但请求者应继续使用原有位置来进行以后的请求

## 4xx

代表了客户端看起来可能发生了错误，妨碍了服务器的处理。

- 400（错误请求）： 参数有误，请求无法被服务器识别
- 401（未授权）： 请求要求身份验证。 对于需要登录的网页，服务器可能返回此响应。
- 403（禁止）： 服务器拒绝请求
- 404（未找到）： 服务器找不到请求的网页
- 405（方法禁用）： 禁用请求中指定的方法
- 406（不接受）： 无法使用请求的内容特性响应请求的网页
- 407（需要代理授权）： 此状态代码与 401（未授权）类似，但指定请求者应当授权使用代理
- 408（请求超时）： 服务器等候请求时发生超时

## 5xx

表示服务器无法完成明显有效的请求。这类状态码代表了服务器在处理请求的过程中有错误或者异常状态发生。

- 500（服务器内部错误）：服务器遇到错误，无法完成请求
- 501（尚未实施）：服务器不具备完成请求的功能。 例如，服务器无法识别请求方法时可能会返回此代码
- 502（错误网关）： 服务器作为网关或代理，从上游服务器收到无效响应
- 503（服务不可用）： 服务器目前无法使用（由于超载或停机维护）
- 504（网关超时）： 服务器作为网关或代理，但是没有及时从上游服务器收到请求
- 505（HTTP 版本不受支持）： 服务器不支持请求中所用的 HTTP 协议版本
