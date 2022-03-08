---
title: less预处理语言
date: 2022-03-08 13:50:07
tags: less
---

# 变量

## 声明和使用

```less
//定义变量
@color: red;
@var: a;
@attr: color;
@{var} { //作为选择器或者属性名需要添加大括号
    @{attr}: @color; //作为属性值直接使用
}

@btn: {
    width: 100px;
    height: 40px;
    background-color: aqua;
}

.btn {
    @btn(); //需要括号
}
    
@url: 'https://cdn.jsdelivr.net/gh/cjhsyc/image-hosting@master/头像.677yiujw5980.webp';
.img{
    background-image: url("@{url}"); //作为url使用
    background-size: 100px;
    width: 100px;
    height: 100px;
}
```

## 运算

可以使用+、-、*、/进行运算

```less
@a: 100px + 20px; //120px
@b: @a*2; //240px
@c: #666/2; //#333
```

# 嵌套

```less
.parent{
    width: 100px;
    height: 100px;
    border: 1px solid gray;
    .child{
        background-color: skyblue;
        @media (min-width: 900px){
            background-color: pink;
        }
        @media (min-width: 500px) and (max-width: 900px){
            background-color: greenyellow;
        }
    }
}
```

相当于

```less
.parent {
  width: 100px;
  height: 100px;
  border: 1px solid gray;
}
.parent .child {
  background-color: skyblue;
}
@media (min-width: 900px) {
    .parent .child {
        background-color: pink;
    }
}
@media (min-width: 500px) and (max-width: 900px) {
    .parent .child {
        background-color: greenyellow;
    }
}
```

# 混合

```less
.mixins {
    border: 2px solid skyblue;
    width: 300px;
    height: 30px;
}

.box {
    .mixins(); //括号可加可不加
}
```

# 函数

自定义一个绘制三角形的函数

```less
.triangle(top,@color:black,@height:50px) {
    border-color: transparent transparent @color transparent;
}

.triangle(left,@color:black,@height:50px) {
    border-color: transparent @color transparent transparent;
}

.triangle(bottom,@color:black,@height:50px) {
    border-color: @color transparent transparent transparent;
}

.triangle(right,@color:black,@height:50px) {
    border-color: transparent transparent transparent @color;
}

.triangle(@dir,@color:black,@height:50px) {//@dir用来匹配第一个参数，后面的参数都要一致
    width: 0;
    height: 0;
    border-width: @height;
    border-style: solid;
}

.triangleBox {
    .triangle(top,red);//绘制尖朝上的三角形,后两个参数不写则使用默认值
}
```

