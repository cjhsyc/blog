---
title: nextTick原理
date: 2022-10-30 16:33:01
categories: vue
tags:
- nextTick
---

# NextTick

官方定义：在下次 DOM 更新循环结束之后执行延迟回调。在修改数据之后立即使用这个方法，获取更新后的 DOM。

我们可以理解成，`Vue` 在更新 `DOM` 时是异步执行的。当数据发生变化，`Vue`将开启一个异步更新队列，视图需要等队列中所有数据变化完成之后，再统一进行更新。

```js
{{num}}
for(let i=0; i<100000; i++){
    num = i
}
```

如果没有 `nextTick` 更新机制，那么 `num` 每次更新值都会触发视图更新(上面这段代码也就是会更新10万次视图)，有了`nextTick`机制，只需要更新一次，所以`nextTick`本质是一种优化策略

# 使用

```typescript
// 类型
function nextTick(callback?: () => void): Promise<void>
```

你可以传递一个回调函数作为参数，或者 await 返回的 Promise。

```vue
<script setup>
import { ref, nextTick } from 'vue'

const count = ref(0)

async function increment() {
  count.value++

  // DOM 还未更新
  console.log(document.getElementById('counter').textContent) // 0

  await nextTick()
  // DOM 此时已经更新
  console.log(document.getElementById('counter').textContent) // 1
}
</script>

<template>
  <button id="counter" @click="increment">{{ count }}</button>
</template>
```

# 原理

`Vue`在更新`dom`时是异步执行的。只要监听到数据变化，`Vue`将开启一个队列，并缓冲在同一事件循环中发生的所有数据变更。如果同一个`watcher`被多次触发，只会被推入到队列中一次。这种在缓存时去重对于避免不必要的计算和dom操作是非常重要的。`nextTick`方法会在队列中加入一个回调函数，确保该函数在前面的`dom`操作完成后才调用。 

`nextTick` 主要使用了宏任务和微任务。根据执行环境分别尝试采用：

- `Promise`
- `MutationObserver`：微任务，创建并返回一个新的 `MutationObserver` 它会在指定的 DOM 发生变化时被调用
- `setImmediate`：宏任务，可以用来替代 `setTimeout(fn, 0)`
- 如果以上都不行则采用 `setTimeout`

过程：

1. 把回调函数放入callbacks等待执行
2. 根据执行环境，将执行函数放到微任务或者宏任务中
3. 事件循环到了微任务或者宏任务，执行函数依次执行callbacks中的回调
