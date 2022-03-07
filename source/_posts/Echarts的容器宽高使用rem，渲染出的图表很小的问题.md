---
title: Echarts的容器宽高使用rem，渲染出的图表很小的问题
date: 2022-03-06 19:54:11
tags: Echarts
---
主要是因为div还没有创建出来echarts就已经加载了，因为获取不到宽高，所以会缩小在一起。
参考了几篇文章，我在echarts配置后加入如下代码，解决了问题：
```javascript
myChart.setOption(option);
 //延迟resize
 setTimeout(function (){
	 myChart.resize()
 },200)
//随屏幕大小改变
 window.addEventListener('resize',function(){
	 myChart.resize()
 })
```
CSDN:https://blog.csdn.net/cjhsyc/article/details/122584331
参考文章：
https://www.cnblogs.com/xxxx0130/p/14182677.html
https://blog.csdn.net/weixin_40180205/article/details/106116073