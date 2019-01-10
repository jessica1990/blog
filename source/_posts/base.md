---
title: javascript学习笔记（一）-- 调用堆栈
date: 2019-1-9 15:16:09
tags: javascript 调用堆栈 执行机制
categories: 前端 javascript 
---

### javascript引擎 -- 最流行的v8引擎
1. 使用在chrome和node中
2. 由Memory Heap（内存堆）和Call Stack（调用栈）组成
		内存堆：内存分配发生的地方 
		调用栈：代码执行的地方


### 调用栈（执行栈）
javaScript是单线程语言，他只有一个调用栈，因此它同一时间只能做一件事。
	
	1. 调用栈或说执行栈是一种LIFO（后进先出）数据结构，它记录了我们在程序中的位置。用来存储代码运行时创建的所有执行上下文。如果运行到一个function，它会将其放到栈顶，当从这个function返回时，就会将这个function从栈顶弹出。这就是调用栈做的事情。
	2. 当调用栈中放入的function数大于调用栈的最大size时，就会发生堆栈溢出。
	3. 调用栈中的函数调用需要大量时间处理，当调用栈有函数要执行时，浏览器就不能做任何事，它被堵塞住，这就意味着浏览器不能渲染也不能运行其他代码。并且一旦浏览器开始调用栈中的众多任务时可能会停止响应。

### 执行上下文
评估和执行javascript代码的环境的抽象概念。每当javascript代码再运行的时候，他都是在执行上下文中运行。有三种，如下：

	1. 全局执行上下文：即默认或基础的上下文，任何不在函数内部的代码都在全局上下文中。他会执行两件事：创建一个全局的window对象（浏览器情况下），并且设置this的值等于这个全局对象。一个程序中只有一个全局上下文。
	2. 函数执行上下文：每当一个函数被调用时，都会为该函数创建一个新的上下文。每个函数都有自己的执行上下文，不过是在函数被调用时创建的。函数上下文可以有任意多个。每当一个新的执行上下文被创建，它会按照定义的顺序执行一系列步骤。
	3. Eval函数执行上下文：执行在eval函数内部的代码也会有它属于自己的执行上下文
***
	当javascript引擎第一次遇到你的脚本时，它会创建一个全局的执行上下文，并且压入当前执行栈。每当引擎遇到一个函数调用，它会为该函数创建一个新的执行上下文并压入栈顶。
	
	引擎会执行那些位于栈顶的函数，当该函数执行结束时，执行上下文从栈顶弹出，控制流程到达当前栈中的下一个上下文。
	
