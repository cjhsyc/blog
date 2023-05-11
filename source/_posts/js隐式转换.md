---
title: js隐式转换
date: 2022-12-01 17:48:46
categories: JavaScript
tags:
---

# 三种隐式转换类型

涉及隐式转换最多的是两个运算符 + 和 ==。\- * / 这些运算符只会针对number类型，故转换的结果只能是转换成number类型。

隐式转换中主要涉及到三种转换：

1. 将值转为原始值，`ToPrimitive()`。

2. 将值转为数字，`ToNumber()`。

3. 将值转为字符串，`ToString()`。

# ToPrimitive

```js
ToPrimitive(input[, PreferredType])
```

`input`是要转换的值；`PreferredType`是可选参数，可以是`number`或`string`，他只是一个转换标志，转化后的结果并不一定是这个参数所指的类型，但是转换结果一定是一个原始值（或者报错）。

- `PreferredType` 为`number`时：
  1. 如果输入的值已经是一个原始值，则直接返回它 
  2. 否则，如果输入的值是一个对象，则调用该对象的`valueOf()`方法，如果`valueOf()`方法的返回值是一个原始值，则返回这个原始值。 
  3. 否则，调用这个对象的`toString()`方法，如果`toString()`方法返回的是一个原始值，则返回这个原始值。 
  4. 否则，抛出`TypeError`异常。

- `PreferredType` 为`string`时：
  1. 如果输入的值已经是一个原始值，则直接返回它 
  2. 否则，调用这个对象的`toString()`方法，如果`toString()`方法返回的是一个原始值，则返回这个原始值。 
  3. 否则，如果输入的值是一个对象，则调用该对象的`valueOf()`方法， 如果`valueOf()`方法的返回值是一个原始值，则返回这个原始值。 
  4. 否则，抛出`TypeError`异常。

- 未设置`PreferredType`时：
  1. 该对象为`Date`类型，则`PreferredType`被设置为`String` 
  2. 否则，`PreferredType`被设置为`Number`

# valueOf和toString

任何对象都会有`valueOf`和`toString`方法，这两个方法都在`Object.prototype`上。

## valueOf

- Number、Boolean、String这三种构造函数生成的基础值的对象形式，通过`valueOf`转换后会变成相应的原始值。

  ```js
  var num = new Number('123');
  num.valueOf(); // 123
  
  var str = new String('12df');
  str.valueOf(); // '12df'
  
  var bool = new Boolean('fd');
  bool.valueOf(); // true
  ```

- Date这种特殊的对象，其原型`Date.prototype`上内置的`valueOf`函数将日期转换为日期的毫秒的形式的数值。

  ```js
  var a = new Date();
  a.valueOf(); // 1515143895500
  ```

- 除此之外返回的都为this，即对象本身。

  ```js
  var a = new Array();
  a.valueOf() === a; // true
  
  var b = new Object({});
  b.valueOf() === b; // true
  ```

## toString

`Number`、`Boolean`、`String`、`Array`、`Date`、`RegExp`、`Function`这几种构造函数生成的对象，通过`toString`转换后会变成相应的字符串的形式，因为这些构造函数上封装了自己的`toString`方法。

```js
var num = new Number('123sd');
num.toString(); // 'NaN'

var str = new String('12df');
str.toString(); // '12df'

var bool = new Boolean('fd');
bool.toString(); // 'true'

var arr = new Array(1,2);
arr.toString(); // '1,2'

var d = new Date();
d.toString(); // "Wed Oct 11 2017 08:00:00 GMT+0800 (中国标准时间)"

var func = function () {}
func.toString(); // "function () {}"
```

除这些对象及其实例化对象之外，其他对象返回的都是该对象的类型，都是继承的`Object.prototype.toString`方法。

```js
var obj = new Object({});
obj.toString(); // "[object Object]"

Math.toString(); // "[object Math]"
```

# ToNumber

根据参数类型进行下面转换：

| 参数      | 结果                                                         |
| --------- | ------------------------------------------------------------ |
| undefined | `NaN`                                                        |
| null      | +0                                                           |
| 布尔值    | true转换1，false转换为+0                                     |
| 数字      | 无须转换                                                     |
| 字符串    | 有字符串解析为数字，例如：‘324’转换为324，‘qwer’转换为`NaN`  |
| 对象(obj) | 先进行 `ToPrimitive(obj, Number)`转换得到原始值，在进行`ToNumber`转换为数字；空数组转换为0，非空数组、空对象、非空对象转换为`NaN` |

# ToString

根据参数类型进行下面转换：

| 参数      | 结果                                                         |
| --------- | ------------------------------------------------------------ |
| undefined | 'undefined'                                                  |
| null      | 'null'                                                       |
| 布尔值    | 转换为'true' 或 'false'                                      |
| 数字      | 数字转换字符串，比如：1.765转为'1.765'                       |
| 字符串    | 无须转换                                                     |
| 对象(obj) | 先进行 `ToPrimitive(obj, String)`转换得到原始值，在进行`ToString`转换为字符串 |

# == 运算符隐式转换

比较运算 x == y，当x和y的类型相同时，没有类型转换，主要注意`NaN`不与任何值相等，包括它自己，即`NaN !== NaN`。

类型不相同时：

1. x、y 为null、undefined两者中一个   // 返回true
2. x、y为Number和String类型时，则转换为Number类型比较。
3. 有Boolean类型时，Boolean转化为Number类型比较。
4. 一个Object类型，一个String或Number类型，将Object类型进行原始转换后，按上面流程进行原始值比较。

# 加号运算符隐式转换

运算x + y，当x、y其中一个为string类型时，将另一个值`ToString`后直接拼接；没有string类型时，`ToNumber`后进行比较。



参考：

[你所忽略的js隐式转换](https://juejin.cn/post/6844903557968166926?share_token=33decd56-6027-4ea9-85f3-9fbf164a0e20)
