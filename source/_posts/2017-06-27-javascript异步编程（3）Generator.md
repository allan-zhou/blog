---
title: javascript异步编程（3）Generator
date: 2017-06-27 22:53:56
categories:
- Javascript
tags:
- Javascript
- 异步编程
---

# 基础概念

## Symbol 类型
Symbol 是一种特殊的、不可变的数据类型，可以作为对象属性的标识符使用。  

<!-- more -->

ES5 的对象属性名都是字符串，这容易造成属性名的冲突。比如，你使用了一个他人提供的对象，但又想为这个对象添加新的方法（mixin 模式），新方法的名字就有可能与现有方法产生冲突。如果有一种机制，保证每个属性的名字都是独一无二的就好了，这样就从根本上防止属性名的冲突。这就是 ES6 引入Symbol的原因。

它是 JavaScript 语言的第七种数据类型，前六种是：undefined、null、布尔值（Boolean）、字符串（String）、数值（Number）、对象（Object）。

## Symbol.iterator 
`Symbol.iterator` 为每一个对象定义了默认的迭代器。该迭代器可以被 `for...of` 循环结构使用。  

当需要迭代一个对象的时候（比如在 `for...of` 循环的开始时），它的 `@@iterator` 方法就会被调用一次（0 个参数），同时返回的迭代器将被用来获取被迭代出来的值。

javascript中拥有默认的迭代器行为的**内建类型**有：
* Array.prototype\[@@iterator]()
* TypedArray.prototype\[@@iterator]()
* String.prototype\[@@iterator]()
* Map.prototype\[@@iterator]()
* Set.prototype\[@@iterator]()


## 可迭代协议 iterable
可迭代协议允许 JavaScript 对象去定义或定制它们的迭代行为, 例如（定义）在一个 for..of 结构中什么值可以被循环（得到）。  

为了变成可遍历对象， 一个对象必须实现 `@@iterator` 方法, 意思是这个对象（或者它原型链prototype chain上的某个对象）必须有一个名字是 `Symbol.iterator` 的属性:

属性 | 值 
---------|----------
[Symbol.iterator] | 返回一个对象的无参函数，被返回对象符合迭代器协议。

### 可迭代对象示例
```javascript
var myIterable = {}
myIterable[Symbol.iterator] = function* () {
    yield 1;
    yield 2;
    yield 3;
};
for( let val of myIterable){
    console.log(val); // 1,2,3
};
[...myIterable] // [1, 2, 3],也可以使用 spread operator扩展语句
```


### 用于可迭代对象的语法
一些语句和表达式是预料会用于可迭代对象，比如 `for-of` 循环，`spread operator(扩展语句)`, `yield*` 和 `destructuring assignment(解构赋值)`。

```javascript
for(let value of ["a", "b", "c"]){
    console.log(value);
}
// "a"
// "b"
// "c"

[..."abc"]; // ["a", "b", "c"]

function* gen(){
  yield* ["a", "b", "c"];
}

gen().next(); // { value:"a", done:false }

[a, b, c] = new Set(["a", "b", "c"]);
a // "a"
```

## 迭代器协议 iterator
该迭代器协议定义了一种标准的方式来产生一个有限或无限序列的值。
当一个对象被认为是一个迭代器时，它实现了一个 `next()` 的方法并且拥有以下含义：

属性 | 值 
---------|----------
next | 返回一个对象的无参函数，被返回对象拥有两个属性：<br/>`done (boolean)` <br/> &nbsp;&nbsp;&nbsp;&nbsp;如果迭代器已经经过了被迭代序列时为 true。这时 value 可能描述了该迭代器的返回值。返回值在这里有更多解释。<br/>&nbsp;&nbsp;&nbsp;&nbsp;如果迭代器可以产生序列中的下一个值，则为 false。这等效于连同 done 属性也不指定。 <br/>`value` <br/> &nbsp;&nbsp;&nbsp;&nbsp;迭代器返回的任何 JavaScript 值。done 为 true 时可省略。

### 迭代器示例
```javascript
function makeIterator(array){
    var nextIndex = 0;
    
    return {
       next: function(){
           return nextIndex < array.length ?
               {value: array[nextIndex++], done: false} :
               {done: true};
       }
    };
}

var it = makeIterator(['yo', 'ya']);

console.log(it.next().value); // 'yo'
console.log(it.next().value); // 'ya'
console.log(it.next().done);  // true
```

## 可迭代对象转换为迭代器对象

```javascript
var someArray = [1, 5, 7];
var someArrayEntries = someArray.entries();

console.log(someArrayEntries.toString()); ;           // "[object Array Iterator]"
console.log(someArrayEntries === someArrayEntries[Symbol.iterator]());    // true;
console.log(someArrayEntries.next()); // { value: [ 0, 1 ], done: false }
```
注：`entries()` 方法返回一个新的Array Iterator对象，该对象包含数组中每个索引的键/值对。

