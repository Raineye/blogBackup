title: 			你不知道的JavaScript(4)
date: 			2016-03-21 9:57:42
comments:   false
tags:
    - 读书笔记

---

《你不知道的JavaScript》上卷 阅读笔记

本篇讲述：【对象】【属性描述符】【对象属性的get和set】【遍历】

<!-- more -->

## 第三章	对象
### 3.2	类型
首先来介绍JavaScript中的六种基本类型：

- `string`
- `number`
- `boolean`
- `null`
- `undefined`
- `object`

它们本身并不是对象。但是有一个例外`null`有时会被当做一种对象类型，这其实只是语言本身的一共bug，即对`null`执行`typeof null`时会返回`object`。但实际上，`null`本身是基本类型。

**内置对象**
这些内置对象有些和简单基础类型一样，不过它们的关系更复杂，我们稍后详细介绍。

- `String`
- `Number`
- `Boolean`
- `Object`
- `Function`
- `Array`
- `Date`
- `RegExp`
- `Error`

它们实际上只是一些内置函数。这些内置函数可以当做构造函数来使用，从而可以构造一个对应子类型的新对象。

```JavaScript
var strPrimitive = "I am a string"
typeof strPrimitive;//"string"
strPrimitive instanceof String;//false

var strObject = new String("I am a string");
typeof strObject;//"object"
strObject instanceof String;//true

//检查sub-type对象
Object.prototype.toString.call(strObject);//[object String]
```

由此可看出`strObject`是由`String`构造函数创建的一个对象。

原始值`"I am a string"`并不是一个对象，它只是一个字面量。如果要在这个字面量上执行一些长度，比如获取长度、访问其中的某个字符等，那需要将其转换成`String`对象。

在你直接通过字面量去执行那些操作时，语言会自动的把字符串字面量转换成一个`String`对象，也就是说你不需要显示的创建一个对象。

### 3.3	内容
对一个对象的访问有两种方式：

- 通过`.`操作符，通常叫做属性访问
- 通过`[key]`访问，通常叫做键访问

实际上他们访问的是同一个位置，这两个术语的意思其实是一致的。 它们的区别在于，`[..]`语法可以接受任何UTF-8/Unicode字符串作为属性名。例如，如果要引用名称为`Super-Fun!`的属性那么久必须要使用`["Super-Fun!"]`来访问。因为实际上`[..]`是通过字符串来访问属性的。

```JavaScript
var myObject = {
  a:2
};

var idx = "a";

console.log(myObject[idx]);//2
```

在对象中属性名永远都是字符串。如果你使用`string`以外的其他值作为属性名，那它首先会被转换成一个字符串。即使是数字也不例外，虽然在数组下标中使用的的确是数字，但是在对象属性名中，数字会被转换成字符串。

```JavaScript
var myObject = {};

myObject[true] = "foo";
myObject[3] = "bar";
myObject[myObject] = "baz";

myObject["true"];//"foo"
myObject["3"];//"bar"
myObject["[object Object]"];//"baz"
```

#### 3.3.1	可计算属性名
ES6增加了可计算的属性名，可以在文字形式中使用`[]`包裹一个表达式来作为属性名：

```JavaScript
var prefix = "foo";

var myObject = {
  [prefix + "bar"]:"hello",
  [prefix + "baz"]:"world"
};

myObject["foobar"];//hello
myObject["foobaz"];//world
```

#### 3.3.4 复制对象
首先我们应该分清浅拷贝和深拷贝，对于一般的`=`运算符来说，对对象的拷贝都是浅拷贝，只是将他们对值的引用拷贝给对方。

如何深拷贝一个对象呢。

对于JSON安全（也就是说可以被序列化为一个JSON字符串并且可以根据这个字符串解析出一个结构和值完全一样的对象）的对象来说有一种巧妙的复制方法可以解决这个问题。

```JavaScript
var newObj = JSON.parse(JSON.stringify(someObj));
```
当然，这种方法需要保持对象是JSON安全的，所以只适用于部分情况。

#### 3.3.5	属性描述符

```JavaScript
var myObject = {
  a:2
};

Object.getOwnPropertyDescriptor(myObject,"a");

//{
//  value:2,
//  writable:true,
//  enumerable:true,
//  configurable:true
//}

```

这个普通对象属性对应的属性描述符（也被称为“数据描述符”，因为它只保存一个数据值）包含了四个值：`value`(值)、`writable`(可写)、`enumerable`(可枚举)和`configurable`(可配置)。

1. `Writable`

	这个属性决定了是否可以修改属性的值。

	```JavaScript
	var myObject = {};

	Object.defineProperty( myObject, "a", {
    value: 2,
    writable: false, // not writable!
    configurable: true,
    enumerable: true
	});

	myObject.a = 3;

	myObject.a; // 2
	```
	我们对属性值的修改静默失败。如果在严格模式下，这种方式会抛出`TypeError`来表示我们无法修改一个不可写的属性。
2. `Configurable`

	只要属性是可配置的（Configurable），就可以用`defineProperty(..)`方法来修改属性描述符。如果该属性为`false`那么将无法使用`defineProperty(..)`方法，而且这个值的设置是单向的。因为一旦修改它为`false`那么就无法撤销！而且还会禁止删除这个属性，`delete`语句会对它静默失败。（`delete myObject.a`）

3. `Enumerable`

这个描述符控制的是属性是否会出现在对象属性的枚举中，比如`for..in`循环。如果把`enumerable`设置为`false`那么它将不会出现在枚举中，虽然仍然可以正常访问它。

```JavaScript
fot (var k in myObject){
  console.log(k,myObject[k]);
}//输出所有可以被枚举的属性
```

#### 3.3.6	不变性

**禁止扩展**

