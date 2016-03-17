title: 你不知道的JavaScript(2)
date: 2016-03-17 16:45:34
tags:
    - JavaScript
---

《你不知道的JavaScript》上卷 阅读笔记

本篇讲述：【变量和函数的提升】【闭包】【模块】

<!-- more -->

## 第四章	提升

### 4.2	编译器再度来袭

```
foo();
function foo(){
	console.log(a);//undefined
	var a =2;
}
```
显然这段代码的结果告诉我们，在引擎处理这段代码的时候，只是将声明提升了，但是表达式并没有被提升。
它被引擎理解成了下面这段代码：

```
function foo(){
	var a;
	console.log(a);
	a = 2;
}
foo();
```
下面再来看一段代码：

```
foo();//TypeError!
bar();//ReferenceError!
var foo = function bar(){
	//...
}
```
这段代码中的变量标识符`foo()`被提升并分配给所在作用域，因此`foo()`不会导致`ReferenceError`。但是foo此时没有被赋值，它的默认值为`undefined`，对它进行函数调用而导致非法操作，因此会抛出`TypeError`异常。
而`bar()`函数是一个带名字的匿名函数，所以它只能在内部作用域使用，所以在外部作用域会由于导致`ReferenceError`（引用错误）
这段代码会被引擎理解成以下形式：

```
var foo;

foo();
bar();

foo = function(){
	var bar = ..self..
}
```
### 4.3函数优先
在多个“重复”声明的代码中，函数会首先被提升，然后才是变量。
考虑以下代码：
```
foo();//1
var foo;
function foo(){
	console.log('1');
}
foo = function(){
	console.log('2');
}
foo();//2
```
尽管`var foo`出现在了`function foo()...`的声明之前，但是它是重复声明，因此它会被忽略，因为函数声明会被提升到普通变量之前。它会被引擎理解成如下形式：
```
function foo(){
	console.log(1);
}
foo();//1
foo = function(){
	console.log(2);
}
foo();//2
```
当你理解了这些以后，我们再来一段相关的代码：
```
foo();//3
function foo(){
	consloe.log(1);
}
var foo = function(){
	console.log(2);
}
function foo(){
	console.log(3);
}
```
虽然这些听起来都是些无用的学院理论，但是它说明了除非你走投无路，不然千万不要在同一个作用域中重复定义，经常会导致各种奇怪的问题。

ok,让我们再来加个餐，看以下代码：
```
function foo(){
	a = 1;
}
foo();
console.log(a);//1
```
虽然你在`foo()`内部给`a`赋值，但是，它的声明却在外部作用域。首先，引擎会在`foo`内部作用域中查找是否有`a`这个变量，然后作用域告诉它没有找到，那么它就去`foo`的上层作用域去找，依然没有找到，这时它会声明一个变量`var a`在外部作用域，然后，返回这个变量给表达式赋值。所以此时，`a`其实是属于外部作用域的变量，所以`a`的值为1。
如果我们把代码改成如下形式，那么便会抛出`ReferenceError`异常，即引用错误。
```
function foo(){
	var a = 1;
}
foo();
console.log(a);//ReferenceError
```
### 小结
我们习惯把`var a = 2;`看做一个声明，而实际上JavaScript引擎并不这么认为。它将`var a`和`a = 2`当做两个单独的声明，第一个是编译阶段的任务，而第二个是执行阶段的任务。

## 第五章	作用域闭包
接下来我们将注意力转移到这门语言中一个非常重要但又难以掌握，近乎神话的概念上：**闭包**。

### 5.2	实质问题

你可能会问，闭包到底是什么，先给一段生涩的定义：
>当函数可以记住并访问所在词法作用域时，就产生了闭包，即使函数是在当前词法作用域之外执行。

下面用一些代码来解释这个定义。

```
function foo(){
	var a = 2;

	function bar(){
		console.log(a);//2
	}

	return bar;
}
var baz = foo();
baz();//2  这就是闭包的效果
```

函数`bar()`的词法作用域能够访问`foo()`的内部作用域。然后我们将`bar()`函数本身当做一个值类型进行传递。在`foo()`执行后，其返回值（即`bar()`函数）赋值给变量`baz`并调用，实际上只是通过不同的标识符引用调用了内部的函数bar()。

在`foo()`执行后，通常会期待`foo()`的整个内部作用域都会被销毁，因为引擎会自动回收垃圾来释放不再使用的内存空间。但是内部作用域由于`bar()`在外部还在使用，所以并不会被销毁掉。

`bar()`依然持有对`foo()`的作用域的引用，而这个引用就叫做闭包。

### 5.4	循环和闭包
来看一个例子：

```
for(var i = 1;i<=5;i++){
	setTimeout(function timer(){
		console.log(i);
	},i*1000);
}
```
我们对这段代码的预期是分别输出1~5，每秒一次每次一个。

但实际上，这段代码在运行时会以每秒一次的频率输出五次6。

**这是为什么**

由于i的循环终止条件是`i<=5`所以在循环终止的时候`i`的值为6。然后延迟函数的回调会在循环结束时才执行，所以会输出6。

事实上即使每个迭代中执行`setTimeout(..,0)`，所有的回调依然会在循环结束后才会被执行。这个问题稍后来解释。

