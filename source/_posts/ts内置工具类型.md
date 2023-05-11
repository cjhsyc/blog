---
title: ts内置工具类型
date: 2022-11-01 14:10:03
categories: TypeScript
tags:
- infer
---

# Record

`Record<Keys, Type>`定义一个对象的 key 和 value 类型

- 源码

  ```typescript
  type Record<K extends keyof any, T> = {
      [P in K]: T;
  };
  ```


- 使用

  ```typescript
  type RecordType = Record<string, string|number>;
  
  const recordExample: RecordType ={
    a: 1,
    b: "1"
  }
  ```

# Partial

`Partial<Type>`构造一个类型使`Type`的所有属性都设置为可选。

- 源码

  ```typescript
  type Partial<T> = {
      [P in keyof T]?: T[P];
  };
  ```


- 使用

  ```typescript
  interface Example {
      a: string;
      b: number;
  }
  
  type PartialExample = Partial<Example>;
  /**
   * PartialExample
   * interface {
   *     a?: string | undefined;
   *     b?: number | undefined;
   * }
   */
  ```

# Required

`Required<Type>`构造一个类型使`Type`的所有属性都设置为`required`，与`Partial<Type>`功能相反。

- 源码

  ```typescript
  type Required<T> = {
      [P in keyof T]-?: T[P];
  };
  ```


- 使用

  ```typescript
  interface Example {
      a?: string;
      b?: number;
  }
  
  type RequiredExample = Required<Example>;
  /**
   * RequiredExample
   * interface {
   *     a: string;
   *     b: number;
   * }
   */
  ```

# Readonly

`Required<Type>`构造一个类型使`Type`的所有属性都设置为`readonly`。

- 源码

  ```typescript
  type Readonly<T> = {
      readonly [P in keyof T]: T[P];
  };
  ```


- 使用

  ```typescript
  interface Example {
      a: string;
      b: number;
  }
  
  type ReadonlyExample = Readonly<Example>;
  /**
   * ReadonlyExample
   * interface {
   *     readonly a: string;
   *     readonly b: number;
   * }
   */
  ```

# Pick

`Pick<Type, Keys>`通过从`Type`中选择一组属性`Keys`来构造一个类型。

- 源码

  ```typescript
  type Pick<T, K extends keyof T> = {
      [P in K]: T[P];
  };
  ```


- 使用

  ```typescript
  interface Example {
      a: string;
      b: number;
      c: symbol;
  }
  
  type PickExample = Pick<Example, "a"|"b">;
  /**
   * PickExample
   * interface {
   *     a: string;
   *     b: number;
   * }
   */
  ```

# Omit

`Omit<Type, Keys>`通过从`Type`中选择所有属性然后删除`Keys`来构造一个类型，与`Pick<Type, Keys>`功能相反。

- 源码

  ```typescript
  type Omit<T, K extends keyof any> = Pick<T, Exclude<keyof T, K>>;
  ```


- 使用

  ```typescript
  interface Example {
      a: string;
      b: number;
      c: symbol;
  }
  
  type OmitExample = Omit<Example, "a"|"b">;
  /**
   * OmitExample
   * interface {
   *     c: symbol;
   * }
   */
  ```

# Exclude

`Exclude<UnionType, ExcludedMembers>`通过从`UnionType`中排除可分配给`ExcludedMembers`的所有联合成员来构造类型。

- 源码

  ```typescript
  type Exclude<T, U> = T extends U ? never : T;
  ```


- 使用

  ```typescript
  type ExcludeExample = Exclude<"a"|"b"|"c"|"z", "a"|"b"|"d">;
  
  /**
   * ExcludeExample
   * "c" | "z"
   */
  ```

# Extract

`Extract<Type, Union>`通过从`Type`中提取所有可分配给`Union`的联合成员来构造一个类型，与`Exclude<UnionType, ExcludedMembers>`功能相反。

- 源码

  ```typescript
  type Extract<T, U> = T extends U ? T : never;
  ```

- 使用

  ```typescript
  type ExtractExample = Extract<"a"|"b"|"c"|"z", "a"|"b"|"d">;
  /**
   * ExtractExample
   * "a" | "b"
   */
  ```

# NonNullable

`NonNullable<Type>`通过从`Type`中排除`null`和`undefined`来构造一个类型。

- 源码

  ```typescript
  type NonNullable<T> = T extends null | undefined ? never : T;
  ```

- 使用

  ```typescript
  type NonNullableExample = NonNullable<number|string|null|undefined>;
  /**
   * NonNullableExample
   * string | number
   */
  ```

# Parameters

`Parameters<Type>`从函数类型`Type`的参数中使用的类型构造元组类型。

- 源码

  ```typescript
  type Parameters<T extends (...args: any) => any> = T extends (...args: infer P) => any ? P : never;
  ```

- 使用

  ```typescript
  type FnType = (a1: number, a2: string) => void;
  
  type ParametersExample = Parameters<FnType>;
  /**
   * ParametersExample
   * [a1: number, a2: string]
   */
  ```

## infer

`infer`和`extends`配合，用作类型推断。

1. `infer`只能在条件类型的 extends 子句中使用
2. `infer`得到的类型只能在`true`语句中使用

使用案例：

- 判断数组类型

  ```typescript
  type InferArray<T> = T extends (infer U)[] ? U : never;
  ```

- 判断函数参数类型（`Parameters`）

- 判断函数返回值类型（`ReturnType`）

- 判断元组最后一个元素的类型

  ```typescript
  type InferLast<T extends unknown[]> = T extends [... infer _, infer Last] ? Last : never;
  ```

- 判断字符串第一个字符的字面量类型

  ```typescript
  type InferString<T extends string> = T extends `${infer First}${infer _}` ? First : never;
  ```

# ReturnType

`ReturnType<Type>`构造一个由函数`Type`的返回类型组成的类型。

- 源码

  ```typescript
  type ReturnType<T extends (...args: any) => any> = T extends (...args: any) => infer R ? R : any;
  ```

- 使用

  ```typescript
  type FnType = (a1: number, a2: string) => string | number;
  
  type ReturnTypeExample = ReturnType<FnType>;
  /**
   * ReturnTypeExample
   * string | number
   */
  ```

# ConstructorParameters

# InstanceType





