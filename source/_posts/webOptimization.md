---
title: 前端性能优化及在项目中的实践
date: 2019-04-09 11:57:06
tags: 性能优化
categories: 前端
---

### 前言
首先思考 从输入 URL 到页面加载完成，发生了什么？

* 1. DNS 解析
* 2. TCP连接
* 3. HTTP请求
* 4. HTTP响应
* 5. 浏览器得到响应数据、解析、渲染

由此可知，可以从以上几个过程中考虑如何优化。而前两步的优化往往需要服务器端developer协作完成，前端单方面的可作为空间有限，那么就从http请求到得到相应渲染页面这一过程来考虑前端性能优化。

### HTTP请求
优化方向为以下两种：

* 减少请求次数  --  资源合并
* 减少单次请求所花费的时间  --  资源压缩

以上两个方向直指资源的合并与压缩，也就是构建工具webpack做的事情。可通过webpack以及相关插件进行优化。

#### javascript/css相关--构建层面

* 利用CommonsChunkPlugin（webpack 4中被optimization.splitChunks 和 optimization.runtimeChunk替代）实现分包，把公用代码抽取出来，利用浏览器缓存来避免多次请求
* 利用DllPlugin拆分资源
* 利用Tree-Shaking删除冗余代码

#### javascript/css相关--代码层面
* link css放到head中，因为CSSOM和DOM都构建完成才能合力构建渲染树，将css放到head就是为了尽早构建CSSOM，防止阻塞渲染
* 首屏快速显示可使用critical css，即 将首屏用到的css单拎为**style**标签放到**head**，其他css后置并设置**rel="preload"**属性，**rel="preload"**通知浏览器开始获取非关键CSS以供之后用。其关键在于，**preload**不阻塞渲染，无论资源是否加载完成，浏览器都会接着绘制页面，可以使用Node.js的模块[Critical](https://github.com/addyosmani/critical)来实现
* javascript会阻塞浏览器，因此要放到html尾部，或者按需使用defer（异步加载，整个文档解析完、DOMContentLoaded事件即将被触发时执行）或async（异步加载，该文件加载结束后立即执行）。实际上并不是javascript阻塞了浏览器，而是因为js引擎是独立于渲染引擎存在的，遇到js时，浏览器会将控制权交给js引擎，渲染引擎没权了自然无法继续工作
* 异步模块加载，如将vue-router中的component从方案一改为方案二（利用function形式，在用到的时候返回相关组件）

```
//方案一
import Home from '@/view/home/index'
...
component: Home

//方案二
const Home = () => import('@/view/home/index');
...
component: Home
```

#### 图片相关

首先看下不同类型的图片的特性：

##### JPG
* 优点：最大的特点是有损压缩，压缩至原有体积的**50%**以下时，JPG 仍然可以保持住**60%**的品质，JPG 格式以 24 位存储单个图，可以呈现多达 1600 万种颜色，足以应对大多数场景下对色彩的要求，这一点决定了它压缩前后的质量损耗并不容易被我们人类的肉眼所察觉。
* 缺点：处理线条感强或者颜色对比强烈的图像时会有明显的模糊，且不支持透明度处理
* 适用场景：大的背景图、轮播图或 Banner 图

##### PNG
* 优点：PNG是无损压缩的高保真的图片格式，比 JPG 更强的色彩表现力，对线条的处理更加细腻，对透明度有良好的支持
* 缺点：体积太大
* 适用场景：小的 Logo、颜色简单且对比强烈的图片或背景

##### SVG
* 优点：**文本文件、体积小、图片可无限放大而不失真、兼容性好，可压缩性更强**。svg 基于 XML 语法的图像格式，与 jpg 和 png 不同处在于，svg 不是基于像素点，而是是基于对图像的形状描述。
* 缺点：渲染成本比较高，有学习成本（因为它是可编程的）
* 适用场景：常用于简单图像，需要修改或编辑等灵活使用的场景

##### Base64
* 优点：**文本文件、依赖编码、小图标解决方案**，Base64 并非一种图片格式，而是一种编码方式，浏览器可直接解析，无需发送http请求
* 缺点：通过base64编码后，图片大小会变为原来的4/3
* 适用场合：2kb以下的小图，更新频率很低，无法以雪碧图形式使用

##### WebP
* 优点：分无损压缩和有损压缩，支持透明，支持动图，同等质量下size更小
* 缺点：兼容性差，增加服务器负担（与jpg相比，编码同质量的webP文件会占用更多的计算资源）
* 适用场景：不需兼容，或做降级处理（修改图片后缀，支持webP的使用webP，不支持的使用其他常规图片）

##### 相关性能优化
* 为了体验好，可以将轮播图的首屏以base64形式展示，浏览器可直接解析无需http请求
* 多个小图拼接雪碧图，减少http请求
* 保证视觉无影响的情况下压缩图片到最小
* 上传至服务端的图按照具体场景选择不同的质量压缩，如上传合同照片需要清晰度较高故质量指数要高，上传小头像则无需很高的质量指数
* 多图时懒加载，如类似相册模式浏览图片时，只给当前图及其后3个依次添加src属性，避免一次请求过多的图片，多排图片竖屏滚动时，只在图片进入或即将进入视口时请求图片
* 合适的场景用合适的图片格式，如大图、轮播图用jpg等，避免无谓的浪费

#### http携带数据相关

* Gzip 压缩：HTTP 压缩的一种，对HTTP内容进行重新编码的过程。Gzip 压缩背后的原理，是在一个文本文件中找出一些重复出现的字符串、临时替换它们，从而使整个文件变小。
* cookie 中仅携带与服务器交互用的数据，纯存储的数据交给webStorage
* 静态资源放到cdn上，除了能更快获取到资源外，还能避免不需要的cookie飞来飞去

#### 页面数据存储相关
* 利用浏览器缓存（Memory Cache，Service Worker Cache，HTTP Cache，Push Cache）
* 利用本地存储（cookie，4k，来往于C/S；sessionStorage，5-10M，会话级别；localStorage，5-10M，永久存储；indexDB，无上限，永久存储）

### 渲染

#### 浏览器运行机制

* HTML parse：解析html文档输出**DOM树**，并在此过程中发出了页面渲染所需**外部资源的http请求**
* Style：识别和加载CSS，解析CSS形成**CSSOM**，**CSSOM**的解析过程与**DOM**的解析过程是并行的，二者都解析完毕后形成**渲染树**
* Layout：计算页面中所有元素的位置信息和大小信息
* Paint：将每个页面图层转换成像素，并对所有的媒体文件进行解码
* Composit：合并各个图层，将数据由CPU输出给GPU绘制到屏幕上

之后每当有新元素加到DOM树种，浏览器会通过CSS引擎查询CSS样式表，找到符合的样式应用到该元素然后重新绘制。由此可以理出第一个可优化的点--CSS样式表优化以使其加快查询速度，此处要注意，CSS是从右往左匹配的。因此CSS代码方面可有以下优化点：

* 避免使用通配符
* 尽量避免使用标签选择器
* 避免重复匹配和重复定义
* 减少嵌套

此外，由于js引擎和渲染引擎是独立的，二者要跨界交流就会依赖桥接接口，所以当js操作引起dom变化时，过桥费很昂贵，频繁过桥就愈加昂贵，所以应该避免不必要的过桥，要懂得让**js为DOM分压**，[DOM Fragment](https://developer.mozilla.org/zh-CN/docs/Web/API/DocumentFragment)就是这个思路。综上，相关的优化有以下几点：

* 避免频繁操作DOM、过渡渲染

```
//避免以下方式
for(let i = 0; i < 100; i++) {
	document.getElementById('test').innterHTML += '<span>test</span>'
}

//推荐以下方式
let test = document.getElementById('test'); //缓存结果，减少调用DOM接口就省了过桥费
let content = '';
for(let i = 0; i < 100; i++) {
	content += '<span>test</span>'
}
test.innerHTML = content; //减少DOM更改，可减少由此触发的重绘或回流
```

上文提到的重绘和回流：
> *重绘*：当我们对 DOM 的修改导致了样式的变化、却并未影响其几何属性（比如修改了颜色或背景色）时，浏览器不需重新计算元素的几何属性、直接为该元素绘制新的样式

> *回流*：也叫重排，当我们对 DOM 的修改引发了 DOM 几何尺寸的变化（width、height、padding、margin、left、top、border等）、或增减元素时，浏览器需要重新计算元素的几何属性（其他元素的几何属性和位置也会因此受到影响），然后再将计算的结果绘制出来。用到这些属性offsetTop、offsetLeft、 offsetWidth、offsetHeight、scrollTop、scrollLeft、scrollWidth、scrollHeight、clientTop、clientLeft、clientWidth、clientHeight时，也会立即触发回流。

因此**避免重绘和回流**也是经典的优化点，如何避免呢？

* 将上述的会触发回流的属性缓存起来，避免不必要的重复触发
* 避免逐条改动样式，最好通过class一次性改动
* 有复杂改动时将DOM离线，即将DOM先隐藏，复杂改变结束后再显示
* 需要频繁变更的元素使用脱离文档流的样式

此外，针对操作或其他的**节流和防抖**也是十分非常特别相当必要的。
> *节流*：在某段时间内，不管你触发了多少次回调，都只认第一次，并在计时结束时给予响应

```
function throttle(fn, delay = 0) {
  let timer = null;
  return function() {
    let context = this;
    if(!timer) {
      timer = setTimeout(() => {
        timer = null;
      }, delay)
      return fn.apply(context, arguments) // 加return是因为fn可能会有返回值
    }
  }
}
```

> *防抖*：在某段时间内，不管你触发了多少次回调，都只认最后一次,并在计时结束时给予响应

```
function debounce(fn, delay = 0) {
  let timer = null;
  return function() {
    if(timer) {
      clearTimeout(timer);
    }
    timer = setTimeout(() => {
      return fn.apply(this, arguments)
    }, delay)
  }
}
```