### 如何创建执行上下文
* 创建阶段
	1. this值的决定，即熟知的**this绑定**
		* 在全局执行上下文中，**this**指向全局对象，若在浏览器中，**this**指向window
		* 在函数执行上下文中，**this**的值取决于该函数是如何被调用的。若被一个引用对象调用，则**this**会指向改对象，否则**this**指向全局对象或undefined（严格模式下）
	
	2. 创建**词法环境**组件
	> 词法环境是一种规范类型，基于ECMAScript代码的词法嵌套结构来定义**标识符**和具体变量和函数的关联。一个词法环境由**环境记录器**和一个可能的引用**外部词法环境**的空值组成。

		简单来说，**词法环境**是一宗持有**标识符-变量映射**的结构。**标识符**是指变量或函数的名字，**变量**是对之际对象或原始数据的引用。
		  
		**环境记录器**是存储变量和函数声明的实际位置。
		
		**外部环境的引用**意味着它可以访问其父级词法环境（作用域）
		
		**词法环境**有两种类型：  
			
		* **全局环境**（在全局执行上下文中）是没有外部环境引用的词法环境。全局环境的外部环境引用是**null**。它拥有内建的Object Array等在环境记录器内的原型函数，还有任何用户定义的全局变量，并且**this**的值指向全局对象。
	
		* 在**函数环境**中，函数内部用户定义的变量存储在环境记录器中，并且引用的外部环境可能是全局环境，或者任何包含此内部函数的外部函数。

		**环境记录器**也有两种类型：
		
		1. **声明式环境记录器**存储变量、函数和参数（对应函数环境）
		2. **对象环境记录器**用来定义出现在**全局上下文**中的变量和函数的关系（对应全局环境）
	
	3. 创建**变量环境**组件

		**变量环境**也是一个词法环境，词法环境组件和变量环境的一个不同就是前者被用来存储函数声明和变量（let 和 const）绑定，而后者只用来存储 var 变量绑定。
		
		通过样例代码理解上面的概念：
		
		```
		let a = 20;
		const b = 30;
		var c;

		function multiply(e, f) {
 			var g = 20;
 			return e * f * g;
		}

		c = multiply(20, 30);

		```
		对应的执行上下文看起来是这样：
		
		```
		  GlobalExectionContext = {

        ThisBinding: <Global Object>,

        LexicalEnvironment: {
            EnvironmentRecord: {
            Type: "Object",
            // 在这里绑定标识符
            a: < uninitialized >,
            b: < uninitialized >,
            multiply: < func >
            }
            outer: <null>
        },

        VariableEnvironment: {
            EnvironmentRecord: {
            Type: "Object",
            // 在这里绑定标识符
            c: undefined,
            }
            outer: <null>
        }
    }

    FunctionExectionContext = {
        ThisBinding: <Global Object>,

        LexicalEnvironment: {
            EnvironmentRecord: {
            Type: "Declarative",
            // 在这里绑定标识符
            Arguments: {0: 20, 1: 30, length: 2},
            },
            outer: <GlobalLexicalEnvironment>
        },

        VariableEnvironment: {
            EnvironmentRecord: {
            Type: "Declarative",
            // 在这里绑定标识符
            g: undefined
            },
            outer: <GlobalLexicalEnvironment>
        }
    }

		```
		可以看出 **let** 和 **const** 定义的变量并没有关联任何值，但 **var** 定义的变量被设成了 undefined。这是因为在创建阶段时，引擎检查代码找出变量和函数声明，虽然函数声明完全存储在环境中，但是变量最初设置为 undefined（var 情况下），或者未初始化（let 和 const 情况下）。  
		
		这就是为什么你可以在声明之前访问 **var** 定义的变量（虽然是 undefined），但是在声明之前访问 **let** 和 **const** 的变量会得到一个引用错误。
		
* 执行阶段：完成对所有变量的分配，然后执行代码。在此阶段，若javascript引擎不能在源码中声明的实际位置找到let变量的值，则该变量会被赋值为**undefined**

### javascript执行机制

#### Event Loop（事件循环）是javascript的执行机制，也是其实现异步的一种方法

javascript任务是一个一个顺序执行的，若前一个任务耗时很长，后一个任务只能等着，因为js是单线程的。

javascript任务分为**同步任务**和**异步任务**  

* **同步任务**进入**主线程**，**异步任务**进入event table并注册回调函数  
* 当指定的事情完成时，event table会将回调函数移入event queue
* **主线程**内的任务执行完毕（即主线程执行栈为空，javascript引擎存在monitoring process进程，会持续不断的检查主线程执行栈是否为空），会去event queue读取对应的函数进入主线程执行
* 上述过程会不断重复，这就是**Event Loop（事件循环）**

#### setTimeout 和 setInterval

除了广义的**同步任务**和**异步任务**，还有更精细的**宏任务**和**微任务**

* macro-task(宏任务)：包括整体代码script，setTimeout，setInterval
* micro-task(微任务)：Promise，process.nextTick

	**不同类型的任务会进入不同的event queue**
	
##### 事件循环的顺序，决定js代码的执行顺序。进入整体代码(宏任务)后，开始第一次循环。接着执行所有的微任务。然后再次从宏任务开始，找到其中一个任务队列执行完毕，再执行所有的微任务。以此循环。

按照以上理论分析以下代码样例：

```
console.log('1');

setTimeout(function() {
    console.log('2');
    process.nextTick(function() {
        console.log('3');
    })
    new Promise(function(resolve) {
        console.log('4');
        resolve();
    }).then(function() {
        console.log('5')
    })
})
process.nextTick(function() {
    console.log('6');
})
new Promise(function(resolve) {
    console.log('7');
    resolve();
}).then(function() {
    console.log('8')
})

setTimeout(function() {
    console.log('9');
    process.nextTick(function() {
        console.log('10');
    })
    new Promise(function(resolve) {
        console.log('11');
        resolve();
    }).then(function() {
        console.log('12')
    })
})
```

整段代码，共进行了三次事件循环，完整的输出为1，7，6，8，2，4，3，5，9，11，10，12。
(请注意，node环境下的事件监听依赖libuv与前端环境不完全相同，输出顺序可能会有误差)

	