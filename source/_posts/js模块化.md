---
title: js模块化
date: 2022-10-21 22:46:54
categories: JavaScript
tags:
- cjs
- esm
- umd
---

# cjs (commonjs)

`commonjs` 是 Node 中的模块规范，通过 `require` 及 `exports` 进行导入导出 (进一步延伸的话，`module.exports` 属于 `commonjs2`)

同时，webpack 也对 `cjs` 模块进行解析，因此 `cjs` 模块可以运行在 node 环境及 webpack 环境下的，但不能在浏览器中直接使用。但如果你写前端项目在 webpack 中，也可以理解为它在浏览器和 Node 都支持。

```js
// sum.js
exports.sum = (x, y) => x + y;

// index.js
const { sum } = require("./sum.js");
```

由于 `cjs` 为动态加载，可直接 `require` 一个变量

```js
require(`./${a}`);
```

# esm (es module)

`esm` 是 tc39 对于 ESMAScript 的模块话规范，正因是语言层规范，**因此在 Node 及 浏览器中均会支持**。

它使用 `import/export` 进行模块导入导出.

```js
// sum.js
export const sum = (x, y) => x + y;

// index.js
import { sum } from "./sum";
```

`esm` 为静态导入，正因如此，可在编译期进行 **Tree Shaking**，减少 js 体积。

如果需要动态导入，tc39 为动态加载模块定义了 API: `import(module)` 。

```js
const ms = await import("https://cdn.skypack.dev/ms@latest");

ms.default(1000);
```

目前，在浏览器与 node.js 中均原生支持 esm。

## 动态 import

标准用法的 import 导入的模块是静态的，会使所有被导入的模块，在加载时就被编译（无法做到按需编译，降低首页加载速度）。有些场景中，你可能希望根据条件导入模块或者按需导入模块，这时你可以使用动态导入代替静态导入。下面的是你可能会需要动态导入的场景：

- 当静态导入的模块很明显的降低了代码的加载速度且被使用的可能性很低，或者并不需要马上使用它。
- 当静态导入的模块很明显的占用了大量系统内存且被使用的可能性很低。
- 当被导入的模块，在加载时并不存在，需要异步获取。
- 当导入模块的说明符，需要动态构建。（静态导入只能使用静态说明符）
- 当被导入的模块有副作用（这里说的副作用，可以理解为模块中会直接运行的代码），这些副作用只有在触发了某些条件才被需要时。（原则上来说，模块不能有副作用，但是很多时候，你无法控制你所依赖的模块的内容）

请不要滥用动态导入（只有在必要情况下采用）。静态框架能更好的初始化依赖，而且更有利于静态分析工具和 **Tree Shaking** 发挥作用。

## 与cjs比较

- cjs 模块输出的是一个值的拷贝，esm 输出的是值的引用
- cjs 模块是运行时加载，esm 是编译时加载

# umd

一种兼容 `cjs` 与 `amd` 的模块，既可以在 node/webpack 环境中被 `require` 引用，也可以在浏览器中直接用 CDN 被 `script.src` 引入。

```js
(function (root, factory) {
  if (typeof define === "function" && define.amd) {
    // AMD
    define(["jquery"], factory);
  } else if (typeof exports === "object") {
    // CommonJS
    module.exports = factory(require("jquery"));
  } else {
    // 全局变量
    root.returnExports = factory(root.jQuery);
  }
})(this, function ($) {
  // ...
});
```
