---
title: HTML5和CSS3新特性
date: 2022-12-12 12:48:14
categories: 前端
tags:
- HTML5
- CSS3
---

# HTML5

## 语义化标签

- `header`: 描述了文档的头部区域，于定义内容的介绍展示区域。
- `nav`：定义导航链接的部分。
- `section`：定义文档中的节（section、区段）。比如章节、页眉、页脚或文档中的其他部分，section通常包含了⼀组内容及其标题。
- `article`：定义独立的内容。
- `aside`：定义页面主区域内容之外的内容（比如侧边栏）。
- `figure`：标签规定独立的流内容（图像、图表、照片、代码等等）。
- `figcaption`：定义`figure`元素的标题。
- `main`：`main`元素代表文档的主内容区，它应当与文档直接相关，或者是文档的中心主题的扩展。一个页面中只能有一个`main`元素，不能将 `main`元素放在 `article`、`aside`、`header`、`footer`、`nav` 元素的里面，即`main`元素的级别不能低于这些元素。

## 内容标签

- `mark`：定义带有记号的文本，请在需要突出显示文本时使用`mark`标签。
- `progress` ：定义运行中的任务进度，与 `JavaScript` 一起使用来显示任务的进度。

## 新的input类型

- `color`：定义拾色器。
- `datetime-local/datetime`：定义日期字段（带有 calendar 和 time 控件）。

- `search`：定义用于搜索的文本字段。
- `email`：用于应该包含 e-mail 地址的输入域。在提交表单时，会自动验证 email 域的值。
- `url`：用于应该包含 URL 地址的输入域。在提交表单时，会自动验证 `url` 域的值。
- `number`：`number` 类型用于应该包含数值的输入域。

## [Web存储](https://cjhsyc.github.io/2022/10/23/cookie%E3%80%81session%E5%92%8C%E6%9C%AC%E5%9C%B0%E5%AD%98%E5%82%A8/)

- `localstorage`为永久储存
- `sessionstorage`为临时储存

## 多媒体

- `video`：定义视频，比如电影片段或其他视频流。默认情况下不允许用户自己控制播放停止。
- `audio`：定义声音，比如音乐或其他音频流。
- `source`：为媒介元素（比如 `video` 和 `audio`）定义媒介资源，允许您规定可替换的视频/音频文件供浏览器根据它对媒体类型或者编解码器的支持进行选择。
- `track`：为诸如 `video` 元素之类的媒介规定外部文本轨道。
- `embed`：定义嵌入的内容，比如插件。

# CSS3

- 颜色：新增`RGBA`，`HSLA`模式

- ```css
  .red{background-color: red}
  .red_hex{background-color: #ff0000}
  .red_rgb{background-color: rgb(255,0,0);}
  .red_hsl{background-color: hsl(0,100%,54%);}
  ```

- 文字阴影：`text-shadow`

- 边框：新增圆角（`border-radius`）、边框阴影（`box-shadow`）、`border-image`

- 盒子模型：`box-sizing`

  ```css
  //默认值 内容真正宽度 = 设置的宽度
  box-sizing: content-box; 
  // 内容真正宽度width = 设置的width - 左右padding - 左右border
  box-sizing: border-box; 
  // 规定从父元素继承此值
  box-sizing: inherit;
  ```

- 背景：`background-size` 设置背景图片的尺寸；`background-origin` 设置背景图片的原点；background-clip 设置背景图片的裁切区域

- 渐变：`linear-gradient`、`radial-gradient`

- 过渡：`transition`

- 动画：`animation`

- 转换：`transform`

- 媒体查询

## 新增选择器

### 属性选择器

1. `[attribute^=value]` 匹配属性值以指定值开头的每个元素。

2. `[attribute$=value]` 匹配属性值以指定值结尾的每个元素。

3. `[attribute*=value]` 匹配属性值中包含指定值的每个元素。

### 层级选择器

`E~F`：选择匹配的F元素，且位于匹配的E元素后的所有匹配的F元素

### 伪元素和伪类选择器

| 选择器                 | 示例                  | 示例说明                                                  |
| ---------------------- | --------------------- | --------------------------------------------------------- |
| :first-of-type         | p:first-of-type       | 选择每个p元素是其父级的第一个p元素                        |
| :last-of-type          | p:last-of-type        | 选择每个p元素是其父级的最后一个p元素                      |
| :only-of-type          | p:only-of-type        | 选择每个p元素是其父级的唯一p元素                          |
| :only-child            | p:only-child          | 选择每个p元素是其父级的唯一子元素                         |
| :nth-child(*n*)        | p:nth-child(2)        | 选择每个p元素是其父级的第二个子元素                       |
| :nth-last-child(*n*)   | p:nth-last-child(2)   | 选择每个p元素的是其父级的第二个子元素，从最后一个子项计数 |
| :nth-of-type(*n*)      | p:nth-of-type(2)      | 选择每个p元素是其父级的第二个p元素                        |
| :nth-last-of-type(*n*) | p:nth-last-of-type(2) | 选择每个p元素的是其父级的第二个p元素，从最后一个子项计数  |
| :last-child            | p:last-child          | 选择每个p元素是其父级的最后一个子级。                     |
| :root                  | :root                 | 选择文档的根元素                                          |
| :empty                 | p:empty               | 选择每个没有任何子级的p元素（包括文本节点）               |
| :target                | #news:target          | 选择当前活动的#news元素（包含该锚名称的点击的URL）        |
| :enabled               | input:enabled         | 选择每一个已启用的输入元素                                |
| :disabled              | input:disabled        | 选择每一个禁用的输入元素                                  |
| :checked               | input:checked         | 选择每个选中的输入元素                                    |
| :not(*selector*)       | :not(p)               | 选择每个并非p元素的元素                                   |
| ::selection            | ::selection           | 匹配元素中被用户选中或处于高亮状态的部分                  |
| :out-of-range          | :out-of-range         | 匹配值在指定区间之外的input元素                           |
| :in-range              | :in-range             | 匹配值在指定区间之内的input元素                           |
| :read-write            | :read-write           | 用于匹配可读及可写的元素                                  |
| :read-only             | :read-only            | 用于匹配设置 "`readonly`"（只读） 属性的元素              |
| :optional              | :optional             | 用于匹配可选的输入元素                                    |
| :required              | :required             | 用于匹配设置了 "`required`" 属性的元素                    |
| :valid                 | :valid                | 用于匹配输入值为合法的元素                                |
| :invalid               | :invalid              | 用于匹配输入值为非法的元素                                |



参考：

- [HTML5和CSS3新特性](https://blog.csdn.net/m0_60263299/article/details/124724891)
- [css3有哪些新增的选择器](https://www.php.cn/website-design-ask-485901.html)
