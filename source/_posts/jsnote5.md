---
title: javascript学习笔记（五）-- 作用域
date: 2019-05-05 16:52:51
tags: javascript 作用域
categories: javascript
---

### javascript 作用域

#### 变量提升

**先提升变量，然后函数提升，然后赋值**，函数的优先级会高于变量。

```
(function(){
  console.log(a);
  function a(){}
  var a = 5;
  console.log(a);
  function b(){}
  var b;
  console.log(b);
  b = 9;
  var c = d = b;
}())
console.log(d);
console.log(c);
```