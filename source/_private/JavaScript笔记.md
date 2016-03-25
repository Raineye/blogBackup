# JavaScript 笔记
## 常用函数

### [Array.prototype.filter()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)
#### 概述
filter() 方法使用指定的函数测试所有元素，并创建一个包含所有通过测试的元素的新数组。

#### 语法
arr.filter(callback[, thisArg])

#### 参数

- callback

用来测试数组的每个元素的函数。调用时使用参数 (element, index, array)。
返回true表示保留该元素（通过测试），false则不保留。

- thisArg

可选。执行 callback 时的用于 this 的值。

#### 示例

```
function isBigEnough(element) {
  return element >= 10;
}
var filtered = [12, 5, 8, 130, 44].filter(isBigEnough);
// filtered is [12, 130, 44]
```

`arr.filter(call)`

当`call`返回`true`时保留该元素，最后返回过滤后的元素。

### [Object.assign()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)

#### 概述
Object.assign() 方法可以把任意多个的源对象所拥有的自身可枚举属性拷贝给目标对象，然后返回目标对象。

#### 语法
Object.assign(target, ...sources)
#### 参数

- target

	目标对象。

- sources

	任意多个源对象。

- 返回值

	目标对象会被返回。
	
#### 示例

```
var o1 = { a: 1 };
var o2 = { b: 2 };
var o3 = { c: 3 };

var obj = Object.assign(o1, o2, o3);
console.log(obj); // { a: 1, b: 2, c: 3 }
console.log(o1);  // { a: 1, b: 2, c: 3 }, 注意目标对象自身也会改变。
```

###  [Array.prototype.map()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/map)
#### 概述
map() 方法返回一个由原数组中的每个元素调用一个指定方法后的返回值组成的新数组。

#### 语法
array.map(callback[, thisArg])

#### 参数
- callback

	原数组中的元素经过该方法后返回一个新的元素。
- currentValue

	callback 的第一个参数，数组中当前被传递的元素。
- index
 
	callback 的第二个参数，数组中当前被传递的元素的索引。
- array

	callback 的第三个参数，调用 map 方法的数组。
- thisArg

	执行 callback 函数时 this 指向的对象。

#### 示例

```
var List = [a,b,c,d,e]
List.map(function(item,index){
	console.log(item);//a,b,c,d,e
	console.log(index);//0,1,2,3,4
})
```