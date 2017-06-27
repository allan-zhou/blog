---
title: javascript异步编程（0）目录
date: 2017-06-27 22:52:59
categories:
- 技术
- Javascript
tags:
- Javascript
- 异步编程
---

# 内容概述
- [什么是异步](/2017/06/27/javascript异步编程（1）什么是异步)
- [Promise](#Promise)
- [Generator](#Generator)
- [async/await](#async/await)

 <!-- more -->

我们先看一下异步操作代码的变化  

`callback`方式
```javascript
fs.readFile('some1.json', (err, data) => {
    fs.readFile('some2.json', (err, data) => {
        fs.readFile('some3.json', (err, data) => {
            fs.readFile('some4.json', (err, data) => {

            })
        })
    })
})
```

`Promise`方式
```javascript
readFilePromise('some1.json').then(data => {
    return readFilePromise('some2.json')
}).then(data => {
    return readFilePromise('some3.json')
}).then(data => {
    return readFilePromise('some4.json')
})
```

`Generator`方式
```javascript
co(function* () {
    const r1 = yield readFilePromise('some1.json')
    const r2 = yield readFilePromise('some2.json')
    const r3 = yield readFilePromise('some3.json')
    const r4 = yield readFilePromise('some4.json')
})
```

`async-await`方式
```javascript
const readFileAsync = async function () {
    const f1 = await readFilePromise('data1.json')
    const f2 = await readFilePromise('data2.json')
    const f3 = await readFilePromise('data3.json')
    const f4 = await readFilePromise('data4.json')
}
```