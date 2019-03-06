---
title: 「js高程」笔记（1）
date: 2019-03-06 19:32:39
tags: javascript DOM级别
categories: javascript 
---

### javascript组成
1. 核心（ECMAScript）：对实现ES标准规定的各个方面（语法、数据类型、语句、关键字、保留字、操作符、对象）内容的语言的描述

2. 文档对象模型（DOM）：针对XML，但经过扩展后可用于HTML的应用程序编程接口（API）。它将整个页面映射为一个多层节点结构。开发人员可以通过操作DOM来控制页面

3. 浏览器对象模型（BOM）：可通过BOM访问访问和操作浏览器窗口。从根本上讲BOM只处理浏览器窗口和框架，但习惯上也把针对浏览器的javascript扩展算作BOM的一部分，如：alert，confirm，close，open，navigator，location，screen，对cookies的支持，XMLHttpRequest和ActiveXObject这样自定义的对象

### DOM级别
##### 1. DOM1级
其目标主要是映射文档结构

##### 2. DOM2级
在原来的基础上添加了以下新模块：
1. DOM视图（DOM Views）：定义了跟踪不同文档视图（如应用css之前和之后的文档）的接口
2. DOM事件（DOM Events）：定义了事件和事件处理的接口
3. DOM样式（DOM Styles）：定义了基于CSS为元素应用样式的接口
4. DOM遍历和规范（DOM Traversal and Range）：定义了遍历和操作文档树的接口

##### 3. DOM3级
进一步扩展了DOM，引入了以统一方式加载和保存文档的方法--在DOM加载和保存（DOM Load and Save）模块中定义；新增了验证文档的方法--在DOM验证（DOM Validation）模块中定义。也对DOM核心进行了扩展，开始支持XML1.0规范，涉及XML Infoset、XPath和XML Base


### 总结
javascript是一种专为网页交互而设计的脚本语言，由下面三个不同部分组成：

**ECMAScript**，由ECMA-262定义，提供核心语言功能

**DOM**，提供访问和操作网页内容的方法和接口

**BOM**，提供与浏览器交互的方法和接口
