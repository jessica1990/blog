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

```
function fn () {
  for (var i = 0; i < 10; i++) {
    setTimeout(function(){
      console.log(i)
    }, 10)
  }
}
fn();
```
上述代码不会输出期望的0-9，而是输出10个10。
原因是这里的匿名函数与声明该函数的词法环境形成了闭包，10个闭包在循环中被创建，并且他们共享了同一个词法作用域，这个作用域中存在一个变量i，当setTimeout结束开始执行其回调的匿名函数时，i已经++到了10。

#### 如何解决该错误
解决该错误的实质就是要让多个闭包不再共享同一个词法环境，所以那就在创建闭包之前给它来个自己的词法环境，而创建函数就可以生成新的词法环境。所以可以按照以下方法解决问题：

```
function fn () {
  for (var i = 0; i < 10; i++) {
    uniq(i)
  }
}
function uniq (i) {
  setTimeout(function(){
    console.log(i)
  }, 10)
}
fn();
```

当然，这里的具名函数换成匿名自执行函数也是可以的。

```
function fn () {
  for (var i = 0; i < 10; i++) {
    (function (e) {
      setTimeout(function(){
        console.log(e)
      }, 10)
    })(i)
  }
}
fn();
```

当然最简单的是es6的let带来的块作用域，这样多个闭包的词法环境也是独立的。

### 性能思考

若不是某些特定任务需要使用闭包的话，在其他函数中创建函数是不明智的，因为闭包在处理速度和内存消耗方面对脚本性能有负面影响。

如：在给对象创建方法时，方法通常要关联到对象的原型，而不是定义到对象的构造器中，因为如果定义到对象的构造器中，每次new的时候方法都会被重新赋值一次。这样的情况下不推荐闭包，而应该使用原型继承。