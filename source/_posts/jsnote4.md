---
title: javascript学习笔记（四）-- 闭包
date: 2019-03-12 19:58:21
tags: javascript 闭包
categories: 前端 javascript 
---

### 定义
> 闭包是函数和声明该函数的词法环境（也称作用域）的组合。

```
function makeFunc() {
	var name = 'outer';
	function displayName() {
		alert(name);
	}
	return displayName;
}
var myFunc = makeFunc();
myFunc();
```

javascript中的函数会形成闭包，这个闭包的环境包含了该闭包创建时所能访问到的所有局部变量。

### 闭包模拟私有方法

闭包很有用，因为它允许将函数与其所操作的某些数据（环境）关联起来。使用闭包可以模拟私有方法，私有方法不仅有利于限制对代码的访问，还提供了管理全局命名空间的强大能力，避免非核心的方法弄乱了代码的公共接口部分。

```
var makeCounter = function() {
	var count = 0;
	function changeBy(val) {
		count += val;
	}
	return {
		increment: function() {
			changeBy(1);
		},
		decrement: function() {
			changeBy(-1);
		},
		value: function() {
			return count
		}
	}
};

var counter1 = makeCounter();
var counter2 = makeCounter();

counter1.increment();
counter1.value(); //1

counter1.increment();
counter1.value(); //2

counter1. decrement();
counter1.value(); //1

counter2.value(); //0
```

由此可以看出，counter1中的三个私有方法共享同一个闭包，但是counter1和counter2之间的闭包是互相独立的。

### 循环中常见的闭包错误

