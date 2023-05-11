---
title: link和css中import的区别
date: 2022-11-04 12:23:19
categories: css
tags:
- link
- css
---

# 引入CSS

有4种方式可以在 `html` 中引入 `css`。分别为：内联方式、嵌入方式、链接方式、导入方式，其中链接方式使用link引入，导入方式使用import引入。

## link

使用 HTML 头部的 `<head>` 标签引入外部的 CSS 文件。

```html
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
```

这是最常见的也是最推荐的引入 CSS 的方式。使用这种方式，所有的 CSS 代码只存在于单独的 CSS 文件中，所以具有良好的可维护性。并且所有的 CSS 代码只存在于 CSS 文件中，CSS 文件会在第一次加载时引入，以后切换页面时只需加载 HTML 文件即可。

## @import

使用 CSS 规则引入外部 CSS 文件。

```html
<style>
    @import url(style.css);
</style>
```

# 区别

- link除了引用样式文件，还可以引用图片等资源文件，而@import属于CSS范畴，只能加载CSS。
- link引用CSS时，在页面载入时同时加载；@import需要页面网页完全载入以后加载。
- link是XHTML标签，无兼容问题；@import是在CSS2.1提出的，低版本的浏览器不支持。
- link支持使用`JavaScript`控制DOM去改变样式；而@import不支持。
