---
title: Audio
date: 2022-11-06 15:34:40
categories: web
tags:
- audio
---

# audio元素

**HTML `<audio>` **元素用于在文档中嵌入音频内容。 `<audio>` 元素可以包含一个或多个音频资源，这些音频资源可以使用 `src` 属性或者`<source>` 元素来进行描述：浏览器将会选择最合适的一个来使用。也可以使用 `MediaStream` 将这个元素用于流式媒体。

```html
<audio controls src="/media/cc0-audio/t-rex-roar.mp3">
  <a href="/media/cc0-audio/t-rex-roar.mp3">
    Download audio
  </a>
</audio>
```

在浏览器不支持该元素时，会显示 `<audio></audio>` 标签之间的内容作为回退。

## 属性

- `autoplay`：布尔值属性；声明该属性，音频会尽快自动播放，不会等待整个音频文件下载完成。
- `controls`：如果声明了该属性，浏览器将提供一个包含声音，播放进度，播放暂停的控制面板，让用户可以控制音频的播放。
- `crossorigin`：枚举属性 展示音频资源是否可以通过 `CORS` 加载。支持 `CORS` 的资源可以被 `canvas` 元素复用而不污染。可选值如下：
  - `anonymous`：在发送跨域请求时不携带验证信息。
  - `use-credentials`：在发送跨域请求时携带验证信息。
- `currentTime`：读取 `currentTime` 属性将返回一个双精度浮点值，用以标明以秒为单位的当前音频的播放位置。
- `duration`：只读属性，这是一个双精度浮点数，指明了音频在时间轴中的持续时间（总长度），以秒为单位。
- `loop`：布尔属性；如果声明该属性，将循环播放音频。
- `muted`：表示是否静音的布尔值。默认值为 `false`，表示有声音。
- `perload`：枚举属性，让开发者自行思考来示意浏览器使用何种加载方式以达到最好的用户体验。可以是以下属性之一：
  - `none`: 示意用户可能不会播放该音频，或者服务器希望节省带宽；换句话说，该音频不会被缓存；
  - `metadata`: 示意即使用户可能不会播放该音频，但获取元数据 (例如音频长度) 还是有必要的。
  - `auto`: 示意用户可能会播放音频；换句话说，如果有必要，整个音频都将被加载，即使用户不期望使用。
  - *空字符串* : 等效于`auto`属性。不同浏览器会有自己的默认值，规范建议的默认值为 `metadata`。
- `src`：嵌入的音频的 URL。这是一个可选属性，你可以在 audio 元素中使用 `source` 元素来替代该属性指定嵌入的音频。

使用内嵌的 `source` 元素提供不同的播放源。浏览器会使用第一个它支持的播放源：

```html
<audio controls>
  <source src="myAudio.mp3" type="audio/mpeg">
  <source src="myAudio.ogg" type="audio/ogg">
  <p>Your browser doesn't support HTML5 audio. Here is
     a <a href="myAudio.mp4">link to the audio</a> instead.</p>
</audio>
```

# Web Audio

`Web Audio API` 提供了在 Web 上控制音频的一个非常有效通用的系统，允许开发者来自选音频源，对音频添加特效，使音频可视化，添加空间效果（如平移），等等。



TODO





参考：

- [`<audio>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/audio)
- [Web Audio API](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Audio_API)

