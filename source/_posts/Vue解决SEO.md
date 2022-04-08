---
title: Vue解决SEO
date: 2022-03-27 11:46:01
categories: 
- [服务端渲染,NUXT]
- [SEO]
tags:
- Vue
- SEO
- Nuxt
---

# SEO：搜索引擎优化

seo需要的几个关键点：

1. 多页面
2. title、描述、关键词
3. 网站的内容

vue-cli无法实现以上三点：

1. vue项目是SPA：单页面应用
2. vue项目只有一套title、描述、关键词
3. vue项目的内容是通过引入js文件加载的，无法通过源代码的HTML文件中直接读取

# 预渲染

在html页面生成之前数据就已经加载完成。预渲染的流程：

- 读取配置，获取需要预渲染的页面

- 发布机模拟浏览器环境打开页面
- 页面脚本触发渲染时机
- 渲染出当前的页面内容
- 获取当前所有的DOM结构
- 生成HTML文件

## prerender-spa-plugin

提供一个预渲染解决方案。

安装插件prerender-spa-plugin：

```
npm i prerender-spa-plugin
```

在vue.config.js中进行配置：

```js
const path = require('path')
const PrerenderSPAPlugin = require('prerender-spa-plugin')
 
module.exports = {
	publicPath:'./',
	configureWebpack:{
    	plugins: [
	        new PrerenderSPAPlugin({
          		staticDir: path.join(__dirname, 'dist'),
          		//将指定的路由分别预渲染为HTML页面(打包后生成多个HTML页面)
         		routes: [ '/', '/about', '/some/deep/nested/route' ],
        	})
      	]
	} 
}
```

## vue-meta-info

修改title、描述、关键词。

```
npm i vue-meta-info
```

在路由对应 的Vue文件中设置对应的meta-info：

```vue
<template>
    ...
</template>
 
<script>
  export default {
    metaInfo: {
      title: 'My Example App', // 设置title
      meta: [{                 
        name: 'keyWords',//关键字
        content: 'My Example App'//描述
      }]
      /*link: [{//设置link
        rel: 'asstes',
        href: 'https://assets-cdn.github.com/'
      }]*/
    }
  }
</script> 
```

## 存在的问题

prerender-spa-plugin无法配置动态路由，适合一个项目只需要其中几个页面需要做seo。

vue-meta-info无法给title、描述和关键字设置动态数据。

# 服务器渲染（SSR）

在客户端和后端之间再添加一个服务器端（比如node.js）,后端将返回的数据交给服务器端，服务器端将html返回给客户端。

## NUXT

