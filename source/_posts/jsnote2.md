---
title: javascript学习笔记（二）-- this, call, apply 和 bind
date: 2019-02-18 19:58:21
tags: javascript this
categories: 前端 javascript 
---

### this
**this**是函数运行时，在函数体内部自动生成的一个对象，是函数运行时所在的环境对象，只能在函数体内部使用。存在以下四种情况：

#### 1. 纯粹的函数调用
这是函数的通常用法，属于全局性调用，因此这里的**this**代表全局对象。

```
var x = 1;
function test() {
	console.log(this.x); 
}
test(); // 1
```

#### 2. 作为对象方法的调用
作为某个对象的方法时，**this**就指向这个对象
 
```
function test() {
	console.log(this.x);
}
var obj = {};
var x = 1;
obj.x = 2;
obj.test = test;
 
obj.test(); // 2
test(); // 1
```
 
#### 3. 作为构造函数调用
所谓构造函数，就是通过这个函数可以生成一个新的对象，这时**this**指向这个新对象。

```
var x = 2;
function test() {
	this.x = 1;
}

var obj = new test();
obj.x // 1
x  // 2
```

x的值还是为2，表明全局变量**x**的值没变。

#### 4. apply调用
**apply()**是函数的一个方法，作用是改变函数的调用对象。它的第一个参数表示改变后的调用这个函数的对象，因此这时的**this**就指向第一个函数。第一个参数为空时，默认为全局对象，**this**就指向全局对象。

```
var x = 0;
function test() {
	console.log(this.x);
}

var obj = {};
obj.x = 1;
obj.m = test;
obj.m.apply() // 0 this指向全局对象
obj.m.apply(obj); //1 this指向obj
```