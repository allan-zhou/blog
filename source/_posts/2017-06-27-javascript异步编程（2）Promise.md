---
title: javascript异步编程（2）Promise
date: 2017-06-27 22:53:38
categories:
- Javascript
tags:
- Javascript
- 异步编程
---

# 定义&概念
Promise 对象表示一个异步操作的最终完成（或失败）情况

<!-- more -->

```javascript
var promise = new Promise(function(resolve, reject) {
  // do a thing, possibly async, then…

  if (/* everything turned out fine */) {
    resolve("Stuff worked!");
  }
  else {
    reject(Error("It broke"));
  }
});

```
resolve 和 reject 函数被调用时，分别将promise的状态改为fulfilled(完成)或rejected（失败）。

一个 Promise有以下几种状态:

* pending: 初始状态，不是成功或失败状态。
* fulfilled: 意味着操作成功完成。
* rejected: 意味着操作失败。

> Promise 对象是一个代理对象（代理一个值），被代理的值在Promise对象创建时可能是未知的。它允许你为异步操作的成功和失败分别绑定相应的处理方法（handlers ）。 这让异步方法可以像同步方法那样返回值，但并不是立即返回最终执行结果，而是一个能代表未来出现的结果的promise对象

# Promise.prototype.then

`then()` 方法返回一个  Promise。它最多需要有两个参数：`onfulfilled` 和 `onrejected`，它们都是 Function 类型。  

```javascript
p.then(onFulfilled, onRejected);

p.then(function(value) {
   // fulfillment
  }, function(reason) {
  // rejection
});
```
当Promise状态为fulfilled时，调用 then 的 onfulfilled 方法，当Promise状态为rejected时，调用 then 的 onrejected 方法， 所以在异步操作的完成和绑定处理方法之间不存在竞争

因为 `Promise.prototype.then` 和 `Promise.prototype.catch` 方法返回promise 对象， 所以它们可以被链式调用。
![promises](/images/javascript/js-promises.png)

# 参数传递

- 只需返回新值即可改变值
- 如果前面返回的是Promise对象，后面的then将会被当做这个返回的Promise的第一个then来对待

```javascript
var promise = new Promise(function(resolve, reject) {
  resolve(1);
});

var promise2 = new Promise(function (resolve, reject) {
    resolve(100);
});

promise.then(function (val) {
    console.log(val); // 1
    return val + 2;   //返回新值
}).then(function (val) {
    console.log(val); // 3
    return promise2;  //返回promise2
}).then(function (val) {
    console.log(val); //100
});
```

# 异常处理
catch() 没有任何特殊之处。它的行为与调用 `Promise.prototype.then(undefined, onRejected)` 相同，但可读性更强。

```javascript
get('story.json').then(function(response) {
  console.log("Success!", response);
}).catch(function(error) {
  console.log("Failed!", error);
})
```
上面代码，相当于下面代码
```javascript
get('story.json').then(function(response) {
  console.log("Success!", response);
}).then(undefined, function(error) {
  console.log("Failed!", error);
})
```
两者之间的**差异虽然很微小**，但非常有用。  
Promise 拒绝后，将跳至带有拒绝回调的下一个`then()`（或具有相同功能的 `catch()`）。如果是 `then(func1, func2)`，则 `func1` 或 `func2` 中的一个将被调用，而**不会二者均被调用**。但如果是`then(func1).catch(func2)`，则在 `func1` 拒绝时两者均被调用，因为它们在该链中是单独的步骤。

如下代码，当Promise 拒绝后，`handleNetworkError`和 `handleProgrammerError`都会被调用。
```javascript
save()
  .then(
    handleSuccess,
    handleNetworkError
  )
  .catch(handleProgrammerError)
;
```
> **注意**：与 JavaScript 的 try/catch 一样，错误被捕获而后续代码继续执行。

> **建议**： ending all promise chains with a `.catch()`.

# Promise API参考

**静态方法**

方法| 描述
---------|----------
`Promise.resolve(promise);`  | 返回 promise（仅当 promise.constructor == Promise 时）
`Promise.resolve(thenable);` | 从 thenable 中生成一个新 promise。thenable 是具有 `then()` 方法的类似于 promise 的对象。
`Promise.resolve(obj);`	 | 	在此情况下，生成一个 promise 并在执行时返回 obj。
`Promise.reject(obj);` | 生成一个 promise 并在拒绝时返回 obj。为保持一致和调试之目的（例如堆叠追踪）， obj 应为 instanceof Error。
`Promise.all(array);` | 生成一个 promise，该 promise 在数组中各项执行时执行，在任意一项拒绝时拒绝。每个数组项均传递给 Promise.resolve，因此数组可能混合了类似于 promise 的对象和其他对象。执行值是一组有序的执行值。拒绝值是第一个拒绝值。
`Promise.race(array);` | 生成一个 Promise，该 Promise 在任意项执行时执行，或在任意项拒绝时拒绝，以最先发生的为准。


# Q.js 库

## 安装Q.js
```
npm i q --save
```
## 使用Q.nfcall和Q.nfapply
`Q.nfcall` 就是使用 **call的语法** 来返回一个promise对象，例如

```javascript
const fullFileName = path.resolve(__dirname, '../data/data1.json')
const result = Q.nfcall(fs.readFile, fullFileName, 'utf-8')  // 使用 Q.nfcall 返回一个 promise
result.then(data => {
    console.log(data)
}).catch(err => {
    console.log(err.stack)
})
```

`Q.nfapply` 就是使用 **apply的语法** 返回一个promise对象，例如
```javascript
const fullFileName = path.resolve(__dirname, '../data/data1.json')
const result = Q.nfapply(fs.readFile, [fullFileName, 'utf-8'])  // 使用 Q.nfapply 返回一个 promise
result.then(data => {
    console.log(data)
}).catch(err => {
    console.log(err.stack)
})

```

# 参考链接
- [MDN Promise](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)
- [developers.google.com: JavaScript Promise 简介](https://developers.google.com/web/fundamentals/getting-started/primers/promises)
- [medium: What is a Promise?](https://medium.com/javascript-scene/master-the-javascript-interview-what-is-a-promise-27fc71e77261)
- [Q.js 库](https://github.com/kriskowal/q)