---
title: 性能优化、Webpack date: "2022-04-02T17:53:03.284Z"
---

## Webpack

[Webpack 教程 - 姜瑞涛的官方网站](https://www.jiangruitao.com/webpack/)
Webpack 自身只支持对 JS 和 JSON 文件处理，

```
  var path = require('path');  

  module.exports = {
    entry: './a.js',
    output: {
      path: path.resolve(__dirname, ''),
      filename: 'bundle.js'
    },
    mode: 'none'
  };

```

从 a.js 开始打包，在根目录输出 bundle.js。

### 入口和出口

### 预处理器 loader

css-loader style-loader

### 插件 Plugin

## 懒加载

也叫延迟加载、按需加载。如果网页中图片很多，用户只能看到视口中的图片，那么就只应该加载那部分图片。

### 特点

1. 减少无用资源加载
2. 提升用户体验
3. 防止加载过多图片导致影响其他资源加载

### 原理

图片的加载是由 src 引起的，当对 src 赋值时，浏览器就会请求图片资源。根据这个原理，我们使用HTML5 的 data-xxx 属性来储存图片的路径，在需要加载图片的时候，将 data-xxx 中图片的路径赋值给
src，这样就实现了图片的按需加载，即懒加载。 注意：data-xxx 中的 xxx 可以自定义，这里我们使用 data-src 来定义。
懒加载的实现重点在于确定用户需要加载哪张图片，在浏览器中，可视区域内的资源就是用户需要的资源。所以当图片出现在可视区域时，获取图片的真实地址并赋值给图片即可。

### 如何判断图片是否加载完成以及加载成功失败

是否加载完成：img 元素的 complete 属性 加载成功失败：

### 如何判断元素是否到达可视区域

window.innerHeight 是浏览器可视区的高度； document.body.scrollTop || document.documentElement.scrollTop 是浏览器滚动的过的距离； imgs.offsetTop
是元素顶部距离文档顶部的高度（包括滚动条的距离）； 内容达到显示区域的：el.offsetTop < window.innerHeight + document.body.scrollTop;

```
function isInViewPortOfOne (el) {
    // viewPortHeight 兼容所有浏览器写法
    const viewPortHeight = window.innerHeight || document.documentElement.clientHeight || document.body.clientHeight 
    const offsetTop = el.offsetTop
    const scrollTop = document.body.scrollTop || document.documentElement.scrollTop
    const top = offsetTop - scrollTop
    return top <= viewPortHeight
}
// 具体代码
<div class="container">
     <img src="loading.gif"  data-src="pic.png">
     <img src="loading.gif"  data-src="pic.png">
     <img src="loading.gif"  data-src="pic.png">
     <img src="loading.gif"  data-src="pic.png">
     <img src="loading.gif"  data-src="pic.png">
     <img src="loading.gif"  data-src="pic.png">
</div>
<script>
var imgs = document.querySelectorAll('img');
function lozyLoad(){
		var scrollTop = document.body.scrollTop || document.documentElement.scrollTop;
		var winHeight= window.innerHeight;
		for(var i=0;i < imgs.length;i++){
			if(imgs[i].offsetTop < scrollTop + winHeight ){
				imgs[i].src = imgs[i].getAttribute('data-src');
			}
		}
	}
  window.onscroll = lozyLoad();
</script>

```

## 图片优化

1. 少用图片，尽可能用 CSS 来代替。
2. 少用原图，根据屏幕尺寸去请求适合的图片。
3. 小图片用 base64 格式。
4. 多个图片文件合成到一张图中（精灵图或称雪碧图），减少请求数。
5. 选择正确的图片格式，支持 WebP 格式的就用这个格式，小图 png 或是用 svg。

## 2. 如何避免回流与重绘？

*减少回流与重绘的措施：*

* 操作 DOM 时，尽量在低层级的 DOM 节点进行操作
* 不要使用 table布局， 一个小的改动可能会使整个 table 进行重新布局
* 使用 CSS 的表达式
* 不要频繁操作元素的样式，对于静态页面，可以修改类名，而不是样式。
* 使用 absolute 或者 fixed，使元素脱离文档流，这样他们发生变化就不会影响其他元素
* 避免频繁操作 DOM，可以创建一个文档片段 documentFragment，在它上面应用所有DOM操作，最后再把它添加到文档中
* 将元素先设置 display: none，操作结束后再把它显示出来。因为在display属性为none的元素上进行的DOM操作不会引发回流和重绘。
* 将DOM的多个读操作（或者写操作）放在一起，而不是读写操作穿插着写。这得益于*浏览器的渲染队列机制*。
  浏览器针对页面的回流与重绘，进行了自身的优化——渲染队列浏览器会将所有的回流、重绘的操作放在一个队列中，当队列中的操作到了一定的数量或者到了一定的时间间隔，浏览器就会对队列进行批处理。这样就会让多次的回流、重绘变成一次回流重绘。
  上面，将多个读操作（或者写操作）放在一起，就会等所有的读操作进入队列之后执行，这样，原本应该是触发多次回流，变成了只触发一次回流。

## 防抖和节流

* 函数防抖是指在事件被触发 n 秒后再执行回调，如果在这 n 秒内事件又被触发，则重新计时。这可以使用在一些点击请求的事件上，避免因为用户的多次点击向后端发送多次请求。
* 函数节流是指规定一个单位时间，在这个单位时间内，只能有一次触发事件的回调函数执行，如果在同一个单位时间内某事件被触发多次，只有一次能生效。节流可以使用在 scroll 函数的事件监听上，通过事件节流来降低事件调用的频率。

### 防抖函数的应用场景

* 按钮提交场景：防⽌多次提交按钮，只执⾏最后提交的⼀次
* 服务端验证场景：表单验证需要服务端配合，只执⾏⼀段连续的输⼊事件的最后⼀次，还有搜索联想词功能

### 节流函数的适⽤场景

* 拖拽场景：固定时间内只执⾏⼀次，防⽌超⾼频次触发位置变动
* 缩放场景：监控浏览器resize
* 动画场景：避免短时间内多次触发动画引起性能问题

### 手写代码

```
// 防抖
function debounce(fn, wait) {
  var timer = null;

  return function() {
    var context = this,
      args = [...arguments];

    // 如果此时存在定时器的话，则取消之前的定时器重新记时
    if (timer) {
      clearTimeout(timer);
      timer = null;
    }

    // 设置定时器，使事件间隔指定事件后执行
    timer = setTimeout(() => {
      fn.apply(context, args);
    }, wait);
  };
}

// 节流 时间戳版
function throttle(fn, delay) {
  var preTime = Date.now();

  return function() {
    var context = this,
      args = [...arguments],
      nowTime = Date.now();

    // 如果两次时间间隔超过了指定时间，则执行函数。
    if (nowTime - preTime >= delay) {
      preTime = Date.now();
      return fn.apply(context, args);
    }
  };
}

// 节流 定时器版
function throttle (fun, wait){
  let timeout = null
  return function(){
    let context = this
    let args = [...arguments]
    if(!timeout){
      timeout = setTimeout(() => {
        fun.apply(context, args)
        timeout = null 
      }, wait)
    }
  }
}
```

# 前端面试