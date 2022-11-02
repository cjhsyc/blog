---
title: js事件循环
date: 2022-10-23 15:32:27
categories: JavaScript
tags:
- EventLoop
---

# 事件循环

JavaScript 有一个基于**事件循环**的并发模型，事件循环负责执行代码、收集和处理事件以及执行队列中的子任务。这个模型与其它语言中的模型截然不同，比如 C 和 Java。

## 栈

函数调用形成了一个由若干帧组成的栈。

```js
function foo(b) {
  let a = 10;
  return a + b + 11;
}

function bar(x) {
  let y = 3;
  return foo(x * y);
}

console.log(bar(7)); // 返回 42
```

当调用 `bar` 时，第一个帧被创建并压入栈中，帧中包含了 `bar` 的参数和局部变量。当 `bar` 调用 `foo` 时，第二个帧被创建并被压入栈中，放在第一个帧之上，帧中包含 `foo` 的参数和局部变量。当 `foo` 执行完毕然后返回时，第二个帧就被弹出栈（剩下 `bar` 函数的调用帧）。当 `bar` 也执行完毕然后返回时，第一个帧也被弹出，栈就被清空了。

## 堆

对象被分配在堆中，堆是一个用来表示一大块（通常是非结构化的）内存区域的计算机术语。

## 队列

一个 JavaScript 运行时包含了一个待处理消息的消息队列。每一个消息都关联着一个用以处理这个消息的回调函数。

在 事件循环 期间的某个时刻，运行时会从最先进入队列的消息开始处理队列中的消息。被处理的消息会被移出队列，并作为输入参数来调用与之关联的函数。正如前面所提到的，调用一个函数总是会为其创造一个新的栈帧。

函数的处理会一直进行到执行栈再次为空为止；然后事件循环将会处理队列中的下一个消息（如果还有的话）。

# 同步任务和异步任务

在`JavaScript`中，所有的任务都可以分为

- 同步任务：立即执行的任务，同步任务一般会直接进入到主线程中执行
- 异步任务：异步执行的任务，比如`ajax`网络请求，`setTimeout`定时函数等

同步任务进入主线程，即主执行栈，异步任务进入任务队列，主线程内的任务执行完毕为空，会去任务队列读取对应的任务，推入主线程执行。上述过程的不断重复就事件循环。

# 宏任务与微任务

异步任务还可以细分为微任务与宏任务

## 微任务

一个需要异步执行的函数，执行时机是在主函数执行结束之后、当前宏任务结束之前

常见的微任务有：

- `Promise.then`
- `MutaionObserver`
- `Object.observe`（已废弃；`Proxy` 对象替代）
- `process.nextTick`（`Node.js`）

## 宏任务

宏任务的时间粒度比较大，执行的时间间隔是不能精确控制的，对一些高实时性的需求就不太符合

常见的宏任务有：

- `script` （整体代码）
- `setTimeout/setInterval`
- `UI rendering/UI`事件
- `postMessage`、`MessageChannel`
- `setImmediate`、`I/O`（`Node.js`）
- `requestAnimationFrame` （浏览器独有）

### 宏任务`setTimeout`的误区

`setTimeout`的回调不一定在指定时间后能执行。而是在指定时间后，将回调函数放入事件循环的队列中。

如果时间到了，JS引擎还在执行同步任务，这个回调函数需要等待；如果当前事件循环的队列里还有其他回调，需要等其他回调执行完。

另外，`setTimeout` 0ms 也不是立刻执行，它有一个默认最小时间，为4ms。

所以下面这段代码的输出结果不一定：

```js
// node
setTimeout(() => {
  console.log('setTimeout')
}, 0)
setImmediate(() => {
  console.log('setImmediate')
})
```

因为取出第一个宏任务之前在执行全局Script，如果这个时间大于 4ms，这时 `setTimeout` 的回调函数已经放入队列，就先执行 `setTimeout`；如果准备时间小于 4ms，就会先执行 `setImmediate`。

# 执行机制

- 首先，事件循环从宏任务队列开始。此时，宏任务队列中，只有一个 script (整体代码)任务
- 同步任务直接进入主线程中执行
- 异步任务则判断为宏任务还是微任务，放入对应的任务队列中
- 一旦执行栈中的所有同步任务执行完毕，查询微任务队列，取出一个任务，进入执行栈执行（从第二步开始重复上述步骤）
- 微任务队列清空完毕后，渲染并且更新界面
- 处理worker相关的任务
- 开启下一轮的事件循环，查询宏任务队列，加载下一个宏任务

每轮循环都是由一个宏任务+多个微任务组成。

# 为什么分宏任务与微任务

微任务是线程之间的切换，速度快。不用进行上下文切换，可以快速的一次性做完所有的微任务。

宏任务是进程之间的切换，速度慢，且每次执行需要切换上下文。因此一个`Eventloop`中只执行一个宏任务。

事件循环中的任务被分为宏任务和微任务，是为了给高优先级任务一个插队的机会：微任务比宏任务有更高优先级。由于微任务执行快，一次性可以执行很多个，在当前宏任务执行后立刻清空微任务可以达到伪同步的效果，这对视图渲染效果起到至关重要的作用。

# 例子

```js
async function async1() {
  console.log('async1 start')
  await async2()
  console.log('async1 end')
}
async function async2() {
  console.log('async2')
}
console.log('script start')
setTimeout(function () {
  console.log('settimeout')
})
async1()
new Promise(function (resolve) {
  console.log('promise1')
  resolve()
}).then(function () {
  console.log('promise2')
})
console.log('script end')
```

分析过程：

1. 执行整段代码，遇到 `console.log('script start')` 直接打印结果，输出 `script start`
2. 遇到定时器了，它是宏任务，先放着不执行
3. 遇到 `async1()`，执行 `async1` 函数，先打印 `async1 start`，下面遇到`await`怎么办？先执行 `async2`，打印 `async2`，然后阻塞下面代码（即加入微任务列表），跳出去执行同步代码
4. 跳到 `new Promise` 这里，直接执行，打印 `promise1`，下面遇到 `.then()`，它是微任务，放到微任务列表等待执行
5. 最后一行直接打印 `script end`，现在同步代码执行完了，开始执行微任务，即 `await`下面的代码，打印 `async1 end`
6. 继续执行下一个微任务，即执行 `then` 的回调，打印 `promise2`
7. 上一个宏任务所有事都做完了，开始下一个宏任务，就是定时器，打印 `settimeout`

所以最后的结果是：`script start`、`async1 start`、`async2`、`promise1`、`script end`、`async1 end`、`promise2`、`settimeout`