由于所有的回调函数都共享一个`i`的引用，所以在循环结束后输出的`i`均为6。

如何实现我们的预期呢，答案就是闭包。

```
for(var i = 1; i<=5; i++){
	(function(j){
		setTimeout(function timer(){
			console.log(j);
		},j*1000);
	})(i)
}
```
在迭代内使用IIFE会为每个迭代都生成一个新的作用域，使得延迟函数的回调可以将新的作用域封闭在每个迭代内部，每个迭代中都会有一个具有正确值的变量供我们访问。

>IIFE:立即执行函数表达式
>`(function(){...})()`

##### 题外话
我们来解决`setTimeout(..,0)`的问题。

由于JavaScript是单线程的。而`setTimeout()`并不会创建一个新的线程去执行，而是被插入了任务队列。我们可以把整个代码看做一个任务。那么就是说代码执行完成就是当前任务完成了。接着就会继续执行下一个任务，这时，由于`setTimeout()`的回调函数被插入了。所以才会执行回调。

如果不理解的话，我们来看另一个例子：

```
var isEnd = true;

window.setTimeout(function () {
	isEnd = false;//1s后，改变isEnd的值
}, 1000);

//这个while永远的占用了js线程，所以setTimeout里面的函数永远不会执行
while (isEnd);

//alert也永远不会弹出
alert('end');
```
![](http://7xpp66.com1.z0.glb.clouddn.com/js_16031715.png)

由于while的执行而导致后面`setTimeout`的回调函数一直无法执行，故不会跳出，`alert('end')`也就不会弹出。

### 5.5	模块

我们来看一个模块的实现方式：

```
function CoolModule() {
    var something = "cool";
    var another = [1, 2, 3];

    function doSomething() {
        console.log( something );
    }

    function doAnother() {
        console.log( another.join( " ! " ) );
    }

    return {
        doSomething: doSomething,
        doAnother: doAnother
    };
}
var foo = CoolModule();

foo.doSomething();//cool
foo.doAnother();//1!2!3!
```
这个模式在JavaScript中被称为模块。最常见的模块模式的方法通常被称为模块暴露，这里展示的是其变体。

使用`CoolModule()`会返回一个用对象字面量语法`{key:value,...}`来表示的对象。这个返回的对象（模块还可以返回一个函数，因为函数也是一个对象，本身也可以拥有属性，比如JQuery）中含有对内部函数而不是内部数据变量的引用。我们保持内部数据变量是隐藏且私有的状态。可以将这个对象类型的返回值看做本质上是模块的公共API。

模块模式的定义：
> 1. 必须有外部的封闭函数，该函数必须至少被调用一次（每次调用都会创建一个新的模块实例）
> 2. 封闭函数必须返回至少一个内部函数，这样内部函数才能在私有作用域中形成闭包，并且可以访问或者修改私有的状态。


#### 5.5.1	现代的模块机制

```
var MyModules = (function Manager() {
    var modules = {};

    function define(name, deps, impl) {
        for (var i=0; i<deps.length; i++) {
            deps[i] = modules[deps[i]];
        }
        modules[name] = impl.apply( impl, deps );
    }

    function get(name) {
        return modules[name];
    }

    return {
        define: define,
        get: get
    };
})();
```
```
MyModules.define( "bar", [], function() {
    function hello(who) {
        return "Let me introduce: " + who;
    }

    return {
        hello: hello
    };
} );

MyModules.define( "foo", ["bar"], function(bar) {
    var hungry = "hippo";

    function awesome() {
        console.log( bar.hello( hungry ).toUpperCase() );
    }

    return {
        awesome: awesome
    };
} );

var bar = MyModules.get( "bar" );
var foo = MyModules.get( "foo" );

console.log(bar.hello( "hippo" ));//Let me introduce:hippo
foo.awesome();//LET ME INTRODUCE:HIPPO
```

第一段代码维护了一个模块列表，可以定义模块并引入依赖，也可以从列表中获取模块。

第二段代码是如何使用它来定义切使用模块

#### 5.5.2	未来的模块机制

ES6中为模块增加了一级语法支持。但通过模块系统进行加载时，ES6会将文件当做独立模块来处理。每个模块都可以导入其他模块或特定的API成员，同样也可以导出自己的API成员。

例如：

```
//bar.js
function hello(who){
	return "Let me introduce:"+who;
}

export hello;
```

```
//foo.js
//仅从"bar"模块中导入hello()
import hello from "bar";

var hungry = "hippo";

function awesome(){
	console.log(
		hello(hungry).toUpperCase();
	)
}

export awesome;
```

```
//导入完整的"foo"和"bar"模块
module foo from "foo";
module foo from "bar";

console.log(
	hello("rhino").toUpperCase();
)//Let me introduce:rhino

foo.awesome();//LET ME INTRODUCE:RHINO

```

### 5.6	小结
>当函数可以记住并访问所在的词法作用域，即使函数是在当前词法作用域之外执行，这时就产生了闭包。
>
>
>**模块有两个主要特征**：
>
>	1. 为创建内部作用域而调用了一个包装函数
>	2. 包装函数的返回值必须至少包括一个对内部函数的引用，这样就会创建涵盖整个包装函数内部作用域的闭包。
