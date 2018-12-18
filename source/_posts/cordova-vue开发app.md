---
title: cordova+vue开发app
date: 2018-12-18 18:25:00
tags: cordova vue 混合开发
categories: 前端 工具
---

# cordova + vue 混合开发

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


