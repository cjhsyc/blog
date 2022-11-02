---
title: cookie、session和本地存储
date: 2022-10-23 15:24:08
categories: JavaScript
tags:
- cookie
- session
- localStorage
- sessionStorage
- indexedDB
---

# Cookie

`Cookie`，类型为「小型文本文件」，指某些网站为了辨别用户身份而储存在用户本地终端上的数据。是为了解决 `HTTP`无状态导致的问题。

作为一段一般不超过 4KB 的小型文本数据，它由一个名称（Name）、一个值（Value）和其它几个用于控制 `cookie`有效期、安全性、使用范围的可选属性组成。

但是`cookie`在每次请求中都会被发送，如果不使用 `HTTPS`并对其加密，其保存的信息很容易被窃取，导致安全风险。举个例子，在一些使用 `cookie`保持登录态的网站上，如果 `cookie`被窃取，他人很容易利用你的 `cookie`来假扮成你登录网站。

## cookie的使用

通过 `Document.cookie` 属性可创建新的 `Cookie`，也可通过该属性访问非`HttpOnly`标记的 `Cookie`。

```js
document.cookie = '名字=值'
// 通过 JavaScript 创建的 `Cookie` 不能包含 `HttpOnly` 标志。
```

`cookie`的修改，首先要确定`domain`和`path`属性都是相同的才可以，其中有一个不同得时候都会创建出一个新的`cookie`

```
Set-Cookie:name=aa; domain=aa.net; path=/  # 服务端设置
document.cookie =name=bb; domain=aa.net; path=/  # 客户端设置
```

`cookie`的删除，最常用的方法就是给`cookie`设置一个过期的时间，这样`cookie`过期后会被浏览器删除

## Set-Cookie

响应标头 **`Set-Cookie`** 被用来由服务器端向用户代理（一般是浏览器）发送 cookie，所以用户代理可再后续的请求中将其发送回服务器。服务器要发送多个 cookie，则应该在同一响应中发送多个 **`Set-Cookie`** 标头。

一个简单的 Cookie 可能像这样：

```
Set-Cookie: <cookie 名>=<cookie 值>
```

服务器通过该头部告知客户端保存 Cookie 信息。

```
HTTP/1.0 200 OK
Content-type: text/html
Set-Cookie: yummy_cookie=choco
Set-Cookie: tasty_cookie=strawberry

[页面内容]
```

现在，对该服务器发起的每一次新请求，浏览器都会将之前保存的 Cookie 信息通过 `Cookie` 请求头部再发送给服务器。

```
GET /sample_page.html HTTP/1.1
Host: www.example.org
Cookie: yummy_cookie=choco; tasty_cookie=strawberry
```

## 生命周期

Cookie 的生命周期可以通过两种方式定义：

- 会话期 Cookie 是最简单的 Cookie：浏览器关闭之后它会被自动删除，也就是说它仅在会话期内有效。会话期 Cookie 不需要指定过期时间（`Expires`）或者有效期（`Max-Age`）。需要注意的是，有些浏览器提供了会话恢复功能，这种情况下即使关闭了浏览器，会话期 Cookie 也会被保留下来，就好像浏览器从来没有关闭一样，这会导致 Cookie 的生命周期无限期延长。
- 持久性 Cookie 的生命周期取决于过期时间（`Expires`）或有效期（`Max-Age`）指定的一段时间。

例如：

```
Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2015 07:28:00 GMT;
```

## 属性

- `Expires=<date>` 

  cookie 的最长有效时间，形式为符合 HTTP-date 规范的时间戳。

  如果没有设置这个属性，那么表示这是一个**会话期 cookie**。一个会话结束于客户端被关闭时，这意味着会话期 cookie 在彼时会被移除。其截止时间与客户端相关，而非服务器的时间。

- `Max-Age=<number>`

  在 cookie 失效之前需要经过的秒数。秒数为 0 或 -1 将会使 cookie 直接过期。假如 `Expires` 和 `Max-Age` 属性均存在，那么 `Max-Age` 的优先级更高。

- `Domain=<domain-value>`

  指定 cookie 可以送达的主机名。

  假如没有指定，那么默认值为当前文档访问地址中的主机部分（但是不包含子域名）。 与之前的规范不同的是，域名（`.example.com`）之前的点号会被忽略。 多个主机/域名的值是不被允许的，但如果指定了一个域，则其子域也会被包含。

- `Path=<path-value>`

  指定一个 URL 路径，这个路径必须出现在要请求的资源的路径中才可以发送 `Cookie` 标头。

  字符 `/` 可以解释为文件目录分隔符，此目录的下级目录也满足匹配的条件（例如，如果 `path=/docs`，那么`/docs`、`/docs/`、`/docs/Web/` 和 `/docs/Web/HTTP` 都满足匹配条件。

  `/`、`/docsets` 或者 `/fr/docs` 则不满足匹配条件。

