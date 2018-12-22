---
title: cordova+vue开发app
date: 2018-12-18 18:25:00
tags: cordova vue 混合开发
categories: 前端 工具
---

### cordova 是什么？
可参考[官网](https://cordova.apache.org)
>Apache Cordova is an open-source mobile development framework. It allows you to use standard web technologies - HTML5, CSS3, and JavaScript for cross-platform development. Applications execute within wrappers targeted to each platform, and rely on standards-compliant API bindings to access each device's capabilities such as sensors, data, network status, etc.

适用什么场景呢？
>Use Apache Cordova if you are:

>* a mobile developer and want to extend an application across more than one platform, without having to re-implement it with each platform's language and tool set.

>* a web developer and want to deploy a web app that's packaged for distribution in various app store portals.

>* a mobile developer interested in mixing native application components with a WebView (special browser window) that can access device-level APIs, or if you want to develop a plugin interface between native and WebView components.

简单来说，cordova的优势在于，仅需要开发维护一套前端代码即可生成多端（android ios等）运行的app，并且有相应的热更新插件，可以只在某些时刻（某些时刻是哪些后面说）在应用商店上传更新app，其他时刻就能随时更新app而无需经过繁琐的应用商店。

### cordova怎么用？

下面就通过一个例子来介绍 **cordova + vue生成app** 从无到有！

#### 1. 首先创建cordova工程
```
npm install -g cordova
cordova create helloApp
```
![cordova工程目录](https://github.com/jessica1990/blog/blob/master/source/img/helloCordova.png?raw=true)
#### 2. 创建vue工程，有了脚手架之后很简单、网上也有很多相关文章，不细说直接命令行建工程，如下：
```
npm install webpack webpack-cli -g
npm install vue-cli -g
vue init webpack hellovue
```
![vue工程目录](https://github.com/jessica1990/blog/blob/master/source/img/helloVue.png?raw=true)
#### 3. 修改vue工程部分配置，如下所示：
* 在index.html的head中添加meta

```
<meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: https://ssl.gstatic.com 'unsafe-eval'; style-src 'self' 'unsafe-inline'; media-src *; img-src 'self' data: content:;">
```
添加[Content-Security-Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy)主要是为了防止跨域脚本攻击，可不加，但建议加上。
加上之后如果有其他域名的资源引入需要在这里声明，否则无法引入。

然后引入cordova.js，引入后body变成下面这样：

```
<body>
    <div id="app"></div>
    <script type="text/javascript" src="cordova.js"></script>
    <!-- built files will be auto injected -->
</body>
```

* 修改src/main.js，修改后如下：

```
// The Vue build version to load with the `import` command
// (runtime-only or standalone) has been set in webpack.base.conf with an alias.
import Vue from 'vue'
import App from './App'
import router from './router'

Vue.config.productionTip = false

/* eslint-disable no-new */
document.addEventListener('deviceready', function() {
  	new Vue({
		el: '#app',
		router,
		store,
		template: '<App/>',
		components: { App }
  	})
}, false);
```
这里的 deviceready 主要是为了能在设备准备好后创建vue实例，以便可以正常使用cordova提供的API。

* 然后修改config/index.js中的build对象，修改前：

```
// Template for index.html
index: path.resolve(__dirname, '../dist/index.html'),

// Paths
assetsRoot: path.resolve(__dirname, '../dist'),
```
修改后：

```
// Template for index.html
index: path.resolve(__dirname, '../www/index.html'),

// Paths
assetsRoot: path.resolve(__dirname, '../www'),
```
即 将dist改为www，这样改是为了后面将两个工程合并起来好管理。

#### 4. 将两个工程活生生合并到一起，合并后目录如下：
![合并后工程目录](https://github.com/jessica1990/blog/blob/master/source/img/helloApp.png?raw=true)

合并时会遇到同名冲突，vue项目的www包可以直接盖掉cordova工程的www包，而两个工程的package.json则要merge一下。

#### 5. 打包app
在合并后的cordova工程目录下执行以下命令，最初版的app就诞生啦！

```
cordova platform add ios
cordova build ios
```
在platforms/ios目录下双击.xcworkspace文件用xcode打开，启用模拟器或用数据线连接手机点击运行按钮app就启动了。

### 如何持续开发
待续···
### 如何添加热更新
待续···

