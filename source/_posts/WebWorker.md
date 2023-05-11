---
title: WebWorker
date: 2022-11-02 21:15:46
categories: web
tags:
- worker
---

# Web Workers

通过使用 Web Workers，Web 应用程序可以在独立于主线程的后台线程中，运行一个脚本操作。这样做的好处是可以在独立线程中执行费时的处理任务，从而允许主线程（通常是 UI 线程）不会因此被阻塞/放慢。

它的作用就是给 JS 创造多线程运行环境，允许主线程创建worker线程，分配任务给后者，主线程运行的同时worker线程也在运行，相互不干扰，在worker线程运行结束后把结果返回给主线程。这样做的好处是主线程可以把计算密集型或高延迟的任务交给worker线程执行，这样主线程就会变得轻松，不会被阻塞或拖慢。这并不意味着 JS 语言本身支持了多线程能力，而是浏览器作为宿主环境提供了 JS 一个多线程运行的环境。

不过因为worker一旦新建，就会一直运行，不会被主线程的活动打断，这样有利于随时响应主线程的通性，但是也会造成资源的浪费，所以不应过度使用，用完需要关闭。

# 使用

使用构造函数（例如，`Worker()`）创建一个 **worker** 对象，构造函数接受一个 JavaScript 文件 URL，这个文件包含了将在 worker 线程中运行的代码。worker 将运行在与当前 `window`不同的另一个全局上下文中，这个上下文由一个对象表示，标准情况下为`DedicatedWorkerGlobalScope` （标准 workers 由单个脚本使用; 共享 workers 使用`SharedWorkerGlobalScope` ）。

主线程和 worker 线程相互之间使用 `postMessage()` 方法来发送信息，并且通过 `onmessage` 这个 event handler 来接收信息（传递的信息包含在 `message` 这个事件的`data`属性内) 。数据的交互方式为传递副本，而不是直接共享数据。

worker 可以另外生成新的 worker，这些 worker 与它们父页面的宿主相同。此外，worker 可以通过 `XMLHttpRequest` 来访问网络，只不过 `XMLHttpRequest` 的 `responseXML` 和 `channel` 这两个属性的值将总是 `null` 。

## 限制

你可以在 worker 线程中运行任意的代码，但注意存在一些例外：

1. 同源限制
   worker线程执行的脚本文件必须和主线程的脚本文件同源，这是当然的了，总不能允许worker线程到别人电脑上到处读文件吧
2. 文件限制
   为了安全，worker线程无法读取本地文件，它所加载的脚本必须来自网络，且需要与主线程的脚本同源
3. DOM操作限制
   worker线程在与主线程的window不同的另一个全局上下文中运行，其中无法读取主线程所在网页的DOM对象，也不能获取 `document`、`window`等对象，但是可以获取`navigator`、`location(只读)`、`XMLHttpRequest`、`setTimeout族`等浏览器`API`。
4. 通信限制
   worker线程与主线程不在同一个上下文，不能直接通信，需要通过`postMessage`方法来通信。
5. 脚本限制
   worker线程不能执行`alert`、`confirm`，但可以使用 `XMLHttpRequest` 对象发出`ajax`请求。

# Worker

在主线程中生成 Worker 线程很容易：

```js
var myWorker = new Worker(jsUrl, options)
```

Worker()构造函数，第一个参数是脚本的网址（必须遵守同源政策），该参数是必需的，且只能加载 JS 脚本，否则报错。第二个参数是配置对象，该对象可选。它的一个作用就是指定 Worker 的名称，用来区分多个 Worker 线程。

```js
// 主线程
var myWorker = new Worker('worker.js', { name : 'myWorker' });

// Worker 线程
self.name // myWorker
```

## 例子

首先是worker线程的js文件：

```js
// workerThread1.js

let i = 1

function simpleCount() {
  i++
  self.postMessage(i)
  setTimeout(simpleCount, 1000)
}

simpleCount()

self.onmessage = ev => {
  postMessage(ev.data + ' 呵呵~')
}
```

在HTML文件中的body中：

