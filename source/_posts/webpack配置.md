---
title: webpack配置
date: 2022-03-07 23:19:37
tags: webpack
---
# webpack.config.js

## 入口和出口文件

```javascript
const path = require('path')//引入path模块
module.exports = {
    entry: './src/index.js',//打包的入口文件
    output: {//出口
        filename: "bundle.js",//出口文件名
        path: path.resolve(__dirname, "./dist"),//出口文件路径需要绝对路径
        clean: true,//打包前清理dist文件夹
    }
}
```

## 生成HTML文件

安装html-webpack-plugin插件

```
npm i html-webpack-plugin -D
```

该模块用于自动生成HTML文件。在webpack.config.js中引入：

```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin'); //通过 npm 安装
```

通过plugins配置插件：

```javascript
plugins: [
    new HtmlWebpackPlugin({//可不传配置项（默认配置）
        template: "./index.html",//以index.html为模板生成html文件
        filename: "app.html",
        inject: 'body',//指定script标签写在body标签中（默认为head）
    })
]
```

代码有修改后自动打包：

```
webpack --watch
```

## 开发环境

安装webpack-dev-server插件

```
npm i webpack-dev-server -D
```

```javascript
//开发模式
mode: "development",
devtool: 'inline-source-map',//精确显示代码位置（比如报错时）
devServer: {//开发服务器
    static:'./dist'
},
```

命令行执行：

```
webpack-dev-server
```

无需打包，通过http查看页面效果

