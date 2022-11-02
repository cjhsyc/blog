---
title: js循环
date: 2022-10-24 18:51:31
categories: JavaScript
tags:
---

# 遍历器

JavaScript 原有的表示“集合”的数据结构，主要是数组（`Array`）和对象（`Object`），ES6 又添加了`Map`和`Set`。这样就有了四种数据集合，用户还可以组合使用它们，定义自己的数据结构，比如数组的成员是`Map`，`Map`的成员是对象。这样就需要一种统一的接口机制，来处理所有不同的数据结构。

遍历器（Iterator）就是这样一种机制。它是一种接口，为各种不同的数据结构提供统一的访问机制。任何数据结构只要部署 Iterator 接口，就可以完成遍历操作（即依次处理该数据结构的所有成员）。

Iterator 的作用有三个：一是为各种数据结构，提供一个统一的、简便的访问接口；二是使得数据结构的成员能够按某种次序排列；三是 ES6 创造了一种新的遍历命令`for...of`循环，Iterator 接口主要供`for...of`消费。

Iterator 的遍历过程是这样的:

1. 创建一个指针对象，指向当前数据结构的起始位置。也就是说，遍历器对象本质上，就是一个指针对象。

2. 第一次调用指针对象的`next`方法，可以将指针指向数据结构的第一个成员。

3. 第二次调用指针对象的`next`方法，指针就指向数据结构的第二个成员。

4. 不断调用指针对象的`next`方法，直到它指向数据结构的结束位置。

每一次调用`next`方法，都会返回数据结构的当前成员的信息。具体来说，就是返回一个包含`value`和`done`两个属性的对象。其中，`value`属性是当前成员的值，`done`属性是一个布尔值，表示遍历是否结束。

## Symbol.iterator

`ES6` 规定，默认的 Iterator 接口部署在数据结构的`Symbol.iterator`属性，或者说，一个数据结构只要具有`Symbol.iterator`属性，就可以认为是“可遍历的”（`iterable`）。`Symbol.iterator`属性本身是一个函数，就是当前数据结构默认的遍历器生成函数。执行这个函数，就会返回一个遍历器。至于属性名`Symbol.iterator`，它是一个表达式，返回`Symbol`对象的`iterator`属性，这是一个预定义好的、类型为 Symbol 的特殊值，所以要放在方括号内。

原生具备 Iterator 接口的数据结构如下。

- Array
- Map
- Set
- String
- TypedArray
- 函数的 arguments 对象
- NodeList 对象

对象（Object）之所以没有默认部署 Iterator 接口，是因为对象的哪个属性先遍历，哪个属性后遍历是不确定的，需要开发者手动指定。本质上，遍历器是一种线性处理，对于任何非线性的数据结构，部署遍历器接口，就等于部署一种线性转换。

```javascript
class RangeIterator {
  constructor(start, stop) {
    this.value = start;
    this.stop = stop;
  }

  [Symbol.iterator]() { return this; }

  next() {
    var value = this.value;
    if (value < this.stop) {
      this.value++;
      return {done: false, value: value};
    }
    return {done: true, value: undefined};
  }
}

function range(start, stop) {
  return new RangeIterator(start, stop);
}

for (var value of range(0, 3)) {
  console.log(value); // 0, 1, 2
}
```

上面代码是一个类部署 Iterator 接口的写法。`Symbol.iterator`属性对应一个函数，执行后返回当前对象的遍历器对象。

有一些场合会默认调用 Iterator 接口（即`Symbol.iterator`方法），除了下文会介绍的`for...of`循环，还有几个别的场合:

- **解构赋值**

  ```javascript
  let set = new Set().add('a').add('b').add('c');
  
  let [x,y] = set;
  // x='a'; y='b'
  
  let [first, ...rest] = set;
  // first='a'; rest=['b','c'];
  ```

- **扩展运算符(...)**

  这提供了一种简便机制，可以将任何部署了 Iterator 接口的数据结构，转为数组。也就是说，只要某个数据结构部署了 Iterator 接口，就可以对它使用扩展运算符，将其转为数组。

  ```javascript
  let arr = [...iterable];
  ```

- **yield\***

  `yield*`后面跟的是一个可遍历的结构，它会调用该结构的遍历器接口。

  ```javascript
  let generator = function* () {
    yield 1;
    yield* [2,3,4];
    yield 5;
  };
  
  var iterator = generator();
  
  iterator.next() // { value: 1, done: false }
  iterator.next() // { value: 2, done: false }
  iterator.next() // { value: 3, done: false }
  iterator.next() // { value: 4, done: false }
  iterator.next() // { value: 5, done: false }
  iterator.next() // { value: undefined, done: true }
  ```

- `Array.from()`

- `Map(), Set(), WeakMap(), WeakSet()`（比如`new Map([['a',1],['b',2]])`）
- `Promise.all()`
- `Promise.race()`

# for 循环

for 是最早出现的循环，也是最常用的，它是一种循环机制，能够满足大部分的遍历。主要是依靠角标来获取数组内成员。

```js
// 三个语句都可省略
for (语句 1; 语句 2; 语句 3) { 
  循环 执行的代码块...
}
```

# while 循环

while 循环只要指定条件结果为 true，循环就可以一直执行代码块。

```js
while (条件){
    需要执行的代码
}
```

# do while 循环

do**...** while 循环是 while 循环的变体。该循环会在判断条件是否为真之前执行一次代码块，然后如果条件为真的话，就会重复这个循环。

```js
do
{
    需要执行的代码
}
while (条件);
```

# for in 循环

for ... in 是在 `ES5` 中新增的，遍历所有可枚举的属性（包括原型上的），最好只用来循环对象。

```js
for(let 成员 in 对象){
    循环的代码块
}
```

for in 会遍历到对象上的原型方法,如果不想遍历原型方法和属性值，可以在循环内部判断一下,`hasOwnPropery`方法可以判断某属性是否是该对象的实例属性

```JavaScript
for (let key in object) {
  if (Object.hasOwnProperty.call(object, key)) {
    const element = object[key];
  }
}
```

**遍历顺序**：先遍历出整数属性（integer properties，按照升序），然后其他属性按照创建时候的顺序遍历出来。

# for of 循环

for...of 是在 `ES6` 中新增的 语句，它遍历一个可迭代的对象，Object是不行的。

# forEach 循环 

`forEach` 是 `ES5` 提出的，挂载在可迭代对象原型上的方法。`forEach`是一个遍历器，负责遍历可迭代对象。

# 速度比较

各循环执行速度从快到慢排序如下：

1. for循环、while循环、do while循环
2. for of循环
3. forEach循环、map循环、reduce循环
4. for in循环
