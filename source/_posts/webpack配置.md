---
title: webpack配置
date: 2022-03-07 23:19:37
tags: webpack
categories: 
    [工程化]
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

无需打包，通过浏览器访问8080端口实时查看页面效果

## 资源模块

### Resource资源

加载资源导出url

```javascript
module:{
    rules: [
        {
            test:/\.png$/,//匹配.png文件
            type: "asset/resource",//加载资源导出url
            generator: {//指定打包的路径（可省略）
                filename: 'images/[contenthash][ext]'//[contenthash]:哈希值作为文件名，[ext]:扩展名
            }
        }
    ]
}
```

示例：在index.js中导入png图片

```javascript
import imgsrc from '../asset/微信.png'//图片url(http://localhost:8080/images/a2769eaec65049f8919b.png)

const img = document.createElement('img')
img.src = imgsrc
document.body.appendChild(img)
```

也可再output中使用assetModuleFilename指定打包路径，优先级低于rules中的配置

```javascript
output: {//出口
    filename: "bundle.js",//出口文件名
    path: path.resolve(__dirname, "./dist"),//出口文件路径需要绝对路径
    clean: true,//打包前清理dist文件夹
    assetModuleFilename: "images/[contenthash][ext]",//[contenthash]:哈希值作为文件名，[ext]:扩展名
},
```

### inline资源

导出为Data URL base64格式

```javascript
{
    test: /\.svg$/,
    type: "asset/inline",//导出为Data URL base64格式
},
```

示例：导入svg图片

```javascript
import logoSvg from './asset/logo.svg'//(data:image/svg+xml;base64,PHN2ZyB4bWxucz......)

const img2 = document.createElement('img')
img2.src = logoSvg
img2.style.cssText='width:400px;height:600px'
document.body.appendChild(img2)
```

### source资源

导出文件源码

```javascript
{
    test: /\.txt$/,
    type: "asset/source",//导出文件源码
}
```

示例：导入源码

```javascript
import helloTxt from './asset/hello.txt'//txt的文本内容

const block = document.createElement('div')
block.style.cssText = 'width:200px;height:200px;border:1px solid gray'
block.textContent = helloTxt
document.body.appendChild(block)
```

### 通用资源类型

自动选择资源类型，小于maxSize用inline类型，大于则用resource类型

```javascript
{
    test: /\.jpg$/,
    type: "asset",//自动选择资源类型，小于maxSize用inline类型，大于则用resource类型
    parser: {//自定义条件（可省略）
        dataUrlCondition:{
            maxSize: 4 * 1024//默认为4 * 1024
        }
    }
}
```

示例：导入jpg图片

```javascript
import jpgMap from './asset/头像.jpg'//因为大于4k所以是url格式

const block = document.createElement('div')
block.style.cssText = 'width:200px;height:200px;border:1px solid gray'
block.textContent = helloTxt
document.body.appendChild(block)
```

## loader

### 加载CSS

安装loader

```
npm i css-loader -D
npm i style-loader -D
```

在module > rules中配置

```javascript
{
    test: /\.css$/,
    //css-loader写在style-loader后面，先加载
    //如果需要CSS预处理语言，安装相应的loader，写在css-loader后面
    use: ['style-loader','css-loader'],//css-loader允许打包css文件，style-loader将样式加到html页面上
}
```

### 抽离和压缩CSS

安装插件

```
npm i mini-css-extract-plugin -D
npm i css-minimizer-webpack-plugin -D
```

引入插件

```javascript
const MiniCssExtractPlugin = require('mini-css-extract-plugin')
const CssMinimizerPlugin = require('css-minimizer-webpack-plugin')
```

在plugin中使用MiniCssExtractPlugin（用于以link标签的形式在html中导入css文件）

```javascript
new MiniCssExtractPlugin({
	filename:'styles/[contenthash].css'//自定义打包路径
})
```

```javascript
{
    test: /\.css$/,
    //'style-loader'换为MiniCssExtractPlugin
    use: [MiniCssExtractPlugin.loader,'css-loader'],//css-loader允许打包css文件，style-loader将样式加到html页面上
}
```

新配置项optimization中使用CssMinimizerPlugin（用于压缩css文件）

```
optimization: {
    minimizer: [
        new CssMinimizerPlugin()
    ]
},
mode:'production'//生产环境下才会压缩（会有其他报错）
```

### babel-loader

如果浏览器不支持ES6语法，使用babel-loader，将ES6转为ES5

安装babel-loader，@babel/core，@babel/preset-env

```
npm i babel-loader @babel/core @babel/preset-env -D
npm i @babel/runtime -D
npm i @babel/plugin-transform-runtime -D
```

module > rules中配置

```javascript
{
    test:/\.js$/,
    exclude: /node_modules/,//排除node_modules中的js文件
    use:{
        loader:'babel-loader',
        options: {
            presets:['@babel/preset-env'],
            plugins: [
                [
                    '@babel/plugin-transform-runtime'
                ]
            ]
        }
    }
}
```

## 代码分离

### 多入口

entry中设置多入口文件，修改output中的filename

```javascript
entry: {
    //两个文件都引入了lodash模块
    index: './src/index.js',
    another: './src/another.js'
},//打包的入口文件
output: {//出口
    filename: "[name].bundle.js",//出口文件名（[name]:入口文件的键值）
    path: path.resolve(__dirname, "./dist"),//出口文件路径需要绝对路径
    clean: true,//打包前清理dist文件夹
    assetModuleFilename: "images/[contenthash][ext]",//[contenthash]:哈希值作为文件名，[ext]:扩展名
},
```

### 防止重复

```javascript
optimization: {
    splitChunks: {
        chunks: "all"//自动抽离公共代码模块
    }
}
```
