---
title: axios
date: 2022-04-08 20:36:15
categories: [axios]
tags: 
- axios
- xhr
- ajax
---

# axios基本使用

安装：

```
npm install axios
```

发送请求：

```js
axios({        
  url:'xxx',    // 设置请求的地址
  method:"GET", // 设置请求方法
  params:{      // get请求使用params进行参数凭借,如果是post请求用data
    type: '',
    page: 1
  }
}).then(res => {  
  // res为后端返回的数据
  console.log(res);   
})
```

# 二次封装

```js
const requests = axios.create({
    //配置对象
    baseURL: process.env.VUE_APP_BASEURL,//基础路径，自动加在端口后
    timeout: 5000,//超时的时间
    headers:{...}//通用请求头
})
```

## 请求拦截器

```js
requests.interceptors.request.use(
  config => {
    if (store.state.user.token) {
        config.headers.token = store.state.user.token
    }
    return config
  },
  error => {
    return Promise.error(error)
  })
```

## 响应拦截器

```js
requests.interceptors.response.use((res) => {
    //成功的回调
    return res.data//返回数据部分
}, (err) => {
    //失败的回调
    return Promise.reject(new Error('失败'))
})
```

# 取消请求

方式一：

```js
const CancelToken = axios.CancelToken;
const source = CancelToken.source();
 
axios.post(url, {
    data
}, {
    cancelToken: source.token
})
// 取消请求 (请求原因是可选的)
source.cancel('主动取消请求');
```

方式二：

```js
const CancelToken = axios.CancelToken;
let cancel;

axios.get(url, {
  cancelToken: new CancelToken(function executor(c) {
    cancel = c;
  })
});
cancel('主动取消请求');
```

# 简易原理

```js
class Axios {
    constructor() {

    }

    request(config) {
        return new Promise(resolve => {
            const {url = '', method = 'get', data = {}} = config;
            // 发送ajax请求
            const xhr = new XMLHttpRequest();
            //参数3：是否为异步请求，默认为true
            xhr.open(method, url, true);
            xhr.onload = function() {
                console.log(xhr.responseText)
                resolve(xhr.responseText);
            }
            xhr.send(data);
        })
    }
}

//生成axios实例
function CreateAxiosFn() {
    let axios = new Axios();
    let req = axios.request.bind(axios);
    return req;
}

// 得到最后的全局变量axios
let axios = CreateAxiosFn();
```
