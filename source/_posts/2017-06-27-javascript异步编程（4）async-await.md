---
title: javascript异步编程（4）async/await
date: 2017-06-27 22:54:07
categories:
- Javascript
tags:
- Javascript
- 异步编程
---

# AsyncFunction
**AsyncFunction 构造函数** 创建一个新的  `async function` 对象。在JavaScript中，每个异步函数实际上都是一个 **AsyncFunction 对象**。  

<!-- more -->

AsyncFunction不是一个全局对象。可以通过运行以下代码获得。 
```javascript
Object.getPrototypeOf(async function(){}).constructor
```
## 语法
> new AsyncFunction([arg1[, arg2[, ...argN]],] functionBody)
> 
> 参数说明：
- arg1, arg2, ... argN
函数的参数名。每个必须是一个字符串，且符合 JavaScript 有效标示符或者用逗号隔开的一组字符串。例如 "x"，"theValue"，或者 "a,b"。
- functionBody
一段包括函数声明的 javascript 函数语句的字符串

## 示例
```javascript
function resolveAfter2Seconds(x) {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve(x);
    }, 2000);
  });
}

var AsyncFunction = Object.getPrototypeOf(async function(){}).constructor;
var a = new AsyncFunction('a', 
                          'b',
                          'return await resolveAfter2Seconds(a) + await resolveAfter2Seconds(b);');
a(10, 20).then(v => {
  console.log(v); // 4 秒后打印出 30
});

```
# async function 声明语句
`async function` 声明定义了一个异步函数，它返回一个 **AsyncFunction 对象**。

## 语法
> async function name([param[, param[, ... param]]]) {
>     statements
> }

调用异步函数时会返回一个 promise 对象。当这个异步函数返回一个值时，promise 的 resolve 方法将会处理这个返回值；当异步函数抛出的是异常或者非法值时，promise 的 reject 方法将处理这个异常值。

## 示例
```javascript
function resolveAfter2Seconds(x) {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve(x);
    }, 2000);
  });
}

async function add2(x) {
  var a = await resolveAfter2Seconds(20);
  var b = await resolveAfter2Seconds(30);
  return x + a + b;
}

add2(10).then(v => {
  console.log(v);  // prints 60 after 4 seconds.
});
```


# async function expression 表达式
`async function` 关键字可以用来定义一个**异步函数表达式**。

> async function 表达式非常类似于 async function 声明语句，并且几乎拥有等同的语法。他们之间**主要的区别在于函数名称，async function表达式可以省略函数名称来创建一个匿名的函数**。

## 示例
```javascript
function resolveAfter2Seconds(x) {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve(x);
    }, 2000);
  });
};

var add = async function(x) {
  var a = await resolveAfter2Seconds(20);
  var b = await resolveAfter2Seconds(30);
  return x + a + b;
}

add(10).then(v => {
  console.log(v);  // prints 60 after 2 seconds.
});

```

# async 函数的优点
async 函数对 Generator 函数的改进，体现在以下三点。

1. ** 内置执行器。** Generator 函数的执行必须靠执行器，所以才有了 `co 函数库`，而 async 函数自带执行器。也就是说，async 函数的执行，与普通函数一模一样，只要一行。
1. **更好的语义。** async 和 await，比起星号和 yield，语义更清楚了。async 表示函数里有异步操作，await 表示紧跟在后面的表达式需要等待结果。
1. **更广的适用性。** co 函数库约定，yield 命令后面只能是 Thunk 函数或 Promise 对象等 `Yieldables`，而 async 函数的 await 命令后面，可以跟 Promise 对象和原始类型的值（数值、字符串和布尔值，但这时等同于同步操作）。

# 参考链接
- [MDN async_function](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/async_function)
- [阮一峰 async函数的含义和用法](http://www.ruanyifeng.com/blog/2015/05/async.html)