如果你想禁止一个对象添加新属性并保留已有属性，可以使用`Object.preventExtensions(..)`:

```JavaScript
var myObject = {
  a:2
};

Object.preventExtensions(myObject);

myObject.b = 3;
myObject.b;//undefined
```
在非严格模式下，创建属性`b`会静默失败。在严格模式下，将会抛出`TypeError`错误。

**密封**
`Object.seal(..)`会创建一个“密封”的对象，这个方法实际上回在一个现有对象上调用`Object.preventExtensions(..)`并把所有现有属性标记为`configurable:false`。

#### 3.3.9	Getter和Setter

```JavaScript
var myObject = {
  get a(){
    return this._a_;
  },
  set a(val){
    this._a_ = val*2;
  }
};

Object.defineProperty(
  myObject,
  "b",
  {
    get:function() {
      return this.a*2;
    },
    enumerable:true
  }
);

myObject.a= 4;

console.log(myObject.a);//8
console.log(myObject.b);//16
```
通常来说getter和setter是成对出现的（只定义一个的话通常会产生意料之外的行为）

#### 3.3.10	存在性

```JavaScript
var myObject = {
  a:2
};

("a" in myObject);//true
("b" in myObject);//false

myObject.hasOwnProperty("a");//true
myObject.hasOwnProperty("b");//false
```

`in`操作符会检查属性是否存在对象及其`[[Prototype]]`原型链中。相比之下，`hasOwnProperty(..)`只会检查属性是否在`myObject`对象中，不会检查`[[Prototype]]`链。

有一个例外，如果对象没有连接到`Object.prototype`(通过`Object.create(null)`来创建)。在这种情况下，形如`myObject.hasOwnProperty(..)`就会失败。这时可以用一种更加强硬的方式来进行判断：`Object.prototype.hasOwnProperty.call(myObject,"a")`。

`in`操作符是检查某个属性名是否存在。对于数组来说这个区别非常重要，`4 in [2,3,4]`的结果并不是`True`，因为`[2,3,4]`这个数组包含的属性名是0、1、2,没有4。

### 3.4	遍历

**数组遍历**

```JavaScript
var arr = [1,2,3,4,5];
```

- `forEach(..)`

	遍历数组中所有的值，并忽略回调函数的返回值。

	```JavaScript
	arr.forEach(function(e) {
    console.log(e);//1,2,3,4,5
	});
	```
- `every(..)`

	一直运行直到回调函数返回`false`

	```JavaScript
	arr.every(function(e) {
    console.log(e);//1,2,3
    if (e == 3) {
      return false;
    }
	});
	```

- `some(..)`

	一直运行直到回调函数返回`ture`

	```JavaScript
	arr.some(function(e) {
	  console.log(e);//1,2,3
	  if( e==3){
	    return true;
	  }
	});
	```

- `for..in`

	这种遍历方式无法直接获取属性值，因为它实际上遍历的是对象中所有可枚举的属性。当它用在数组上的时候，会返回数组的下标。
	我们先来看对于对象属性的访问：

	```JavaScript
	var foo = {
	  a:1,
	  b:2,
	  c:3,
	  d:4
	};
	for (var key in foo) {
	  if (foo.hasOwnProperty(key)) {
	    console.log(key);//a,b,c,d
	  }
	}
	```
	接下来我们来看对于数组的访问：

	```JavaScript
	var arr = [1,2,3,4,5];
	for (var key in arr) {
	  console.log(key);//0,1,2,3,4
	}
	```

- `for..of`

	这个循环语法是ES6中增加的，它可以返回数组的值而不是下标。

	```JavaScript
	var arr = [1,2,3,4,5];

	for(var v of arr){
		console.log(v);//1,2,3,4,5
	}
	```
	`for..of`循环首先会向被访问对象请求一个迭代器对象，然后通过调用迭代器对象的`next()`方法来遍历所有返回值。

	数组有内置的`@@iterator`，因此`for..of`可以直接应用在数组上。我们使用内置的`@@iterator`来手动遍历数组，看看它是怎么工作的：

	```JavaScript
	var myArray = [1,2,3];
	var it = myArray[Symbol.iterator]();
	it.next();//{value:1,done:false}
	it.next();//{value:2,done:false}
	it.next();//{value:3,done:false}
	it.next();//{value:undefined,done:true}
	```
	如果你想遍历一个对象的话，你可以给这个对象定义一个`@@iterator`。

	```JavaScript
	var myObject = {
	    a: 2,
	    b: 3
	};

	Object.defineProperty( myObject, Symbol.iterator, {
	    enumerable: false,
	    writable: false,
	    configurable: true,
	    value: function() {
	        var o = this;
	        var idx = 0;
	        var ks = Object.keys( o );//['a','b']
	        return {
	            next: function() {
	                return {
	                    value: o[ks[idx++]],
	                    done: (idx > ks.length)
	                };
	            }
	        };
	    }
	} );

	//console.log(Object.keys(myObject));//['a','b']

	// 手动遍历myObject
	var it = myObject[Symbol.iterator]();
	it.next(); // { value:2, done:false }
	it.next(); // { value:3, done:false }
	it.next(); // { value:undefined, done:true }

	// 用for..of遍历myObject
	for (var v of myObject) {
	    console.log( v );//2,3
	}

	```
	我们也可以在定义对象时进行声明

	```JavaScript
	var myObject = {
	    a: 2,
	    b: 3,
	    [Symbol.iterator]:function() {
	      var o = this;
	      var idx = 0;
	      var ks = Object.keys( o );//['a','b']
	      return {
	          next: function() {
	              return {
	                  value: o[ks[idx++]],
	                  done: (idx > ks.length)
	              };
	          }
	      };
	    }
	};
	```
