---
title: 手写vue-lazyload
date: 2022-04-03 18:35:16
categories: 
- [手写]
- [vue]
tags: 
- vue
- vue-lazyload
- 图片懒加载
- 插件
- 自定义指令
---

# vue-lazyload

`main.js`中引入vue-lazyload：

```js
import Vue from 'vue'
import App from './App.vue'

import VueLazyload from "vue-lazyload";

Vue.use(VueLazyload,{
  loading:'http://localhost:3000/img/loading.gif', //图片加载中显示的图片
  error:'http://localhost:3000/img/error-img.png', //图片加载错误显示的图片
  preLoad:1 //超出1倍屏幕高度的图片先不加载
})
```

.vue文件中使用：

使用指令`v-lazy`代替img标签的src属性，表示该图片使用懒加载

```vue
<template>
  <div id="app">
    <div v-for="(item,index) in imgData" :key="index">
      <div class="img">
        <img v-lazy="item.img" alt="img">
      </div>
      <div class="content">{{item.name}}</div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'App',
  data() {
    return {
      imgData: []
    }
  },
  mounted() {
    this.getImgs()
  },
  methods: {
    async getImgs() {
      const res = await axios.get('http://localhost:3000/imgs')
      this.imgData = res.data
    }
  }
}
</script>
```

# 自定义指令实现v-lazy

## 手写插件vue-lazyload

新建文件`modules/vue-lazyload/index.js`:

```js
export default { //默认暴露一个带有install方法的对象
  install(Vue,options){ //Vue：Vue构造器，options：使用插件时传入的配置对象
    
  }
}
```

`main.js`中引入自己的插件：

```js
import VueLazyload from "./modules/vue-lazyload";
```

## 自定义指令

```js
export default {
  install(Vue, options) {
    //自定义指令,参数一：自定义指令名，参数二：定义该指令功能的对象
    Vue.directive('lazy', {
      //指令定义对象可以调用一些钩子函数：比如bind、inserted
      //钩子函数的参数，el：指令所绑定的元素，binding：一个对象，vnode：虚拟节点
      //bind：只调用一次，指令第一次绑定到元素时调用
      bind(el,binding,vnode){

      }
    })
  }
}
```

## 功能实现的类

接下来就需要在bind钩子函数中实现功能逻辑，为了更好的扩展性，将功能封装成一个类：

创建`modules/vue-lazyload/lazy.js`：

```js
export default function (Vue) { //暴露一个函数，接收Vue构造器
  return class Lazy { //返回一个类，接收options
    constructor(options) {
      this.options = options
    }

      //实现功能的函数
    bindLazy(el, binding) {

    }
  }
}
```

`modules/vue-lazyload/index.js`:

```js
import lazy from './lazy' //导入lazy.js

export default {
  install(Vue, options) {
    const LazyClass = lazy(Vue)
    const lazyload = new LazyClass(options)
    Vue.directive('lazy', {
      bind: lazyload.bindLazy.bind(lazyload) //绑定函数，注意修改this指向
    })
  }
}
```

准备一个函数，用于获取dom的最近的滚动父节点（overflow：scroll）

创建`modules/vue-lazyload/util.js`:

```js
export function getScrollParent(el) {
  let _parent = el.parentNode
  while (_parent) {
      //getComputedStyle：获取目标的所有css属性
    const overflow = getComputedStyle(_parent)['overflow'] //获取overflow属性值
    if (/(scroll)|(auto)/.test(overflow)) {
      return _parent
    }
    _parent = _parent.parentNode
  }
}
```

`lazy.js`中使用Vue.nextTick:

```js
import {getScrollParent} from './util'

export default function (Vue) {
  return class Lazy {
    constructor(options) {
      this.options = options
      this.isAddScrollListener = false 
    }

    bindLazy(el, binding) {
      Vue.nextTick(() => {
        const scrollParent = getScrollParent(el)
        if (scrollParent && !this.isAddScrollListener) { //如果还没有绑定事件
          scrollParent.addEventListener('scroll',this.handleScroll.bind(this))
        }
      })
    }

    //滚动事件
    handleScroll(){

    }
  }
}
```

## 图片实例的类

创建`modules/vue-lazyload/lazyimg.js`:

```js
export default class Lazyimg {
  constructor({el, src, options, imgRender}) {
    this.el = el
    this.src = src
    this.options = options
    this.imgRender = imgRender
    this.loaded = false //已经加载过
    this.state = {
      loading: false, //加载成功
      error: false //加载失败
    }
  }
i
  //图片是否在指定范围内
  checkIsVisible() {
    const {top} = this.el.getBoundingClientRect() //获取元素距顶部的距离
    return top < window.innerHeight * (this.options.preLoad || 1.3) //判断是否在范围内，preLoad默认1.3
  }

  //加载图片（未完成，第二个参数先写死成loading，参数一为该图片实例）
  loadImg() {
    this.imgRender(this,'loading') //加载图片，图片加载中时显示的图片
  }
}
```

`lazy.js`:每次触发bind时创建一个图片实例，保存到数组

```js
import {getScrollParent} from './util'
import {throttle} from 'lodash' //节流
import Lazyimg from "./lazyimg";

export default function (Vue) {
  return class Lazy {
    constructor(options) {
      this.options = options
      this.isAddScrollListener = false
      this.lazyimgPool = [] //图片实例数组
    }

    bindLazy(el, binding) {
      Vue.nextTick(() => {
        const scrollParent = getScrollParent(el)
        if (scrollParent && !this.isAddScrollListener) {
          scrollParent.addEventListener('scroll', throttle(this.handleScroll.bind(this), 200))
          this.isAddScrollListener = true
        }
          //创建一个新的图片实例
        const lazyimg = new Lazyimg({ 
          el,
          src: binding.value, //v-lazy指令绑定的值
          options: this.options,
          imgRender: this.imgRender.bind(this)
        })
        this.lazyimgPool.push(lazyimg)
        this.handleScroll() //滚动事件在一开始就执行一次
      })
    }

    //滚动事件
    handleScroll() {

    }

    //图片渲染函数
    imgRender() {

    }
  }
}
```

## 滚动事件

```js
handleScroll() {
  let isVisible = false
  this.lazyimgPool.forEach(lazyimg => {
    if (!lazyimg.loaded) { //图片还没有加载
      isVisible = lazyimg.checkIsVisible() //图片是否出现在指定的范围内（perLoad指定的）
      isVisible && lazyimg.loadImg() //如果出现在范围内，则加载图片
    }
  })
}
```

## 渲染图片

```js
imgRender(lazyimg, state) {
  const {el, options} = lazyimg
  const {loading, error} = options
  let src = ''
  switch (state) {
    case 'loading': //加载中
      src = loading || ''
      break
    case 'error': //加载错误
      src = error || ''
      break
    default: //加载完成，显示真正的目标图片
      src = lazyimg.src
  }
  el.setAttribute('src',src) //设置或改变图片的src
}
```

## 渲染完成

`util.js`中添加：

```js
export function imgLoad(src) {
  return new Promise(((resolve, reject) => {
    const oImg = new Image()
    oImg.src = src
    oImg.onload = resolve //加载成功
    oImg.onerror = reject //加载失败
  }))
}
```

`lazy.js`:

```js
loadImg() {
  this.imgRender(this, 'loading')
  imgLoad(this.src).then(() => {//成功
    this.state.loading = true
    this.imgRender(this, 'ok') 
    this.loaded = true
  }).catch(() => {//失败
    this.state.error = true
    this.imgRender(this, 'error')
    this.loaded = true
  })
}
```