[NUXT中文官网](https://www.nuxtjs.cn/)

一个基于 [Vue.js](https://vuejs.org/) 的服务端渲染应用框架

安装:

```
npx create-nuxt-app <项目名>
```

安装时需要进行一些选择

### 目录结构

`pages`：文件夹中放置路由组件。在该文件夹下的组件会在.nuxt文件夹下的`router.js`中自动配置路由。

`components`：文件夹中放置全局组件，使用其中的组件时无需import，可直接使用。因为配置文件`nuxt.config.js`配置文件中的components: true（改为false后需要手动引入组件）

`store`：`store` 目录用于组织应用的 [Vuex 状态树](http://vuex.vuejs.org/) 文件。 Nuxt.js 框架集成了 [Vuex 状态树](http://vuex.vuejs.org/) 的相关功能配置，在 `store` 目录下创建一个 `index.js` 文件可激活这些配置。

### NUXT生命周期

#### 服务端生命周期

##### nuxtServerInit

在`store` 目录下新建`index.js`。在vuex的actions模块中使用nuxtServerInit

```js
export const state = () => {
}
export const mutations = {}
export const actions = {
  //参数1：vuex上下文
  //参数2：nuxt上下文
  //只能在index.js中使用，其他的js文件会被当成子模块，子模块中使用nuxtServerInit无效
  nuxtServerInit(store, context) {
    console.log('nuxtServerInit')
  }
}
```

##### middleware（中间件）

根目录下新建`middleware`目录，添加js文件（name.js）:

```js
export default ({store,route,req,res,redirect,query,params}) => {//可接收很多参数
  console.log('middleware')
    if(route.name='home'){//前往home路由
        redirect('/login')//重定向到login路由
    }
}
```

全局middleware：`nuxt.config.js`中添加：

```js
  router:{
    middleware:'name',//对应middleware目录下的js文件名
  }
```

局部（组件内）middleware：

```vue
<template>
  <Tutorial/>
</template>

<script>
export default {
  name: 'IndexPage',
  // middleware:'name',
  middleware(){//不需要在middleware目录下写js文件
    console.log('局部middleware')
  }
}
</script>

```

##### validate

用于校验路由参数

在组件中使用：

```vue
<template>
  <Tutorial/>
</template>

<script>
export default {
  name: 'IndexPage',
  validate({params,query}){//接收路由的参数
    console.log('validate')
    return /^\d+$/.test(query.id)//返回值为true时才能正常访问
  }
}
</script>
```

##### asyncData

只能在页面组件中使用，在组件每次加载之前调用，一般用来发送请求、获取数据。

##### fetch

可以在所有组件中使用

#### 服务端和客户端共有的生命周期

##### beforeCreate/created

在NUXT服务器和客户端都执行

#### 客户端生命周期

和vue中一致：

- beforeMount/mounted

- beforeUpdate/updated

- beforeDestroy/destroyed

### NUXT路由

#### nuxt-link

`<nuxt-link>` 的作用和`<router-link>`一致。为了提高 Nuxt.js 应用程序的响应能力，当链接将显示在视口中时，Nuxt.js 将自动预获取代码分割页面。

nuxt中不使用路由懒加载。

#### 子路由

在`pages`目录下新一个和父路由同名的文件夹，该文件夹下的.vue文件为子路由

`<nuxt-child>`相当于`<router-view>`

#### 动态路由

.vue文件以下划线（'_'）开头的为动态路由

#### 手动配置router.js

使用@nuxtjs/router

```
npm i @nuxtjs/router
```

`nuxt.config.js`中配置：

```js
modules:[
	'@nuxtjs/router'
]
```

在根目录下创建`router.js`文件，手动配置路由，与vue中不同的是向外暴露的不是router实例而是一个函数

```js
import Vue from 'vue'
import Router from 'vue-router'
Vue.use(Router)

import MyPage from '~/components/my-page'
const routes = [{
        path: '/',
        component: MyPage
      }]

export function createRouter() {
  return new Router({
    mode: 'history',
    routes
  })
}
```

### 路由导航守卫

在router.js中使用和vue-cli中一样 (全局守卫，路由独享守卫)

nuxtjs自动生成路由时使用：

1. 中间件：middleware

2. 配置插件

   根目录新建`plugins/router.js`

   ```js
   export default ({app}) => {
     app.router.beforeEach((to,from,next)=>{
       console.log('beforeEach')
       next()
     })
   }
   ```

   `nuxt.config.js`中设置：

   ```js
   plugins:[
   	'@/plugins/router.js'
   ]
   ```

#### 注意

导航守卫在服务器端就已经执行，无法获取localStorage和cookie

解决方法：安装cookie-universal-nuxt

```
npm i cookie-universal-nuxt
```

nuxt.config.js中:

```
modules:[
	'cookie-universal-nuxt',
]
```

即可正常使用，如：

- this.$cookies.set
- this.$cookies.get

### 配置（nuxt.config.js）

#### head

head用于配置title、描述、关键字：

```js
head: {
  title: 'app',
  htmlAttrs: {
    lang: 'en'
  },
  meta: [
    { charset: 'utf-8' },//网页编码
    { name: 'viewport', content: 'width=device-width, initial-scale=1' },
    { hid: 'description', name: 'description', content: '' },
    { name: 'format-detection', content: 'telephone=no' }
  ],
  link: [
    { rel: 'icon', type: 'image/x-icon', href: '/favicon.ico' }//网页图标
  ]
},
```

如果当前页面的.vue文件没有单独配置head，则使用nuxt.config.js中的head

在每个页面中单独配置：（使用函数的形式）

```vue
<template>
  <div>about</div>
</template>

<script>
export default {
  name: "About",
  head() {
    return {
      title: 'about',//可以设置为动态数据
      meta: [
        {hid: 'keywords',name:'keywords',content: '网站关键字'},
        {hid: 'description', name: 'description', content: '网站描述'},
      ]
    }
  }
}
</script>
```

#### css

配置全局的css文件：（比如：初始化css、UI组件库的css文件）

```js
css: [
  '@/static/reset.css'//static下准备样式初始化文件
],
```

#### plugins

配置全局js文件：（比如：axios二次封装、UI组件库）

```js
plugins: [
  '@/plugins/router.js'//plugins文件夹下准备全局js文件
],
```

#### modules

modules是Nuxt.js扩展

nuxt中安装axios(方式一)：

```
npm i @nuxtjs/axios
```

modules中配置：

```js
modeules:[
	'@nuxtjs/axios'
]
```

方式二：

```
npm i axios
```

由于nuxt项目中没有main.js文件，使用时需要在.vue文件中单独import。

这里使用方式一：

在pages下 的页面组件中使用sayncData发送请求：

```vue
<template>
  <div>首页</div>
</template>

<script>
export default {
  name: 'IndexPage',
  data(){
    return{
      list:[]  
    }
  },
  async asyncData({$axios}){//接收一个参数是页面组件上下文
    const res = await $axios.get({url:'http://xxxxxxxxxxxxx'})
    return{//asyncData中的this为undefined，需使用return返回数据，返回的数据会合并到组件的data中
      list:res.data
    }
  }
}
</script>
```

在components下的组件使用fetch发送请求获取数据：

```vue
<template>
  <div>news</div>
</template>

<script>
export default {
  name: "News",
  data() {
    return {
      list: []
    }
  },
  async fetch() {//也接收一个参数是页面组件上下文，components下的组件接收不到
      //fetch函数的this是组件实例，该函数不能直接将数据返回给页面组件，所有可以在components下的组件使用
      //如果要在页面组件中使用需要将数据传到vuex中
    const res = await this.$axios.get({url: 'http://xxxxxxxxxxxxx'})
    this.list = res.data
  }
}
</script>
```

#### 配置代理

安装aixos和proxy：

```
npm i @nuxtjs/axios
npm i @nuxtjs/proxy
```

配置：

```js
modules: [
  '@nuxtjs/axios',
  '@nuxtjs/proxy',
],
axios: {
  proxy: true,//是否可以跨域
  baseUrl: 'xxxxxxx',
  retry: {retries: 3},//超时重试次数
},
proxy: {//配置代理
  '/api': {
    target: 'http://localhost:8080',
    pathRewrite: {
      '^/api': ''
    }
  }
},
```

#### axios二次封装

新建`plugins/axios,js`:

```js
export default ({$axios}) => {
  $axios.defaults.timeout = 10000//超时时间
  //请求拦截器
  $axios.onRequest(config => {
    console.log(config)
  })
  //错误拦截器
  $axios.onError((error => {
    console.log(error)
  }))
  //响应拦截器
  $axios.onResponse(response => {
    return response.data
  })
}
```

配置:

```js
plugins: [
  '@/plugins/axios.js',
],
```

#### loading (加载进度条)

配置关闭loading：

```js
loading:false,//默认为true
```

配置样式：

```js
loading:{
	color:'blue',
	height:'5px'
}
```

### Vuex状态树

新建`store/index.js`：

```js
export const state = () => {//state使用函数，避免返回引用
}
export const mutations = {}
export const actions = {}
export const getters = {}
```

模块：

新建js文件，模块名就是文件名

### nuxt项目上线

1. 打包

   ```
   npm run build
   ```

2. 将`.nuxt`、`static`、`nuxt.config.js`、`package.json`四个文件放到服务器中，服务器下载node环境。

3. 服务器安装依赖并启动（依旧是localhost:3000）

   ```
   npm install
   npm run start
   ```

4. nginx代理



[参考视频](https://www.bilibili.com/video/BV1GT4y1X7aC?p=2&t=614.3)