- `Secure`

  一个带有安全（Secure）属性的 cookie 只有在请求使用 `https:` 协议（localhost 不受此限制）的时候才会被发送到服务器。

- `HttpOnly`

  用于阻止 JavaScript 通过 `Document.cookie` 属性访问 cookie。注意，设置了 `HttpOnly` 的 cookie 在 JavaScript 初始化的请求中仍然会被发送。例如，调用 `XMLHttpRequest.send()` 或 `fetch()`。其用于防范跨站脚本攻击（XSS）。

- `SameSite=<samesite-value>`

  允许服务器设定一则 cookie 不随着跨站请求一起发送，这样可以在一定程度上防范跨站请求伪造攻击（CSRF）。

  可选的属性值有：

  - Strict

    这意味浏览器仅对同一站点的请求发送 `cookie`，即请求来自设置 cookie 的站点。如果请求来自不同的域或协议（即使是相同域），则携带有 `SameSite=Strict` 属性的 cookie 将不会被发送。

  - Lax

    这意味着 cookie 不会在跨站请求中被发送，如：加载图像或 frame 的请求。但 cookie 在用户从外部站点导航到源站时，cookie 也将被发送（例如，跟随一个链接）。这是 `SameSite` 属性未被设置时的默认行为。

    对于从第三方站点以`link标签`，`a标签`，`GET形式的Form提交`这三种方式访问目标系统时，会带上目标系统的Cookie，对于其他方式，如 `POST形式的Form提交`、`AJAX形式的GET`、`img的src`访问目标系统时，不带Cookie。

  - None

    这意味着浏览器会在跨站和同站请求中均发送 cookie。在设置这一属性值时，必须同时设置 `Secure` 属性，就像这样：`SameSite=None; Secure`。

## Cookie 前缀

名称中包含 `__Secure-` 或 `__Host-` 前缀的 cookie，只可以应用在使用了安全连接（HTTPS）的域中，需要同时设置 `secure` 属性。

另外，假如 cookie 以 `__Host-` 为前缀，那么 path 属性的值必须为 `/`（表示整个站点），且不能含有 `Domain` 属性。

```
// 当响应来自于一个安全域（HTTPS）的时候，二者都可以被客户端接受
Set-Cookie: __Secure-ID=123; Secure; Domain=example.com
Set-Cookie: __Host-ID=123; Secure; Path=/

// 缺少 Secure 指令，会被拒绝
Set-Cookie: __Secure-id=1

// 缺少 Path=/ 指令，会被拒绝
Set-Cookie: __Host-id=1; Secure

// 由于设置了 domain 属性，会被拒绝
Set-Cookie: __Host-id=1; Secure; Path=/; domain=example.com
```

# Session

Session是另一种记录客户状态的机制，不同的是Cookie保存在客户端浏览器中，而Session保存在服务器上。客户端浏览器访问服务器的时候，服务器把客户端信息以某种形式记录在服务器上。这就是Session。客户端浏览器再次访问时只需要从该Session中查找该客户的状态就可以了。

虽然Session保存在服务器，对客户端是透明的，它的正常运行仍然需要客户端浏览器的支持。这是因为Session需要使用Cookie作为识别标志。**HTTP协议是无状态的，Session不能依据HTTP连接来判断是否为同一客户，因此服务器向客户端浏览器发送一个名为JSESSIONID的Cookie，它的值为该Session的id（也就是HttpSession.getId()的返回值）。Session依据该Cookie来识别是否为同一用户。**

该Cookie为服务器自动生成的，它的maxAge属性一般为–1，表示仅当前浏览器内有效，并且各浏览器窗口间不共享，关闭浏览器就会失效。

## 生命周期

Session保存在服务器端。**为了获得更高的存取速度，服务器一般把Session放在内存里。每个用户都会有一个独立的Session。如果Session内容过于复杂，当大量客户访问服务器时可能会导致内存溢出。因此，Session里的信息应该尽量精简。**

**Session在用户第一次访问服务器的时候自动创建**。需要注意只有访问JSP、Servlet等程序时才会创建Session，只访问HTML、IMAGE等静态资源并不会创建Session。如果尚未生成Session，也可以使用request.getSession(true)强制生成Session。

**Session生成后，只要用户继续访问，服务器就会更新Session的最后访问时间，并维护该Session**。用户每访问服务器一次，无论是否读写Session，服务器都认为该用户的Session“活跃”了一次。

## 有效期

由于会有越来越多的用户访问服务器，因此Session也会越来越多。**为防止内存溢出，服务器会把长时间内没有活跃的Session从内存删除。这个时间就是Session的超时时间。如果超过了超时时间没访问过服务器，Session就自动失效了。**

