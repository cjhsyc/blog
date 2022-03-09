---
title: js严格模式
date: 2022-03-09 13:51:37
tags: JavaScript
categories: [JavaScript]
---

# 严格模式

## 使用严格模式

```javascript
"use strict"
```

## 变量

```javascript
a = 10
console.log(a)//严格模式报错（必须使用var等声明变量）
```

## 静默失败 => 报错

```javascript
var b = 2
console.log(Object.getOwnPropertyDescriptor(window, 'b'))
/*configurable: false //var声明的变量默认不可删除
enumerable: true
value: 2
writable: true*/
delete b//未删除，非严格模式下不报错，程序继续进行（静默失败）
//严格模式下报错
console.log(b)//2
```

## 函数

### 参数唯一

```javascript
function test(a,a){
    console.log(a)//undefined(第二个a),严格模式报错
}
test(10)
```

### 实参和形参的映射关系不存在

```javascript
function test1(a) {
    a = 20
    console.log(arguments[0])//非严格模式:20,严格模式:10
}

test1(10)

function test2(a = 20) {//参数设置初始后自动开启严格模式
    a = 30
    console.log(arguments[0])//10
}

test2(10)
```

### 函数的this默认指向undefined

```javascript
function test(){
    console.log(this)//undefined
}
test()
```

### 不能使用arguments.callee和caller

```javascript
    function test(){
        console.log(arguments.callee === test)//true,严格模式无法使用
        console.log(test.caller === out)//true,严格模式无法使用
    }
    function out(){
        test()
    }
    out()
```

## 不能使用eval()和with()

```javascript
eval('var a=2')
console.log(a)//2,严格模式无法使用

const obj={
    a:3
}
function test(){
    with (obj) {//改变this指向
        console.log(a)//3,严格模式无法使用
    }
}
test()
```

## eval和arguments不能作为标识符

```javascript
let eval='111'//严格模式报错
```







