---
title: vite特点
date: 2022-10-21 21:23:19
categories: 前端工程化
tags:
- vite
- ESM
- Webpack
---

# Vite

Vite是一种新型前端构建工具，能够显著提升前端开发体验。它主要由两部分组成：

- 一个开发服务器，它基于 原生 ES 模块 提供了 丰富的内建功能，如速度快到惊人的 模块热更新（`HMR`）。
- 一套构建指令，它使用 `Rollup` 打包你的代码，并且它是预配置的，可输出用于生产环境的高度优化过的静态资源。

Vite 意在提供开箱即用的配置，同时它的 插件 `API` 和 `JavaScript API`带来了高度的可扩展性，并有完整的类型支持。

# 冷启动

Vite 通过在一开始将应用中的模块区分为 **依赖** 和 **源码** 两类，改进了开发服务器启动时间。

- **依赖** 大多为在开发时不会变动的纯 JavaScript。一些较大的依赖（例如有上百个模块的组件库）处理的代价也很高。依赖也通常会存在多种模块化格式（例如 `ESM` 或者 `CommonJS`）。

  Vite 将会使用 `esbuild` **预构建依赖**。`esbuild` 使用 Go 编写，并且比以 JavaScript 编写的打包器预构建依赖快 10-100 倍。

- **源码** 通常包含一些并非直接是 JavaScript 的文件，需要转换（例如 `JSX`，`CSS` 或者 `Vue/Svelte` 组件），时常会被编辑。同时，并不是所有的源码都需要同时被加载（例如基于路由拆分的代码模块）。

  Vite 以 原生 `ESM` 方式提供源码。这实际上是让浏览器接管了打包程序的部分工作：Vite 只需要在浏览器请求源码时进行转换并按需提供源码。根据情景动态导入代码，即只在当前屏幕上实际使用时才会被处理。

## 生产环境仍需打包

尽管原生 `ESM` 现在得到了广泛支持，但由于嵌套导入会导致额外的网络往返，在生产环境中发布未打包的 `ESM` 仍然效率低下（即使使用 HTTP/2）。为了在生产环境中获得最佳的加载性能，最好还是将代码进行 tree-shaking、懒加载和 chunk 分割（以获得更好的缓存）。

Vite使用`Rollup`进行生产打包，因此，Vite的主要优势在开发阶段。

# NPM 依赖解析

原生 ES 导入不支持下面这样的裸模块导入：

```js
import { someMethod } from 'my-dep'
```

上面的代码会在浏览器中抛出一个错误。Vite 将会检测到所有被加载的源文件中的此类裸模块导入，并执行以下操作:

1. **预构建** 它们可以提高页面加载速度，并将 `CommonJS / UMD` 转换为 `ESM` 格式。**预构建**这一步由 `esbuild` 执行，这使得 Vite 的冷启动时间比任何基于 JavaScript 的打包器都要快得多。
2. 重写导入为合法的 URL，例如 `/node_modules/.vite/deps/my-dep.js?v=f3sf2ebd` 以便浏览器能够正确导入它们。

**依赖是强缓存的**，Vite 通过 HTTP 头来缓存请求得到的依赖。

# 预构建

1. **`CommonJS` 和 `UMD` 兼容性:** 开发阶段中，Vite 的开发服务器将所有代码视为原生 ES 模块。因此，Vite 必须先将作为 `CommonJS` 或 `UMD` 发布的依赖项转换为 `ESM`。

   当转换 `CommonJS` 依赖时，Vite 会执行智能导入分析，这样即使导出是动态分配的（如 React），按名导入也会符合预期效果：

   ```js
   // 符合预期
   import React, { useState } from 'react'
   ```

2. **性能：** Vite 将有许多内部模块的 `ESM` 依赖关系转换为单个模块，以提高后续页面加载性能。

   一些包将它们的 ES 模块构建作为许多单独的文件相互导入。例如，`lodash-es` 有超过 600 个内置模块！当我们执行 `import { debounce } from 'lodash-es'` 时，浏览器同时发出 600 多个 HTTP 请求！尽管服务器在处理这些请求时没有问题，但大量的请求会在浏览器端造成网络拥塞，导致页面的加载速度相当慢。

   通过预构建 `lodash-es` 成为一个模块，就只需要一个 HTTP 请求了。

