title: 你不知道的JavaScript(3)
date: 2016-03-18 09:03:56
tags:
    - 读书笔记

---

《你不知道的JavaScript》上卷 阅读笔记

本篇讲述：【this的那些事】【apply和call的用法】

<!-- more -->

# 第二部分	this和对象原型
<!--
## 第一章	关于`this`
### 1.1	为什么要用`this`

 -->

## 第二章	`this`全面解析
### 2.1	调用位置
什么是调用栈和调用位置，我们有一段代码来解释这个问题：

```JavaScript
function baz(){
  //当前调用栈是：baz
  //因此当前调用位置是全局作用域
  console.log("baz");
  bar();//<--bar的调用位置
}
function bar() {
  // 当前调用栈是baz -> bar
  // 因此，当前调用位置在baz中
  console.log( "bar" );
  foo(); // <-- foo的调用位置
}

function foo() {
  // 当前调用栈是baz -> bar -> foo
  // 因此，当前调用位置在bar中
  console.log( "foo" );
}

baz(); // <-- baz的调用位置
```
你可以把调用栈想象成一个函数调用链，就想代码中注释写的那样。

### 2.2	绑定规则
#### 2.2.1	默认绑定
独立函数调用。可以把这条规则看做是无法应用其他规则时的默认规则。

```JavaScript
function foo(){
  console.log(this.a);
}

var a = 1;

foo();
```

当我们调用`foo()`的时候，`this.a`被解析成了全局变量`a`。因为在函数调用时，应用了`this`的默认绑定，因此`this`指向全局对象。

但是如果我们使用了严格模式(strict mode)，那么全局对象将无法使用默认绑定，因此，`this`会被绑定到`undefined`。

**严格模式下与foo()的调用位置无关**

```JavaScript
function foo(){
  console.log(this.a);
}

var a =2;

(function(){
  "use strict";

  foo();//2
})()
```

```JavaScript
function foo(){
  "use strict";

  console.log(this.a);
}

var a = 1;

foo();//TypeError:this is undefined
```
#### 2.2.2	隐式绑定
如果调用位置有上下文对象，或者说被某个对象拥有或者包含。那么这时候我们需要考虑一些问题。

```JavaScript
function foo(){
  console.log(this.a);
}

var obj = {
  a:2,
  foo:foo
};

obj.foo();//2
```
由于调用位置会使用`obj`上下文来引用函数，因此你可以说函数被调用时`obj`对象“拥有”或者“包含”它。也就是说，在`foo()`被调用时，它的落脚点确实指向`obj`对象。而函数调用中的`this`会绑定到这个上下文对象`obj`。

**隐式丢失**
有些情况下`this`会丢失绑定对象，然后应用默认绑定，从而把`this`绑定到全局对象或者`undefined`上。

```JavaScript
function foo(){
  console.log(this.a);
}

var obj = {
  a:2,
  foo:foo
};

var bar = obj.foo;//函数别名

var a = "oops,global";//a是全局对象的属性

bar();//"oops,global"

```

虽然`bar`是`obj.foo`的一个引用，但是他引用的确实`foo()`本身，所以此时`bar()`其实是一个不带任何修饰的函数调用，所以会应用默认绑定。

#### 2.2.3	显示绑定
这里就涉及到了函数的两个方法：`call(...)`和`apply(...)`。它们的第一个参数是一个对象，它们会把函数的`this`绑定到这个对象上，因此我们称之为显示绑定。

```JavaScript
function foo(){
  console.log(this.a);
}

var obj = {
  a:2
};

foo.call(obj);//2
```

通过`foo.call(..)`我们把`this`绑定到了`obj`上

**硬绑定**

```JavaScript
function foo(something) {
  console.log( this.a, something );
  return this.a + something;
}

var obj = {
  a:2
};

var bar = function() {
  return foo.apply( obj, arguments );
};

var b = bar( 3 ); // 2 3
console.log( b ); // 5
```
在`bar`内部手动调用了`foo.apply(obj,arguments)`，因此强制把`foo`的`this`绑定到了`obj`。无论如何调用`bar`都会手动在`obj`上调用`foo`。

由于硬绑定是一种常用的模式，所以ES5中提供了内置的方法`Function.prototype.bind`，他的用法如下：

```JavaScript
function foo(something){
  console.log(this.a,something);
  return this.a + something;
}

var obj = {
  a:2
};

var bar = foo.bind(obj);

var b = bar(3);//2 3
console.log(b);//5
```

`bind(..)`会返回一个硬编码的新函数，它会把参数设置累`this`的上下文并调用原始函数。

**`apply(..)`和`call(..)`**

二者的作用完全一样，只是接受参数的方式不太一样。他们都是为了改变某个函数运行时的 context 即上下文而存在的，换句话说，就是为了改变函数体内部 this 的指向。因为 JavaScript 的函数存在「定义时上下文」和「运行时上下文」以及「上下文是可以改变的」这样的概念。

- Function.apply(obj,args)

	obj：这个对象将代替Function类里this对象

	args：这个是数组，它将作为参数传给Function（args-->arguments）

- Function.call(obj,param_1,param_2,...,param_n)

	需要把参数按顺序传递进去

#### 2.2.4	`new`绑定

```JavaScript
function foo(a){
  this.a = a;
}

var bar = new foo(2);

console.log(bar.a);//2
```
使用`new`来调用`foo(..)`时，会构造一个新对象并把它绑定到`foo(..)`调用中的`this`上。

### 2.3	优先级
判断`this`

1. 函数是否在`new`中调用（`new`绑定）？如果是的话`this`绑定的是新创建的对象。

	```JavaScript
	var bar = new foo();
	```

2. 函数是否通过`call` `apply`（显示绑定）或者硬绑定调用？如果是的话，`this`绑定的是指定的对象。

	```JavaScript
	var bar = foo.call(obj2);
	```

3. 函数是否在某个上下文对象中调用（饮食绑定）？如果是的话，`this`绑定的是那个上下文对象。

	```JavaScript
	var bar = obj1.foo();
	```

4. 如果都不是的话，使用默认绑定。如果是严格模式，就绑定到`undefined`，否则就绑定到全局对象。

	```JavaScript
	var bar = foo();
	```
