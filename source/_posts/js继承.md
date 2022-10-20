---
title: js继承
date: 2022-10-17 20:08:14
categories: JavaScript
tags: [JavaScript]
---

# 原型链继承

```js
function Parent () {
  this.name = 'kevin';
}

Parent.prototype.getName = function () {
  console.log(this.name);
}

function Child () {

}

// 子类的原型指向父类的实例
Child.prototype = new Parent();

var child1 = new Child();
console.log(child1.getName()) // kevin
```

缺点：

1. 引用（对象）类型的属性被所有实例共享

   ```js
   function Parent () {
     this.names = ['kevin', 'daisy'];
   }
   
   function Child () {
   
   }
   
   Child.prototype = new Parent();
   
   var child1 = new Child();
   child1.names.push('yayu');
   console.log(child1.names); // ["kevin", "daisy", "yayu"]
   
   var child2 = new Child();
   console.log(child2.names); // ["kevin", "daisy", "yayu"]
   ```

2. 在创建 Child 的实例时，不能向Parent传参

# 构造函数继承

```js
function Parent (name) {
  this.names = [name, 'daisy'];
}

function Child (name) {
  // 调用父类构造函数
  Parent.call(this, name);
}

var child1 = new Child('kevin1');
child1.names.push('yayu');
console.log(child1.names); // ["kevin1", "daisy", "yayu"]

var child2 = new Child('kevin2');
console.log(child2.names); // ["kevin2", "daisy"]
```

优点：

1. 避免了引用（对象）类型的属性被所有实例共享

2. 可以在 Child 中向 Parent 传参

缺点：只能继承父类的实例属性和方法，不能继承原型属性或者方法。

# 组合继承

融合原型链继承和构造函数的优点，是 JavaScript 中最常用的继承模式。

```js
function Parent (name) {
  this.name = name;
  this.colors = ['red', 'blue', 'green'];
}

Parent.prototype.getName = function () {
  console.log(this.name)
}

function Child (name, age) {
  Parent.call(this, name); // 第二次调用父构造函数
  this.age = age;
}

Child.prototype = new Parent(); // 第一次调用父构造函数
Child.prototype.constructor = Child; // 修改为正确的constructor指向

var child1 = new Child('kevin', '18');
child1.colors.push('black');

console.log(child1.name); // kevin
console.log(child1.age); // 18
console.log(child1.colors); // ["red", "blue", "green", "black"]

var child2 = new Child('daisy', '20');

console.log(child2.name); // daisy
console.log(child2.age); // 20
console.log(child2.colors); // ["red", "blue", "green"]
```

缺点：会调用两次父构造函数。所以，在上述例子中，如果我们打印 `child1` 对象，我们会发现 `Child.prototype` 和 `child1` 都有一个属性为`colors`，属性值为`['red', 'blue', 'green']`。

# 原型式继承

就是 `ES5` `Object.create` 的模拟实现，将传入的对象作为创建的对象的原型。

```js
function createObj(o) {
  function F(){}
  F.prototype = o;
  return new F();
}
```

缺点：因为`Object.create`方法实现的是浅拷贝，多个实例的引用类型属性指向相同的内存，存在篡改的可能。

```js
var person = {
  name: 'kevin',
  friends: ['daisy', 'kelly']
}

var person1 = createObj(person);
var person2 = createObj(person);

person1.name = 'person1';
console.log(person2.name); // kevin

person1.friends.push('taylor');
console.log(person2.friends); // ["daisy", "kelly", "taylor"]
```

注意：修改`person1.name`的值，`person2.name`的值并未发生改变，并不是因为`person1`和`person2`有独立的 name 值，而是因为`person1.name = 'person1'`，给`person1`添加了 name 值，并非修改了原型上的 name 值。

# 寄生式继承

创建一个仅用于封装继承过程的函数，该函数在内部以某种形式来做增强对象，最后返回对象。

```js
function createObj (o) {
  var clone = Object.create(o);
  clone.sayName = function () {
    console.log('hi');
  }
  return clone;
}
```

缺点：跟上面讲的原型式继承一样。

# 寄生组合式继承

```js
function Parent (name) {
  this.name = name;
  this.colors = ['red', 'blue', 'green'];
}

Parent.prototype.getName = function () {
  console.log(this.name)
}

function Child (name, age) {
  Parent.call(this, name);
  this.age = age;
}

// 关键的三步
var F = function () {};
F.prototype = Parent.prototype;
Child.prototype = new F();
// 上面三步相当于
// Child.prototype = Object.create(Parent.prototype)

Child.prototype.constructor = Child

var child1 = new Child('kevin', '18');
console.log(child1);
```

优点：只调用了一次 `Parent` 构造函数，并且因此避免了在 `Parent.prototype` 上面创建不必要的、多余的属性。与此同时，原型链还能保持不变；因此，还能够正常使用 `instanceof` 和 `isPrototypeOf`。

# class继承

利用`babel`工具进行转换，我们会发现`extends`实际采用的也是寄生组合继承方式。

```js
class Animal {
  constructor(name) {
    this.name = name;
  }

  hello() {
    console.log("hello");
  }
}

class Dog extends Animal {
  constructor(name, say) {
    super(name); // 通过super调用父构造函数
    this.say = say;
  }
}
```



[参考](https://github.com/mqyqingfeng/Blog/issues/16)
