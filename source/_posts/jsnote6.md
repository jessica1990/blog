---
title: jsnote6
date: 2019-07-11 15:06:32
tags: 高阶函数
categories: js
---

## 高阶函数

### 概念
高阶函数（Higher-order function）：一个函数可以接收另一个函数作为参数，这种函数就称为高阶函数。

### map

*不改变原数组*

该函数定义在**Array**上，通过调用array的**map**方法，传入自定义函数并**return所需结果**，就可以得到新的array作为结果。注意：**map()**传入的参数是函数对象本身，比如**double**，而不是**double()**。

``` js
/*
	作为参数的函数的参数
	@params currentValue 必需。当前元素。
	@params index 可选。当前元素的索引。
	@params arr 可选。当前元素所属的数组对象。
	
	map方法除了函数参数外的另一个可选参数
	@params thisValue 可选。对象作为该执行回调时使用，传递给函数，用作 "this" 的值。如果省略了 thisValue，或者传入 null、undefined，那么回调函数的 this 为全局对象。
	
	语法如下代码所示
*/
array.map(function(currentValue,index,arr), thisValue)

function double(x) {
    return x * 2;
}

var arr = [1, 2, 3, 4, 5, 6, 7, 8, 9];
var results = arr.map(double); // [2, 4, 6, 8, 10, 12, 14, 16, 18]
```

**map()**作为高阶函数，事实上是把运算规则抽象和简化了（因为即使不用map方法，使用for循环也完全可以实现上述需求，只是写起来更繁琐而已）

### reduce

*不改变原数组*

该函数定义在**Array**上，**Array**的**reduce()**把一个函数（即参数函数）作用在这个**Array**的**[x1, x2, x3...]**上，参数函数必须接受至少两个参数，**reduce()**把结果继续和array的下一个结果做累积计算。

``` javascript
/*
	作为参数的函数的参数
	@params total	必需。初始值, 或者计算结束后的返回值。
	@params currentValue 必需。当前元素。
	@params currentIndex 可选。当前元素的索引。
	@params arr 可选。当前元素所属的数组对象。
	
	reduce方法除了函数参数外的另一个可选参数
	@params initialValue 可选。传递给函数的初始值。
	
	语法如下代码所示
*/
array.reduce(function(total, currentValue, currentIndex, arr), initialValue)

// 效果类似于
[x1, x2, x3, x4].reduce(fn) = fn(fn(fn(fn(x1,x2)),x3),x4)
```

举例如下：

``` js
var arr = [1, 3, 5, 7, 9];
var add = function(x, y) {
	return x + y;
}
arr.reduce(add); //25
arr.reduce(add, 20); //45

// 字符串转数字，不使用parseInt和Number
var strToInt = function(s) {
	return s.split('').map(x => +x).reduce((x, y) => {		return x * 10 + y;
	})
}
strToInt('123'); // 123
```

### filter

*不改变原数组*

用于把**Array**的某些函数过滤掉，然后返回剩下的元素。和**map()**类似，Array的**filter()**也接收一个函数。和**map()**不同的是，**filter()**把传入的函数依次作用于每个元素，然后根据**返回值是true还是false**决定保留还是丢弃该元素。

```
/*
	作为参数的函数的参数
	@params currentValue 必需。当前元素。
	@params index 可选。当前元素的索引。
	@params arr 可选。当前元素所属的数组对象。
	
	filter方法除了函数参数外的另一个可选参数
	@params thisValue 可选。对象作为该执行回调时使用，传递给函数，用作 "this" 的值。如果省略了 thisValue ，"this" 的值为 "undefined"
	
	语法如下代码所示
*/
array.filter(function(currentValue,index,arr), thisValue)
```