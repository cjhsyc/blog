---
title: vue3使用router
date: 2022-05-02 19:10:06
categories: vue
tags:
- router
- vue-router
- vue3
---

# 初始化vue3项目

```
npm init vite@latest
```

使用vue+ts

# vue-router基本使用

## 安装vue-router

```
npm i vue-router
```

## 基本使用

新建`src/index.ts`:

```ts
import {createRouter, createWebHashHistory, createWebHistory, RouteRecordRaw} from "vue-router";

const routes: RouteRecordRaw[] = [
  {
    path: '/',
    component: () => import('../components/Login.vue')
  },
  {
    path:'/reg',
    component: ()=> import('../components/Register.vue')
  }
]

export default createRouter({
  history: createWebHistory(), //路由模式
  routes
})
```

`main.ts`中导入：

```ts
import { createApp } from 'vue'
import App from './App.vue'
import Router from './router/index'

createApp(App).use(Router).mount('#app')
```

## router-view和router-link

`App.vue`中：

```vue
<template>
  <!--replace：不产生历史记录-->
  <router-link to="/" replace>登录</router-link>
  <router-link to="/reg">注册</router-link>
  <router-view></router-view>
</template>
```

## 编程式导航

```vue
<template>
  <router-link to="/">登录</router-link>
  <router-view></router-view>
  <button @click="goto">去注册</button>
</template>

<script setup lang="ts">
import {useRouter} from "vue-router";

const router = useRouter()

const goto = () => {
  router.push('/reg')
  // router.replace('/') //不产生历史记录
  // router.go(1) //前进
  // router.go(-1) //后退
  // router.back() //后退
}
</script>
```

路由传参和vue2中一样（query、params、动态路由参数）

传params参数必须使用命名路由，params参数刷新页面后丢失，动态路由参数和query不会

# 路由模式

vue2使用mode设置路由模式，vue3改为history

对应关系：

|  vue2   |         vue3         |
| :-----: | :------------------: |
| history |   createWebHistory   |
|  hash   | createWebHashHistory |
| abstact | createMemoryHistory  |

## hash原理

```js
//获取当前hash值
location.hash //输出：'#/'
//设置hash值
location.hash = '/reg' //或者'#/reg'
```

通过onhashchange监听hash的变化:

```js
window.addEventListener('hashchange',(event)=>{ //hash发生变化时触发
	console.log(event) //HashChangeEvent对象（包含很多属性，比如：newURL、oldURL等）
})
```

## history原理

通过onpopstate监听url的变化:

```js
window.addEventListener('popstate',(event)=>{ //路径发生变化时触发
	console.log(event) //PopStateEvent对象（其中的state属性就包含路径信息）
})
```

```js
history.state //{back: '/reg', current: '/', forward: '/reg', replaced: false, position: NaN, …}
history.pushState({},'','/reg') //此方法只会改变当前地址栏的路径，并不会更新页面内容
//需要在Javascript代码中调用history.back()或用户点击浏览器的回退按钮
```

# 命名视图和重定向

定义多个组件：

```ts
{
  path: '/',
  //重定向（可使用字符串、对象、函数三种写法）
  //redirect:'/user1',
  redirect: to => {
    return {
      path: 'user1'
    }
  }，
  //别名
  //alias:['user',...],
  children: [
    {
      path: 'user1',
      components:{
        default: () => import('../components/A.vue'),
      }
    },
    {
      path: 'user2',
      components:{
        bbb: () => import('../components/B.vue'),
        ccc: () => import('../components/C.vue')
      }
    }
  ],
}
```

```vue
<template>
  <!--加载default-->
  <router-view></router-view>
  <!--加载对应的name-->
  <router-view name='bbb'></router-view>
  <router-view name='ccc'></router-view>
</template>
```

# 路由过渡动画

安装animate.css：`npm install animate.css --save`

设置路由元信息（meta），准备好animate.css的类名

```ts
{
  path: '/',
  component: () => import('../components/Login.vue')，
  meta: {
    transition: 'animate__bounceIn'
  }
},
{
  path:'/reg',
  component: ()=> import('../components/Register.vue')，
  meta: {
    transition: 'animate__fadeIn'
  }
}
```

使用：

```vue
<template>
  <router-link to="/">登录</router-link>
  <router-link to="/reg">注册</router-link>
  <router-view #default="{route,Component}">
    <transition :enter-active-class="`animate__animated ${route.meta.transition}`">
      <component :is="Component"></component>
    </transition>
  </router-view>
</template>

<script setup lang="ts">
import 'animate.css'
</script>
```

router-view： 可以使用作用域插槽的写法

transition：定义过度动画

component（动态组件）：通过is属性指定组件

# 滚动行为

scrollBehavior：创建Router时可定义的方法（返回值为滚动条的位置）

```ts
export default createRouter({
  history: createWebHistory(),
  //保存滚动条的位置
  scrollBehavior: (to,from,savedPosition)=>{
    if(savedPosition){
      return savedPosition
    }else {
      return {
        top:0
      }
    }
  },
  routes
})
```
