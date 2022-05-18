---
title: ArrayBuffer
date: 2022-04-09 15:42:09
categories: [ES6]
tags:
- ES6
- ArrayBuffer
- TypedArray
- DataView
---

# ArrayBuffer

存储固定btye（字节）的二进制数据，它不能直接读写，只能通过视图（`TypedArray`视图和`DataView`视图)来读写，视图的作用是以指定格式解读二进制数据。

使用：

```js
const buf = new ArrayBuffer(32) //生成一段32字节的连续内存空间，每个字节的值默认都是 0。
```

## btyeLength

二进制数组的字节长度

```js
const buffer = new ArrayBuffer(32)
buffer.byteLength // 32
```

## slice

截取

```js
const buffer = new ArrayBuffer(32)
const newBuffer = buffer.slice(2,6) //拷贝第2至5个字节
```

## isView

表示参数是否为`ArrayBuffer`的视图实例。

```javascript
const buffer = new ArrayBuffer(8);
ArrayBuffer.isView(buffer) // false

const v = new Int32Array(buffer);
ArrayBuffer.isView(v) // true
```

# TypedArray视图

`TypedArray`一共包括九种：

- `Int8Array`：8 位有符号整数，长度 1 个字节。
- `Uint8Array`：8 位无符号整数，长度 1 个字节。
- `Uint8ClampedArray`：8 位无符号整数，长度 1 个字节，溢出处理不同。
- `Int16Array`：16 位有符号整数，长度 2 个字节。
- `Uint16Array`：16 位无符号整数，长度 2 个字节。
- `Int32Array`：32 位有符号整数，长度 4 个字节。
- `Uint32Array`：32 位无符号整数，长度 4 个字节。
- `Float32Array`：32 位浮点数，长度 4 个字节。
- `Float64Array`：64 位浮点数，长度 8 个字节。

以上九种构造函数统称为`TypedArray`视图，数组的大部分方法它们都能使用。（concat不能）

特点：

- 每个数组成员的类型相同
- 每个数组成员的默认值为0

使用：

```js
const b = new ArrayBuffer(8);
// 创建一个指向b的Int32视图，开始于字节0，直到缓冲区的末尾
const v1 = new Int32Array(b);
// 创建一个指向b的Uint8视图，开始于字节2，直到缓冲区的末尾
const v2 = new Uint8Array(b, 2);
// 创建一个指向b的Int16视图，开始于字节2，长度为2
const v3 = new Int16Array(b, 2, 2);
```

参数1：`ArrayBuffer`对象，参数2：视图开始的字节数，必须为每个成员字节数的整数倍（可选），参数3：视图的长度（可选）

上面三个视图指向同一个`ArrayBuffer`对象，只要任何一个视图对内存有所修改，就会在另外两个视图上反应出来。

`TypedArray`视图还可以不接收`ArrayBuffer`生成：

- 数字（长度）：`const f64a = new Float64Array(8) //8个Float64`  
- 普通数组：`const typedArray = new Uint8Array([1, 2, 3, 4])`
- 另一个`TypedArray`

# DataView视图

使用：

```js
const buffer = new ArrayBuffer(24);
const dv = new DataView(buffer);
```

参数1：`ArrayBuffer`对象，参数2：视图开始的字节数（可选），参数3：视图的长度（可选）

`DataView`实例提供 8 个方法读取内存：（参数：开始的字节序号）

- `getInt8`：读取 1 个字节，返回一个 8 位整数。
- `getUint8`：读取 1 个字节，返回一个无符号的 8 位整数。
- `getInt16`：读取 2 个字节，返回一个 16 位整数。
- `getUint16`：读取 2 个字节，返回一个无符号的 16 位整数。
- `getInt32`：读取 4 个字节，返回一个 32 位整数。
- `getUint32`：读取 4 个字节，返回一个无符号的 32 位整数。
- `getFloat32`：读取 4 个字节，返回一个 32 位浮点数。
- `getFloat64`：读取 8 个字节，返回一个 64 位浮点数。

DataView 视图提供 8 个方法写入内存：（参数1：开始的字节序号，参数2：写入的数据）

- `setInt8`：写入 1 个字节的 8 位整数。
- `setUint8`：写入 1 个字节的 8 位无符号整数。
- `setInt16`：写入 2 个字节的 16 位整数。
- `setUint16`：写入 2 个字节的 16 位无符号整数。
- `setInt32`：写入 4 个字节的 32 位整数。
- `setUint32`：写入 4 个字节的 32 位无符号整数。
- `setFloat32`：写入 4 个字节的 32 位浮点数。
- `setFloat64`：写入 8 个字节的 64 位浮点数。

get和set方法还可接收最后一个参数：false：表示使用大端字节序（默认），true：表示使用小端字节序

- 大端字节序：从多字节数据类型的第一个字节开始读取（或存储）
- 小端字节序：从多字节数据类型的最后一个字节开始读取（或存储）





[参考文献](https://es6.ruanyifeng.com/#docs/arraybuffer)
