---
title: 古典密码Vigenere算法
date: 2022-04-08 13:59:03
categories: 
- [信息安全,古典密码]
tags:
- [Vigenere]
- [Vue]
- [JavaScript]
---

代码如下（html文件）：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
  <script src="https://cdn.jsdelivr.net/npm/vue@2.6.14/dist/vue.js"></script>
  <style>
    #root {
      width: 60%;
      margin: auto;
    }

    .header {
      width: 360px;
      margin: auto;
    }

    .main {
      display: flex;
      justify-content: left;
      padding-top: 20px;
    }

    .area {
      width: 40%;
      padding-right: 80px;
    }

    .area textarea {
      width: 100%;
      height: 120px;
    }
  </style>
</head>
<body>
<div id="root">
  <div class="header">
    <input type="text" placeholder="请输入密钥" v-model.lazy.trim="key" @blur="setKey">
    <button @click="encryption">加密</button>
    <button @click="decrypt">解密</button>
  </div>
  <div class="main">
    <div class="area">
      <textarea placeholder="请输入明文/密文" v-model.lazy.trim="put"></textarea>
    </div>
    <div class="area">
      <textarea placeholder="加密/解密的结果" v-model.lazy.trim="out"></textarea>
    </div>
  </div>
</div>
<script>
  Vue.config.productionTip = false;   //阻止 vue 在启动时生成生产提示

  new Vue({
    el: "#root",
    data: {
      key: '',
      put: '',//输入
      out: '',//输出
    },
    computed: {
      table() {
        const obj = {}
        let c1 = '', c2 = '', r = ''
        for (let i = 97; i < 123; i++) {
          c1 = String.fromCharCode(i)
          obj[c1] = {}
          for (let n = 97; n < 123; n++) {
            let res = n + i - 97
            c2 = String.fromCharCode(n)
            r = String.fromCharCode(res > 122 ? res - 26 : res)
            obj[c1][c2] = r
          }
        }
        return obj
      }
    },
    methods: {
      setKey() {
        if (!/^[a-z|A-Z]+$/.test(this.key)) {
          this.key = ''
        }
      },
      //加密按钮回调
      encryption() {
        let {put, key} = this
        key = key.toLowerCase()
        let out = []
        let n = 0
        const l = key.length - 1
        if (put && key) {
          for (let i = 0; i < put.length; i++) {
            if (/[a-z]/.test(put[i])) {
              out.push(this.table[key[n]][put[i]])
            } else if (/[A-Z]/.test(put[i])) {
              let char = this.table[key[n]][put[i].toLowerCase()]
              out.push(char.toUpperCase())
            } else {
              out.push(put[i])
            }
            n === l ? n = 0 : n++
          }
          this.out = out.join('')
        }
      },
      //解密按钮回调
      decrypt() {
        let {put, key} = this
        key = key.toLowerCase()
        let out = []
        let n = 0
        const l = key.length - 1
        if (put && key) {
          for (let i = 0; i < put.length; i++) {
            if (/[a-z]/.test(put[i])) {
              out.push(this.decryptUtil(key[n], put[i]))
            } else if (/[A-Z]/.test(put[i])) {
              let char = this.decryptUtil(key[n], put[i].toLowerCase())
              out.push(char.toUpperCase())
            } else {
              out.push(put[i])
            }
            n === l ? n = 0 : n++
          }
          this.out = out.join('')
        }
      },
      //解密
      decryptUtil(key, value) {
        for (let i = 97; i < 123; i++) {
          if (this.table[key][String.fromCharCode(i)] === value) {
            return String.fromCharCode(i)
          }
        }
      }
    }
  });
</script>
</body>
</html>
```
