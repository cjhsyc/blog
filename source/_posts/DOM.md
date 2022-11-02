---
title: DOM
date: 2022-10-24 18:59:41
categories: JavaScript
tags:
- DOM
- document
---

# DOM

文档对象模型 (DOM) 是 HTML 和 XML 文档的编程接口。它提供了对文档的结构化的表述，并定义了一种方式可以使从程序中对该结构进行访问，从而改变文档的结构，样式和内容。DOM 将文档解析为一个由节点和对象（包含属性和方法的对象）组成的结构集合。简言之，它会将 web 页面和脚本或程序语言连接起来。

一个 web 页面是一个文档。这个文档可以在浏览器窗口或作为 HTML 源码显示出来。但上述两个情况中都是同一份文档。文档对象模型（DOM）提供了对同一份文档的另一种表现，存储和操作的方式。DOM 是 web 页面的完全的面向对象表述，它能够使用如 JavaScript 等脚本语言进行修改。

# 常用API

## document

### getElementById

```js
var element = document.getElementById(id);
```

- **`element`**是一个 `Element` 对象。如果当前文档中拥有特定 ID 的元素不存在则返回 null。
- **`id`**是大小写敏感的字符串，代表了所要查找的元素的唯一 ID。

### getElementsByTagName

```js
var elements = document.getElementsByTagName(name);
```

- `elements` 是一个由发现的元素出现在树中的顺序构成的动态的 HTML 集合 `HTMLCollection` (但是，这个方法在 WebKit 内核的浏览器中返回一个 `NodeList` ） 。
- `name` 是一个代表元素的名称的字符串。特殊字符 "*" 代表了所有元素。

### createElement

```js
var element = document.createElement(tagName[, options]);
```

- `tagName` 指定要创建元素类型的字符串。
- `options` 一个可选的参数`ElementCreationOptions` 是包含一个属性名为 `is` 的对象，该对象的值是用 `customElements.define()` 方法定义过的一个自定义元素的标签名。
- 返回值：新建的元素（`Element`）。

## Node

### appendChild

```js
element.appendChild(aChild)
```

- `aChild`：要追加给父节点（通常为一个元素）的节点。
- 返回值：返回追加后的子节点（`aChild`），除非 `aChild` 是一个文档片段（`DocumentFragment`），这种情况下将返回空文档片段（`DocumentFragment`）。

### cloneNode

```js
var dupNode = node.cloneNode(deep);
```

- `node`：将要被克隆的节点
- `dupNode`：克隆生成的副本节点
- `deep` ：可选，是否采用深度克隆，如果为 `true`，则该节点的所有后代节点也都会被克隆，如果为 `false`，则只克隆该节点本身。在最新的规范里，其默认值为 false。

### innerText

表示一个节点及其后代的“渲染”文本内容。

```js
var renderedText = HTMLElement.innerText; // 获取的文本有层次结构
HTMLElement.innerText = string;
```

只展示给人看的元素（不包括 `script` 和 `style` 元素）；受 CSS 样式的影响，并且不会返回隐藏元素的文本。

修改`innerText` 会删除它的所有子节点，替换为给定的文本节点，`\n`被替换为 `<br>`元素。

### textContent

属性表示一个节点及其后代的文本内容。

可获取所有元素的内容，包括 `script` 和 `style` 元素

```js
let text = someNode.textContent; // 获取的文本没有层次结构
someOtherNode.textContent = string;
```

在节点上设置 `textContent` 属性的话，会删除它的所有子节点，并替换为一个具有给定值的文本节点。

使用 `textContent` 可以防止 XSS 攻击。

## Element

### innerHTML

设置或获取 HTML 语法表示的元素的后代。

```js
const content = element.innerHTML;
element.innerHTML = htmlString;
```

### setAttribute

```js
element.setAttribute(name, value);
```

- `name`：表示属性名称的字符串。
- `value`：属性的值/新值。

### getAttribute

```js
let attribute = element.getAttribute(attributeName);
```

- `attribute` 是一个包含 `attributeName` 属性值的字符串。
- `attributeName` 是你想要获取的属性值的属性名称。

### [addEventListener](https://cjhsyc.github.io/2022/10/24/%E4%BA%8B%E4%BB%B6%E7%9B%91%E5%90%AC%E5%99%A8/)

## window

### onload

**`load`** 事件在整个页面及所有依赖资源如样式表和图片都已完成加载时触发。它与 `DOMContentLoaded` 不同，后者只要页面 DOM 加载完成就触发，无需等待依赖资源的加载。

该事件不可取消，也不会冒泡。

当页面完全加载后在控制台打印一段信息：

```js
window.addEventListener('load', (event) => {
  console.log('page is fully loaded');
});
```

也可以使用 `onload` 事件处理器属性实现：

```js
window.onload = (event) => {
  console.log('page is fully loaded');
};
```
