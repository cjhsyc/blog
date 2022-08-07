---
title: css高级
date: 2022-07-27 23:29:30
categories: [css]
tags: 
  - css
  - style
---

# 样式初始化

```css
/* 样式初始化 */
* {
  /* 清除默认的外边框和内边框 */
  margin: 0;
  padding: 0;
  /* 统一盒子模型 */
  box-sizing: border-box;
}

/* 和字体相关的样式写在body中,因为默认会被继承 */
body {
  font-family: 'Lato', sans-serif;
  font-weight: 400;
  font-size: 16px;
  /* 当前字体尺寸的1.7倍 */
  line-height: 1.7;
  color: #777;
  
  padding: 30px;
}
```

# 背景图/裁剪区域

`html`文件中导入样式文件，并准备一个容器。

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />

    <!-- Lato字体 -->
    <link rel="stylesheet" href="http://fonts.googleapis.com/css?family=Lato:100,300,400,700,900" />
    <!-- 基础样式 -->
    <link rel="stylesheet" href="style/style.css" />
    <!-- 页面图标 -->
    <link rel="shortcut icon" href="img/favicon.ico" />

    <title>Document</title>
  </head>
  <body>
    <header class="header">header content...</header>
  </body>
</html>
```

```css
.header {
  height: 95vh;
  /* 可指定多个背景图 */
  /* linear-gradient函数用于创建一个颜色线性渐变的图片*/
  /* url指定图片路径 */
  background-image: linear-gradient(
      to right bottom,
      rgba(126, 213, 111, 0.8),
      rgba(40, 180, 131, 0.8)
    ),
    url(../img/background.png);
  /* 保持图像的纵横比并完全覆盖背景区域 */
  background-size: cover;
  /* 窗口缩放时图片顶部保持不动 */
  background-position: top;

  /* clip-path:指定元素的可视区域 */
  /* polygon函数用于创建一个多边形,从右上角开始顺时针指定顶点 */
  clip-path: polygon(0 0, 100% 0, 100% 80%, 0 100%);
}
```