在服务器已经启动之后，如果遇到一个新的依赖关系导入，而这个依赖关系还没有在缓存中，Vite 将重新运行依赖构建进程并重新加载页面。如果没有找到相应的缓存，Vite 将抓取你的源码，并自动寻找引入的依赖项（即 "bare import"，表示期望从 `node_modules` 解析），并将这些依赖项作为预构建包的入口点。预构建通过 `esbuild` 执行，所以它通常非常快。

## 缓存

### 文件系统缓存

Vite 会将预构建的依赖缓存到 `node_modules/.vite`。它根据几个源来决定是否需要重新运行预构建步骤:

- `package.json` 中的 `dependencies` 列表
- 包管理器的 `lockfile`，例如 `package-lock.json`, `yarn.lock`，或者 `pnpm-lock.yaml`
- 可能在 `vite.config.js` 相关字段中配置过的

只有在上述其中一项发生更改时，才需要重新运行预构建。

如果出于某些原因，你想要强制 Vite 重新构建依赖，你可以用 `--force` 命令行选项启动开发服务器，或者手动删除 `node_modules/.vite` 目录。

### 浏览器缓存

解析后的依赖请求会以 HTTP 头 `max-age=31536000,immutable` 强缓存，以提高在开发时的页面重载性能。一旦被缓存，这些请求将永远不会再到达开发服务器。如果安装了不同的版本（这反映在包管理器的 `lockfile` 中），则附加的版本 query 会自动使它们失效。如果你想通过本地编辑来调试依赖项，你可以:

1. 通过浏览器调试工具的 Network 选项卡暂时禁用缓存；
2. 重启 Vite dev server，并添加 `--force` 命令以重新构建依赖；
3. 重新载入页面。

# 热更新

- `vite server`：指 `vite` 在开发时启动的 `server`
- `vite client`：`vite dev server` 会在 `index.html` 中，注入路径为 `@vite/client` 的脚本，这个脚本是运行在浏览器的

热更新的核心流程：

1. 修改代码，vite server 监听到代码被修改
2. vite 计算出热更新的边界（即受到影响，需要进行更新的模块）
3. vite server 通过 websocket 告诉 vite client 需要进行热更新
4. 浏览器拉取修改后的模块
5. 执行热更新的代码

Vite 提供了一套原生 `ESM` 的 [`HMR API`](https://cn.vitejs.dev/guide/api-hmr.html)。 具有 `HMR` 功能的框架可以利用该 `API` 提供即时、准确的更新，而无需重新加载页面或清除应用程序状态。

# TypeScript

Vite 天然支持引入 `.ts` 文件。

Vite 仅执行 `.ts` 文件的转译工作，并 **不** 执行任何类型检查。并假设类型检查已经被你的 IDE 或构建过程接管了（你可以在构建脚本中运行 `tsc --noEmit` 或者安装 `vue-tsc` 然后运行 `vue-tsc --noEmit` 来对你的 `*.vue` 文件做类型检查）。

Vite 使用 `esbuild` 将 `TypeScript` 转译到 JavaScript，约是 `tsc` 速度的 20~30 倍，同时 HMR 更新反映到浏览器的时间小于 50ms。

使用 仅含类型的导入和导出 形式的语法可以避免潜在的 “仅含类型的导入被不正确打包” 的问题，写法示例如下：

```typescript
import type { T } from 'only/types'
export type { T }
```

# 与Webpack区别

`vite`和`webpack`的主要区别在开发阶段：

- `Webpack`基于`commonjs`，先打包合并然后请求服务器，更改一个模块，其他有依赖关系的模块都会重新打包； 

- `Vite`基于`es module`，自动向依赖的module发请求，服务端按需编译返回，改动一个模块仅仅会重新请求该模块；

