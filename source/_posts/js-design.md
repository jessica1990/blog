---
title: js设计模式核心原理（一）
date: 2020-04-07 10:26:06
tags: javascript
---

*修言所书掘金小册[JavaScript 设计模式核⼼原理与应⽤实践](https://juejin.im/book/5c70fc83518825428d7f9dfb)学习笔记，加深印象*

### 设计模式
> 在软件工程中，设计模式是对软件设计中普遍存在或反复出现的各种问题所提出的解决方案。

### 前端核心竞争力
* 能用健壮的代码解决具体的问题
* 能用抽象的思维应对复杂的系统
* 能用工程化的思想规划更大规模的业务

### 知识体系概览
![概览](/img/design.png)

### SOLID设计原则
设计模式的指导理论，帮助规避不良的软件设计。
* 单一功能原则（Single Responsibility Priciple）
* 开放封闭原则（Opened Closed Principle）
* 里式替换原则（Liskov Substitution Principle）
* 接口隔离原则（Interface Segregation Principle）
* 依赖反转原则（Dependency Inversion Principle）

javascript设计模式中主要围绕“单一功能”和“开放封闭”这两个原则展开。

设计模式核心思想 - **封装变化：将变与不变分离，确保变化的部分灵活，不变的部分稳定**。

### 经典的23种设计模式
![23种设计模式](/img/design2.png)

不论创建型、结构型、行为型，都是在用自己的方式去封装**不同类型**的变化

* 创建型：封装创建对象过程中的变化，如工厂模式。它做的事情是**将创建对象的过程抽离**；
* 结构型：封装对象之间组合方式的变化，目的在于灵活地表达对象间的配合与依赖关系；
* 行为型：将对象千变万化的行为进行抽离，确保能安全、方便地对行为（笔者注：对行为还是对对象？）进行更改。