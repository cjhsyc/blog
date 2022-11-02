---
title: flex布局
date: 2022-10-27 17:39:28
categories: css
tags:
- flex
---

# flexbox

Flexible Box 模型，通常被称为 flexbox，是一种一维的布局模型。它给 flexbox 的子元素之间提供了强大的空间分布和对齐能力。一个 flexbox 一次只能处理一个维度上的元素布局，一行或者一列。作为对比的是另外一个二维布局 `Grid Layout`，可以同时处理行和列上的布局。

# 轴线

当使用 flex 布局时，首先想到的是两根轴线 — 主轴和交叉轴。主轴由 `flex-direction` 定义，另一根轴（交叉轴）垂直于它。我们使用 flexbox 的所有属性都跟这两根轴线有关，所以有必要在一开始首先理解它。

## flex-direction

主轴由 `flex-direction` 定义，可以取 4 个值：

- `row`
- `row-reverse`
- `column`
- `column-reverse`

如果你选择了 `row` 或者 `row-reverse`，你的主轴将沿着 **inline** 方向（水平）延伸。选择 `column` 或者 `column-reverse` 时，你的主轴会沿着垂直方向延伸，也就是 **block 排列的方向。**

理解主轴和交叉轴的概念对于对齐 flexbox 里面的元素是很重要的；flexbox 的特性是沿着主轴或者交叉轴对齐之中的元素。

# Flex容器

文档中采用了 flexbox 的区域就叫做 flex 容器。为了创建 flex 容器，我们把一个容器的 [`display`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/display) 属性值改为 `flex` 或者 `inline-flex`。完成这一步之后，容器中的直系子元素就会变为 **flex 元素**。所有 CSS 属性都会有一个初始值，所以 flex 容器中的所有 flex 元素都会有下列行为：

- 元素排列为一行 (`flex-direction` 属性的初始值是 `row`)。
- 元素从主轴的起始线开始。
- 元素不会在主轴方向拉伸，但是可以缩小。
- 元素被拉伸来填充交叉轴大小。
- `flex-basis` 属性为 `auto`。
- `flex-wrap` 属性为 `nowrap`。

这会让你的元素呈线形排列，并且把自己的大小作为主轴上的大小。如果有太多元素超出容器，它们会溢出而不会换行。如果一些元素比其他元素高，那么元素会沿交叉轴被拉伸来填满它的大小。

# flex-wrap

虽然`flexbox`是一维模型，但可以使我们的`flex`项目应用到多行中。在这样做的时候，您应该把每一行看作一个新的`flex`容器。任何空间分布都将在该行上发生，而不影响该空间分布的其他行。

为了实现多行效果，请为属性`flex-wrap`添加一个属性值`wrap`。如果您的项目太大而无法全部显示在一行中，则会换行显示。

于`flex-wrap`的值设置为`wrap`，所以项目的子元素换行显示。若将其设置为`nowrap`，这也是初始值，它们将会缩小以适应容器，因为它们使用的是允许缩小的初始`Flexbox`值。如果项目的子元素无法缩小，使用`nowrap`会导致溢出，或者缩小程度还不够小。

# flex-flow 简写属性

可以将两个属性 `flex-direction` 和 `flex-wrap` 组合为简写属性 `flex-flow`。第一个指定的值为 `flex-direction` ，第二个指定的值为 `flex-wrap`。

# flex属性

为了更好地控制 flex 元素，有三个属性可以作用于它们。

## flex-basis

`flex-basis` 定义了该元素的**空间大小**，flex 容器里除了元素所占的空间以外的富余空间就是**可用空间**。该属性的默认值是 `auto` 。此时，浏览器会检测这个元素是否具有确定的尺寸。如果没有给元素设定尺寸，`flex-basis` 的值采用元素内容的尺寸。

## flex-grow

`flex-grow` 若被赋值为一个正整数，flex 元素会以 `flex-basis` 为基础，沿主轴方向增长尺寸。这会使该元素延展，并占据此方向轴上的可用空间。如果有其他元素也被允许延展，那么他们会各自占据可用空间的一部分。

flex-grow 属性可以按比例分配空间。一行上有三个元素时，如果第一个元素 `flex-grow` 值为 2，其他元素值为 1，则第一个元素将占有 2/4, 另外两个元素各占有 1/4。

## flex-shrink

与`flex-grow` 相反，`flex-shrink`属性是处理 flex 元素收缩的问题。

如果我们的容器中没有足够排列 flex 元素的空间，那么可以把 flex 元素`flex-shrink`属性设置为正整数来缩小它所占空间到`flex-basis`以下。与`flex-grow`属性一样，可以赋予不同的值来控制 flex 元素收缩的程度 —— 给`flex-shrink`属性赋予更大的数值可以比赋予小数值的同级元素收缩程度更大。

在计算 flex 元素收缩的大小时，它的最小尺寸也会被考虑进去，就是说实际上 flex-shrink 属性可能会和 flex-grow 属性表现的不一致。

## flex 简写属性

`Flex` 简写形式允许你把三个数值按这个顺序书写 ： `flex-grow`，`flex-shrink`，`flex-basis`。

第一个数值是 `flex-grow`。赋值为正数的话是让元素增加所占空间。第二个数值是`flex-shrink` — 正数可以让它缩小所占空间，但是只有在 flex 元素总和超出主轴才会生效。最后一个数值是 `flex-basis`；flex 元素是在这个基准值的基础上缩放的。

大多数情况下还可以用预定义的简写形式：

- `flex: initial`

  是把 flex 元素重置为 Flexbox 的**初始值**，它相当于 `flex: 0 1 auto`。

- `flex: auto`

  等同于 `flex: 1 1 auto`。

- `flex: none`

  可以把 flex 元素设置为不可伸缩。它和设置为 `flex: 0 0 auto` 是一样的。

- `flex: <positive-number>`

  常看到的 `flex: 1` 或者 `flex: 2` 等等。它相当于`flex: 1 1 0`。元素可以在`flex-basis`为 0 的基础上伸缩。

# align-items

`align-items` 属性可以使元素在交叉轴方向对齐。

- `stretch`：初始值，最高的元素定义了容器的高度。
- `flex-start`：按 flex 容器的顶部对齐。
- `flex-end`：按 flex 容器的尾部对齐。
- `center`：居中对齐。

# justify-content

`justify-content`属性用来使元素在主轴方向上对齐，主轴方向是通过 `flex-direction` 设置的方向。

- `flex-start`：初始值，元素从容器的起始线排列。

- `flex-end`：从终止线开始排列。
- `center`：在中间排列。
- `space-between`：把元素排列好之后的剩余空间拿出来，平均分配到元素之间，所以元素之间间隔相等。
- `space-around`：使每个元素的左右空间相等。

