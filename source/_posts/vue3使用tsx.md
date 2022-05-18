---
title: vue3使用tsx
date: 2022-04-29 19:29:20
categories: [vue3]
tags:
- vue3
- tsx
- vite
- jsx
---

# 技术栈

vue3+ts+vite

# 安装@vitejs/plugin-vue-jsx

```
npm i @vitejs/plugin-vue-jsx -D
```

# 使用

`vite.config.ts`中导入：

```typescript
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import vueJsx from '@vitejs/plugin-vue-jsx';

export default defineConfig({
  plugins: [vue(),vueJsx()]
})
```

修改`tsconfig.json`:（添加`jsxFactory`和`jsxFragmentFactory`）

```json
{
  "compilerOptions": {
    "jsx": "preserve",
    "jsxFactory": "h",
    "jsxFragmentFactory": "Fragment",
  },
}
```

# tsx

`src`目录内新建tsx文件：（如：`App.tsx`）

```tsx
const renderDom = () => {
  return (
      <div>
        hello,tsx!
      </div>
  )
}

export default renderDom
```

在.vue文件中导入并使用：

```vue
<template>
  <renderDom></renderDom>
</template>

<script setup lang="ts">
import renderDom from "./App.tsx"; //导入后当成组件使用即可
</script>

<style>

</style>
```

## 展示数据

变量写在单个花括号内，展示ref对象的值需要加上.value

```tsx
import {ref, Ref} from "vue";

const text: Ref<string> = ref('')

const renderDom = () => {
  return (
      <div>
        <input type="text" v-model={text.value}/>
        <span>{text.value}</span>
      </div>
  )
}

export default renderDom
```

## v-show和v-if

tsx支持v-show，不支持v-if

要想实现v-if的效果，需使用编程的方法

```tsx
let flag = false

const renderDom = () => {
  return (
      <div>
        { flag ? <div>罗小黑</div> : <div>罗小白</div>}
      </div>
  )
}

export default renderDom
```

## v-for

使用Array.map()

```tsx
const arr = [1,2,3,4]

const renderDom = () => {
  return (
      <div>
        { arr.map(item => {
          return <div>${item}</div>
        }) }
      </div>
  )
}

export default renderDom
```

## v-bind和v-on

tsx不支持v-bind和v-on

绑定数据直接使用即可

绑定事件使用onXXX（如onClick）即可，函数传参使用bind，不能使用事件修饰符（需自己使用js实现）

```tsx
const arr = [1, 2, 3, 4]

const clickEvent = (item: number) => {
  console.log(`点击了第${item}个`)
}
const renderDom = () => {
  return (
      <div>
        {arr.map(item => {
          return <div data-num={item} onClick={clickEvent.bind(this, item)}>${item}</div>
        })}
      </div>
  )
}

export default renderDom
```

## 接收props参数和emit

```vue
<template>
	<renderDom title="这是标题" @getNum="getNum"></renderDom>
</template>

<script setup lang="ts">
import renderDom from "./App.tsx"; //导入后当成组件使用即可
    
const getNum = (num: number) => {
  console.log(num)
}
</script>

<style>

</style>
```

```tsx
interface Props {
  title: string
}

const clickEvent = (ctx: any) => {
  ctx.emit('getNum', 20)
}

const renderDom = (props: Props, ctx: any) => {
  return (
      <div>
        <div>{props.title}</div>
        <button onClick={clickEvent.bind(this,ctx)}>按钮</button>
      </div>
  )
}

export default renderDom
```



[参考视频](https://www.bilibili.com/video/BV1dS4y1y7vd?p=36&t=503.3)
