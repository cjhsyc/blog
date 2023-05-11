---
title: postMessage
date: 2022-11-04 12:20:05
categories: JavaScript
tags:
- postMessage
- worker
---

# window.postMessage

**`window.postMessage()`** 方法可以安全地实现跨源通信。通常，对于两个不同页面的脚本，只有当执行它们的页面位于具有相同的协议（通常为 `https`），端口号（443 为 `https` 的默认值），以及主机 (两个页面的模数 `Document.domain`设置为相同的值) 时，这两个脚本才能相互通信。**`window.postMessage()`** 方法提供了一种受控机制来规避此限制，只要正确的使用，这种方法就很安全。

```js
otherWindow.postMessage(message, targetOrigin, [transfer]);
```

- `otherWindow`

  其他窗口的一个引用，比如 `iframe` 的 `contentWindow` 属性、执行`window.open`返回的窗口对象、或者是命名过或数值索引的`window.frames` 。

- `message`

  将要发送到其他 window 的数据。它将会被 结构化克隆算法 序列化。这意味着你可以不受什么限制的将数据对象安全的传送给目标窗口而无需自己序列化。

- `targetOrigin`

  通过窗口的 origin 属性来指定哪些窗口能接收到消息事件，其值可以是字符串"*"（表示无限制）或者一个 URI。在发送消息的时候，如果目标窗口的协议、主机地址或端口这三者的任意一项不匹配 `targetOrigin` 提供的值，那么消息就不会被发送；只有三者完全匹配，消息才会被发送。**如果你明确的知道消息应该发送到哪个窗口，那么请始终提供一个有确切值的 `targetOrigin`，而不是 \*。不提供确切的目标将导致数据泄露到任何对数据感兴趣的恶意站点。**

- `transfer` 可选

  是一串和 message 同时传递的 `Transferable` 对象。这些对象的所有权将被转移给消息的接收方，而发送一方将不再保有所有权。

## onmessage

执行如下代码，其他 window 可以监听分发的 message:

```js
window.addEventListener("message", receiveMessage, false);

function receiveMessage(event)
{
  var origin = event.origin
  if (origin !== "http://example.org:8080")
    return;

  // ...
  
  // 作为回信的对象，并且把 event.origin 作为 targetOrigin
  event.source.postMessage("hello", event.origin);
}
```

`event`的属性有：

- `data`

  从其他 window 中传递过来的对象。

- `origin`

  调用 `postMessage` 时消息发送方窗口的 origin ，这个字符串由 协议、“://“、域名、“ : 端口号”拼接而成。请注意，这个 origin 不能保证是该窗口的当前或未来 origin，因为 `postMessage` 被调用后可能被导航到不同的位置。

- `source`

  对发送消息的窗口对象的引用; 您可以使用此来在具有不同 origin 的两个窗口之间建立双向通信。

注意：任何窗口可以在任何其他窗口访问此方法，在任何时间，无论文档在窗口中的位置，向其发送消息。因此，用于接收消息的任何事件监听器**必须**首先使用 origin 和 source 属性来检查消息的发送者的身份。 **这不能低估：无法检查 origin 和 source 属性会导致跨站点脚本攻击。**

# Worker.postMessage

[`Worker`](https://cjhsyc.github.io/2022/11/02/WebWorker/) 接口的 **`postMessage()`**方法向 worker 的内部作用域发送一个消息。这接受单个参数，这是要发送给 worker 的数据。数据可以是由 结构化克隆算法 处理的任何值或 JavaScript 对象，其包括循环引用。

```js
myWorker.postMessage(aMessage, transferList);
```

- `aMessage`：发送给 worker 的数据
- `transferList`：一个可选的`Transferable`对象的数组，用于传递所有权。如果一个对象的所有权被转移，在发送它的上下文中将变为不可用（中止），并且只有在它被发送到的 worker 中可用。



参考：

- [window.postMessage](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/postMessage)
- [Worker.postMessage()](https://developer.mozilla.org/zh-CN/docs/Web/API/Worker/postMessage)
