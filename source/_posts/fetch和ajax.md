---
title: fetch和ajax
date: 2022-11-09 16:46:42
categories: JavaScript
tags:
- fetch
- ajax
- axios
---

# Ajax

英文全称为 `Asynchronous JavaScript + XML` ，翻译过来就是**异步JavaScript和XML**。

Ajax 是一个**概念模型**，是一个囊括了众多现有技术的**集合**，并不具体代指某项技术。

它是用来描述一种使用现有技术集合的“新”方法的，这里的“新”方法主要涉及到:  HTML 或 XHTML、CSS、 JavaScript、DOM、XML、XSLT，以及最重要的 XMLHttpRequest。

当使用结合了这些技术的 AJAX 模型以后， 网页应用能够快速地将增量更新呈现在用户界面上，而不需要重载（刷新）整个页面。这使得程序能够更快地回应用户的操作。

Ajax 最重要的特性就是可以**局部刷新页面**。

# Axios

Axios 是一个基于 Promise 网络请求库，作用于 Node.js 和浏览器中。 它是 isomorphic 的(即同一套代码可以运行在浏览器和 Node.js中)。在服务端它使用原生 Node.js http 模块，而在客户端则使用 XMLHttpRequest。

客户端 Axios 的主要特性有：

- 从浏览器创建 XMLHttpRequests
- 支持 Promise API
- 拦截请求和响应
- 转换请求和响应数据
- 取消请求
- 自动转换JSON数据
- 客户端支持防御XSRF

# Fetch

Fetch 提供了一个获取资源的接口（包括跨域请求）。

Fetch 是一个现代的概念, 等同于 XMLHttpRequest。它提供了许多与 XMLHttpRequest 相同的功能，但被设计成**更具可扩展性和高效性**。

Fetch 的核心在于**对 HTTP 接口的抽象**，包括 Request、Response、Headers 和 Body，以及用于初始化异步请求的 `global fetch`。得益于 JavaScript 实现的这些抽象好的 HTTP 模块，其他接口能够很方便的使用这些功能。

除此之外，Fetch 还利用到了请求的异步特性——它是基于 Promise 的。

`fetch() ` 方法必须接受一个参数——资源的路径。无论请求成功与否，它都返回一个 Promise 对象，resolve 对应请求的 Response。

# 总结

Ajax 是一种代表异步 JavaScript + XML 的模型（技术合集），所以 Fetch 也是 Ajax 的一个子集

在之前，我们常说的 Ajax 默认是指以 XHR 为核心的技术合集，而在有了 Fetch 之后，Ajax 不再单单指 XHR 了，我们将以 XHR 为核心的 Ajax 技术称作**传统 Ajax**。

Axios 属于传统 Ajax（XHR）的子集，因为它是基于 XHR 进行的封装。



参考：

[有同学问我：Fetch 和 Ajax 有什么区别？](https://juejin.cn/post/6997784981816737800)
