---
title: type和interface区别
date: 2022-10-31 17:35:03
categories: TypeScript
tags:
- type
- interface
---

# 定义类型范围

`interface`只能定义对象类型，而`type`声明可以声明任何类型，包括基础类型、联合类型、元组类型、交叉类型。

```typescript
// 基本类型 
type person = string
 
// 对象类型
interface Dog {
 name: string;
}
interface Cat {
 age: number;
}

// 联合类型
type animal = Dog | Cat
 
// 元组类型
type animal = [Dog, Cat]
```

# 扩展性

- 接口可以`extends`、`implements`扩展多个接口或类。

```typescript
// interface extends interface
interface Person {
  name: string
}
interface User extends Person {
  age: number
}
 
// interface extends type
type Person = {
  name: string
}
interface User extends Person {
  age: number
}
```

- `type`可以使用交叉类型&，来使成员类型合并。

```typescript
// type & type
type Person = {
   name: string
}
type User = Person & { age: number }
 
// type & interface
interface Person {
  name: string
}
type User = {age: number} & Person
```

# 合并声明

定义两个相同名称的接口会合并声明；而定义两个同名的`type`会出现异常。

```typescript
interface Person { 
  name: string
}
interface Person {
  age: number
}
// 合并为:
// interface Person { 
//   name: string 
//   age: number
// }
```

# 映射类型

`type` 能使用 in 关键字生成映射类型，但 `interface` 不行。

```typescript
type Keys = 'firstname' | 'surname';

type DudeType = {
  [key in Keys]: string;
};

const test: DudeType = {
  firstname: 'Pawel',
  surname: 'Grzybek',
};
```

# typeof

`type`可以使用`typeof`获取实例类型

```typescript
let div = document.createElement('div');
type B = typeof div
```

# 默认导出方式

`inerface` 支持同时声明 和 默认导出；而type必须先声明后导出

```typescript
export default interface Config {
  name: string;
}
```

```typescript
type Config2 = {name: string}
export default Config2
```

