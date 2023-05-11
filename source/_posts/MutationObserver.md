---
title: MutationObserver
date: 2022-11-02 19:40:33
categories: JavaScript
tags:
- MutationObserver
---

# MutationObserver

`MutationObserver` 接口提供了监视对 DOM 树所做更改的能力。DOM 的任何变动，比如节点的增减、属性的变动、文本内容的变动，这个 API 都可以得到通知。

概念上，它很接近事件，可以理解为 DOM 发生变动就会触发 Mutation Observer 事件。但是，它与事件有一个本质不同：事件是同步触发，也就是说，DOM 的变动立刻会触发相应的事件；Mutation Observer 则是异步触发，DOM 的变动并不会马上触发，而是要等到当前所有 DOM 操作都结束才触发。

`MutationObserver` 有以下特点：

- 它等待所有脚本任务完成后，才会运行（即异步触发方式）。
- 它把 DOM 变动记录封装成一个数组进行处理，而不是一条条个别处理 DOM 变动。
- 它既可以观察 DOM 的所有类型变动，也可以指定只观察某一类变动。

# 构造函数

创建并返回一个新的 `MutationObserver` 它会在指定的 DOM 发生变化时被调用。

```js
var observer = new MutationObserver(callback);
```

`callback`：一个回调函数，每当被指定的节点或子树以及配置项有 DOM 变动时会被调用。回调函数拥有两个参数：一个是描述所有被触发改动的 `MutationRecord` 对象数组，另一个是调用该函数的 `MutationObserver` 对象。

## 使用

```js
function callback(mutationList, observer) {
  mutationList.forEach((mutation) => {
    switch(mutation.type) {
      case 'childList':
        /* 从树上添加或移除了一个或更多的子节点；参见 mutation.addedNodes 与
           mutation.removedNodes */
        break;
      case 'attributes':
        /* mutation.target 中某节点的一个属性值被更改；该属性名称在 mutation.attributeName 中，
           该属性之前的值为 mutation.oldValue */
        break;
    }
  });
}

var targetNode = document.querySelector("#someElement");
var observerOptions = {
  childList: true,  // 观察目标子节点的变化，是否有添加或者删除
  attributes: true, // 观察属性变动
  subtree: true     // 观察后代节点，默认为 false
}

var observer = new MutationObserver(callback);
observer.observe(targetNode, observerOptions);
```

使用 ID `someElement` 来获取目标节点树。`observerOptions` 中设定了观察者的选项，通过设定 `childList` 和 `attributes` 为 `true` 来获取所需信息。

当 observer 实例化时，指定 `callback()` 函数。之后指定目标节点与记录选项，我们开始观察使用 `observe()` 指定的 DOM 节点。

从现在开始直到调用 `disconnect()` ，每次以 `targetNode` 为根节点的 DOM 树添加或移除元素时，以及这些元素的任意属性改变时，`callback()` 都会被调用。

# 实例方法

## observe

`MutationObserver` 的 **`observe()`** 方法配置了 `MutationObserver` 对象的回调方法以开始接收与给定选项匹配的 DOM 变化的通知。根据配置，观察者会观察 DOM 树中的单个 `Node`，也可能会观察被指定节点的部分或者所有的子孙节点。

```js
mutationObserver.observe(target[, options])
```

`target`：DOM 树中的一个要观察变化的 DOM `Node` (可能是一个 `Element`)，或者是被观察的子节点树的根节点。

`options`：此对象的配置项描述了 DOM 的哪些变化应该报告给 `MutationObserver` 的 `callback`。当调用 `observe()` 时， `childList`、`attributes` 和 `characterData` 中，必须有一个参数为 `true`。否则会抛出 `TypeError` 异常。

`options` 的属性如下：

- `subtree` 可选

  当为 `true` 时，将会监听以 `target` 为根节点的整个子树。包括子树中所有节点的属性，而不仅仅是针对 `target`。默认值为 `false`。

- `childList` 可选

  当为 `true` 时，监听 `target` 节点中发生的节点的新增与删除（同时，如果 `subtree` 为 `true`，会针对整个子树生效）。默认值为 `false`。

- `attributes` 可选

  当为 `true` 时观察所有监听的节点属性值的变化。默认值为 `true`，当声明了 `attributeFilter` 或 `attributeOldValue`，默认值则为 `false`。

- `attributeFilter` 可选

  一个用于声明哪些属性名会被监听的数组。如果不声明该属性，所有属性的变化都将触发通知。

- `attributeOldValue` 可选

  当为 `true` 时，记录上一次被监听的节点的属性变化。默认值为 `false`。

- `characterData` 可选

  当为 `true` 时，监听声明的 `target` 节点上所有字符的变化。默认值为 `true`，如果声明了 `characterDataOldValue`，默认值则为 `false`

- `characterDataOldValue` 可选

  当为 `true` 时，记录前一个被监听的节点中发生的文本变化。默认值为 `false`

## disconnect

`MutationObserver` 的 **`disconnect()`** 方法告诉观察者停止观察变动。可以通过调用其 `observe()` 方法来重用观察者。

```js
mutationObserver.disconnect()
```

## takeRecords

`MutationObserver` 的 **`takeRecords()`** 方法返回已检测到但尚未由观察者的回调函数处理的所有匹配 DOM 更改的列表，使变更队列保持为空。此方法最常见的使用场景是在断开观察者之前立即获取所有未处理的更改记录，以便在停止观察者时可以处理任何未处理的更改。

```js
mutationRecords = mutationObserver.takeRecords()
```

返回一个`MutationRecord` 对象列表，每个对象都描述了应用于 DOM 树某部分的一次改动。

`MutationRecord`对象包含了DOM的相关信息，有如下属性：

- `type`：观察的变动类型（`attribute`、`characterData`或者`childList`）。
- `target`：发生变动的DOM节点。
- `addedNodes`：新增的DOM节点。
- `removedNodes`：删除的DOM节点。
- `previousSibling`：前一个同级节点，如果没有则返回`null`。
- `nextSibling`：下一个同级节点，如果没有则返回`null`。
- `attributeName`：发生变动的属性。如果设置了`attributeFilter`，则只返回预先指定的属性。
- `oldValue`：变动前的值。这个属性只对`attribute`和`characterData`变动有效，如果发生`childList`变动，则返回`null`。