```html
<!--主线程，HTML文件的body标签中-->

<div>
  Worker 输出内容：<span id='app'></span>
  <input type='text' title='' id='msg'>
  <button onclick='sendMessage()'>发送</button>
  <button onclick='stopWorker()'>stop!</button>
</div>

<script type='text/javascript'>
  if (typeof(Worker) === 'undefined')	// 使用Worker前检查一下浏览器是否支持
    document.writeln(' Sorry! No Web Worker support.. ')
  else {
    window.w = new Worker('workerThread1.js')
    window.w.onmessage = ev => {
      document.getElementById('app').innerHTML = ev.data
    }
    
    window.w.onerror = err => {
      w.terminate()
      console.log(error.filename, error.lineno, error.message) // 发生错误的文件名、行号、错误内容
    }
    
    function sendMessage() {
      const msg = document.getElementById('msg')
      window.w.postMessage(msg.value)
    }
    
    function stopWorker() {
      window.w.terminate()
    }
  }
</script>
```

主线程中的`api`，`worker`表示是 Worker 的实例：

- `worker.postMessage`: 主线程往worker线程发消息，消息可以是任意类型数据，包括二进制数据
- `worker.terminate`: 主线程关闭worker线程
- `worker.onmessage`: 指定worker线程发消息时的回调，也可以通过`worker.addEventListener('message',cb)`的方式
- `worker.onerror`: 指定worker线程发生错误时的回调，也可以 `worker.addEventListener('error',cb)`

Worker线程中全局对象为 `self`，代表子线程自身，这时 `this`指向`self`，其上有一些`api`：

- `self.postMessage`: worker线程往主线程发消息，消息可以是任意类型数据，包括二进制数据
- `self.close`: worker线程关闭自己
- `self.onmessage`: 指定主线程发worker线程消息时的回调，也可以`self.addEventListener('message',cb)`
- `self.onerror`: 指定worker线程发生错误时的回调，也可以 `self.addEventListener('error',cb)`

worker线程中加载脚本的`api`：

```js
importScripts('script1.js')	// 加载单个脚本
importScripts('script1.js', 'script2.js')	// 加载多个脚本
```

# 应用

1. 加密数据
   有些加解密的算法比较复杂，或者在加解密很多数据的时候，这会非常耗费计算资源，导致UI线程无响应，因此这是使用Web Worker的好时机，使用Worker线程可以让用户更加无缝的操作UI。
2. 预取数据
   有时候为了提升数据加载速度，可以提前使用Worker线程获取数据，因为Worker线程是可以是用 `XMLHttpRequest` 的。
3. 预渲染
   在某些渲染场景下，比如渲染复杂的canvas的时候需要计算的效果比如反射、折射、光影、材料等，这些计算的逻辑可以使用Worker线程来执行，也可以使用多个Worker线程。
4. 复杂数据处理场景
   某些检索、排序、过滤、分析会非常耗费时间，这时可以使用Web Worker来进行，不占用主线程。
5. 预加载图片
   有时候一个页面有很多图片，或者有几个很大的图片的时候，如果业务限制不考虑懒加载，也可以使用Web Worker来加载图片。

注意

- 虽然使用worker线程不会占用主线程，但是启动worker会比较耗费资源
- 主线程中使用`XMLHttpRequest`在请求过程中浏览器另开了一个异步`http`请求线程，但是交互过程中还是要消耗主线程资源

# 其他worker

除了专用 worker 之外，还有一些其他种类的 worker：

- `Shared Workers` 可被不同的窗体的多个脚本运行，例如 `IFrames` 等，只要这些 workers 处于同一主域。共享 worker 比专用 worker 稍微复杂一点，脚本必须通过活动端口进行通讯。
- `Service Workers` 一般作为 web 应用程序、浏览器和网络（如果可用）之间的代理服务。他们旨在创建有效的离线体验，拦截网络请求，以及根据网络是否可用采取合适的行动，更新驻留在服务器上的资源。他们还将允许访问推送通知和后台同步 `API`。
- `音频 Workers`可以在网络 worker 上下文中直接完成脚本化音频处理。





参考：

1. [Web Workers API](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Workers_API)
2. [一文看懂 Web Worker](https://zhuanlan.zhihu.com/p/79484282)