# function*  生成器函数
function\*  这种声明方式(function关键字后跟一个星号）会定义一个**`生成器函数(generator function)`**，它返回一个  `Generator`  对象。  

**生成器函数**在执行时能中途退出，后面又能重新进入继续执行。而且在函数内定义的变量的状态都会保留，不受中途退出的影响。

**调用**一个**生成器函数**并不会马上执行它里面的语句，而是返回一个这个生成器的**迭代器（iterator）对象**。
当这个迭代器的 next() 方法被首次（后续）调用时，其内的语句会执行到第一个（后续）出现yield表达式的位置为止，该表达式定义了迭代器要返回的值，或者被 yield*委派至另一个生成器函数。next()方法返回一个对象，这个对象包含两个属性：value 和 done，value 属性表示本次 yield 表达式的返回值，done 属性为布尔类型，表示生成器是否已经产出了它最后的值，即生成器函数是否已经返回。

## next()传参
调用 next() 方法时，如果传入了参数，那么这个参数会取代生成器函数中对应执行位置的 yield 表达式（整个表达式被这个值替换）
```javascript
function* G() {
    const a = yield 100
    console.log('a', a)  // a aaa
    const b = yield 200
    console.log('b', b)  // b bbb
    const c = yield 300
    console.log('c', c)  // c ccc
}
const g = G()
console.log(g.next());      // { value: 100, done: false }
console.log(g.next('aaa')); // { value: 200, done: false }
console.log(g.next('bbb')); // { value: 300, done: false }
console.log(g.next('ccc')); // { value: undefined, done: true }
```

# Generator  生成器
生成器对象是由一个 `generator function` 返回的,并且它符合`可迭代协议`和`迭代器协议`。

> **生成器对象 既是`迭代器`也是`可迭代对象`**

## 方法

方法 | 描述 
---------|----------
`Generator.prototype.next()` | 返回一个由 yield表达式生成的值。
`Generator.prototype.return()` | 返回给定的值并结束生成器。
`Generator.prototype.throw()` |  向生成器抛出一个错误。

# Thunk 函数
要想让Generator和异步操作产生联系，就必须过 `thunk` 函数这一关。

在 JavaScript 语言中，`Thunk` 函数替换的不是表达式，而是多参数函数，将其替换成单参数的版本，且只接受回调函数作为参数。

```javascript
// 正常版本的readFile（多参数版本）
fs.readFile(fileName, callback);

// Thunk版本的readFile（单参数版本）
var readFileThunk = Thunk(fileName);
readFileThunk(callback);

var Thunk = function (fileName){
  return function (callback){
    return fs.readFile(fileName, callback); 
  };
};
```

## thunkify 库
Turn a regular node function into one which returns a thunk, useful for generator-based flow control such as `co`.

示例
```javascript
var thunkify = require('thunkify');
var fs = require('fs');

var read = thunkify(fs.readFile);

read('package.json', 'utf8')(function(err, str){
  
});
```

## Thunk 函数的自动流程管理
使用 `Thunk` 可以实现 `Generator` 的自动化流程管理
```javascript
// 自动流程管理的函数
function run(generator) {
    const g = generator()
    function next(err, data) {
        const result = g.next(data)  // 返回 { value: thunk函数, done: ... }
        if (result.done) {
            // result.done 表示是否结束，如果结束了那就 return 作罢
            return
        }
        result.value(next)  // result.value 是一个 thunk 函数，需要一个 callback 函数作为参数，而 next 就是一个 callback 形式的函数
    }
    next() // 手动执行以启动第一次 next
}

// 定义 Generator
const readFileThunk = thunkify(fs.readFile)
const gen = function* () {
    const r1 = yield readFileThunk('data1.json')
    console.log(r1)
    const r2 = yield readFileThunk('data2.json')
    console.log(r2)
}

// 启动执行
run(gen)
```

# co 库
什么是co？

> Generator based control flow goodness for nodejs and the browser, using promises, letting you write non-blocking code in a nice-ish way.

根据官方介绍得知，`co` 是基于 `Generator` 的控制流管理库，并且 `co()` 返回一个 `promise` 对象

co 函数库其实就是将两种自动执行器（Thunk 函数和 Promise 对象），包装成一个库。

```javascript
const readFileThunk = thunkify(fs.readFile)

co(function* () {
    const r1 = yield readFileThunk('data1.json');
    console.log(r1.toString())
    const r2 = yield readFileThunk('data2.json');
    console.log(r2.toString())
}).catch(onerror);

```
另外，`co` 支持并发的异步操作。当前，co 支持的 `Yieldables` 有：详见 [github](https://github.com/tj/co)
* promises
* thunks (functions)
* array (parallel execution)
* objects (parallel execution)
* generators (delegation)
* generator functions (delegation)


# 参考链接
- [MDN Generator](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Generator)
- [MDN function*](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/function*)
- [MDN 可迭代协议&迭代器协议](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Iteration_protocols#iterable)
- [王福朋的深入理解 JavaScript 异步](https://github.com/wangfupeng1988/js-async-tutorial)
- [阮一峰 ES6入门 Symbol](http://es6.ruanyifeng.com/#docs/symbol)
- [MDN Symbol.iterator](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Symbol/iterator)
- [thunkify 库](https://github.com/tj/node-thunkify)
- [co 库](https://github.com/tj/co)