Session的超时时间为maxInactiveInterval属性，可以通过对应的getMaxInactiveInterval()获取，通过setMaxInactiveInterval(longinterval)修改。

Session的超时时间也可以在web.xml中修改。另外，通过调用Session的invalidate()方法可以使Session失效。

## 和Cookie区别

1. cookie数据存放在客户的浏览器上，session数据放在服务器上
2. cookie不是很安全，别人可以分析存放在本地的cookie并进行cookie欺骗考虑到安全应当使用session。
3. 设置cookie时间可以使cookie过期。但是使用session-destory()，我们将会销毁会话。
4. session会在一定时间内保存在服务器上。当访问增多，会比较占用你服务器的性能考虑到减轻服务器性能方面，应当使用cookie。
5. 单个cookie保存的数据不能超过4K，很多浏览器都限制一个站点最多保存20个cookie。(Session对象没有对存储的数据量的限制，其中可以保存更为复杂的数据类型)

# localStorage

- 生命周期：持久化的本地存储，除非主动删除数据，否则数据是永远不会过期的
- 存储的信息在同一域中是共享的
- 当本页操作（新增、修改、删除）了`localStorage`的时候，本页面不会触发`storage`事件,但是别的页面会触发`storage`事件。
- 大小：5M（跟浏览器厂商有关系）
- `localStorage`本质上是对字符串的读取，如果存储内容多的话会消耗内存空间，会导致页面变卡
- 受同源策略的限制

## 使用

```js
// 设置
localStorage.setItem('username','cfangxu')
// 获取
localStorage.getItem('username')
// 获取键名
localStorage.key(0) //获取第一个键名
// 删除
localStorage.removeItem('username')
// 一次性清除所有存储
localStorage.clear()
```

`localStorage` 也不是完美的，它有两个缺点：

- 无法像`Cookie`一样设置过期时间
- 只能存入字符串，无法直接存对象

```js
localStorage.setItem('key', {name: 'value'});
console.log(localStorage.getItem('key')); // '[object, Object]'
```

# sessionStorage

`sessionStorage`和 `localStorage`使用方法基本一致，唯一不同的是生命周期，一旦页面（会话）关闭，`sessionStorage` 将会删除数据

# 区别

关于`cookie`、`sessionStorage`、`localStorage`三者的区别主要如下：

- 存储大小：`cookie`数据大小不能超过`4k`，`sessionStorage`和`localStorage`虽然也有存储大小的限制，但比`cookie`大得多，可以达到5M或更大
- 有效时间：`localStorage`存储持久数据，浏览器关闭后数据不丢失除非主动删除数据； `sessionStorage`数据在当前浏览器窗口关闭后自动删除；`cookie`设置的`cookie`过期时间之前一直有效，即使窗口或浏览器关闭
- 数据与服务器之间的交互方式，`cookie`的数据会自动的传递到服务器，服务器端也可以写`cookie`到客户端； `sessionStorage`和`localStorage`不会自动把数据发给服务器，仅在本地保存

# indexedDB

`indexedDB`是一种低级API，用于客户端存储大量结构化数据(包括, 文件/ blobs)。该API使用索引来实现对该数据的高性能搜索。

虽然 `Web Storage`对于存储较少量的数据很有用，但对于存储更大量的结构化数据来说，这种方法不太有用。`IndexedDB`提供了一个解决方案。

**优点：**

- 储存量理论上没有上限
- 所有操作都是异步的，相比 `LocalStorage` 同步操作性能更高，尤其是数据量较大时
- 原生支持储存`JS`的对象
- 是个正经的数据库，意味着数据库能干的事它都能干

**缺点：**

- 操作非常繁琐
- 本身有一定门槛

关于`indexedDB`的使用基本使用步骤如下：

- 打开数据库并且开始一个事务
- 创建一个 `object store`
- 构建一个请求来执行一些数据库操作，像增加或提取数据等。
- 通过监听正确类型的 `DOM` 事件以等待操作完成。
- 在操作结果上进行一些操作（可以在 `request`对象中找到）

关于使用`indexdb`的使用会比较繁琐，大家可以通过使用`Godb.js`库进行缓存，最大化的降低操作难度。

# 应用场景

在了解了上述的前端的缓存方式后，我们可以看看针对不对场景的使用选择：

- 标记用户与跟踪用户行为的情况，推荐使用`cookie`
- 适合长期保存在本地的数据（令牌），推荐使用`localStorage`
- 敏感账号一次性登录，推荐使用`sessionStorage`
- 存储大量数据的情况、在线文档（富文本编辑器）保存编辑历史的情况，推荐使用`indexedDB`
