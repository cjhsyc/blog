---
title: less预处理语言
date: 2022-03-08 13:50:07
tags: less
categories: [样式,less]
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

```css
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

## 使用函数

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

## 命名空间和逻辑判断

```less
#card{
    //when:进行逻辑判断
    //not:取反,and:且,或运算使用逗号
    .border(@width,@color,@style) when not(@width > 30px) , (@color >= #666) and (@style = solid){
        border: @width @style @color;
    }
}

#main {
    //调用其他作用域的函数（ '>' 可以省略 ）
    #card > .border(40px, #666, solid); //符合条件才生效
    //#card.border(40px, #666, solid);//效果同上
}
```

## 不定参数

```less
.boxShadow(@a,@b,...){//不定参数
    box-shadow: @arguments;//@arguments:所有的参数,包括@a,@b
    width: 200px;
}
#box1{
    .boxShadow(0,0,10px,gray);
}
#box2{
    .boxShadow(inset,0,0,5px,gray);
}
```

## 循环

```less
//循环（递归调用函数）
.columns(@n,@i:1) when (@i <= @n) {
    .column-@{i} {
        width: @i*100% / @n;
        height: 20px;
        background-color: pink;
    }
    .columns(@n, @i+1)
}

.columns(4);
```

# 属性合并

```less
.bg{
    width: 200px;
    height: 200px;
    //+ :属性用逗号隔开
    //+_ :属性用空格隔开
    background+: #666666;
    background+_: url("@{url}");
    background+_: no-repeat;
    background+_: center;
    box-shadow+: 0 0 5px greenyellow;
    box-shadow+: 0 0 10px #000;
    background-size: 100px;
}
```

相当于

```css
.bg {
  width: 200px;
  height: 200px;
  background: #666666 url("@{url}") no-repeat center;
  box-shadow: 0 0 5px greenyellow,0 0 10px #000;
  background-size: 100px;
}
```

# 继承

```less
.linkBtn {
    display: block;
    width: 200px;
    height: 80px;
}

.linkBtn {
    color: white;
    background-color: skyblue;
}

.link:extend(.linkBtn) {
    text-decoration: none;
    border: 4px solid orange;
}
```

使用混合也可以实现相同的效果，但混合是相当于把代码再复制一份，而继承不是，上述代码转CSS如下：

```css
.linkBtn,
.link {
  display: block;
  width: 200px;
  height: 80px;
}
.linkBtn,
.link {
  color: white;
  background-color: skyblue;
}
.link {
  text-decoration: none;
  border: 4px solid orange;
}
```

# 导入

导入其他less文件

```less
@import "./assets/style.less"
@import (reference) "./assets/style.less" //添加reference后，未被调用的代码不加载
```



[参考视频](https://www.bilibili.com/video/BV1jM4y1c778?t=1.1)



