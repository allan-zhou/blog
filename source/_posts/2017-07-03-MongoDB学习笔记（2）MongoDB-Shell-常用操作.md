---
title: MongoDB学习笔记（2）MongoDB Shell 常用操作
date: 2017-07-03 21:40:52
categories:
- MongoDB
tags:
- MongoDB
---
# collection 相关方法
## Create Operations 插入
```js
db.city.insertOne({name:"北京"}) // New in version 3.2

db.city.insertMany([{name:"上海"},{name:"广州"}]) // New in version 3.2

db.city.insert({name:"北京"})
db.city.save({name:"北京"})
```
![mongodb](/images/mongodb/mongodb-shell-01.png)

详见：[db.collection.insert()](https://docs.mongodb.com/manual/reference/method/db.collection.insert/)、[db.collection.insertOne()](https://docs.mongodb.com/manual/reference/method/db.collection.insertOne/)、[db.collection.insertMany()](https://docs.mongodb.com/manual/reference/method/db.collection.insertMany/)

## Read Operations 查询
```js
db.city.find() //从 collection 中查找所有 documents

db.city.find().count() // documents 总数
db.city.count() // 

db.city.distinct("name") // [ "北京", "上海" ]
```
## Update Operations 更新
```js
db.city.updateOne({name:"北京"},{$set:{desc:"beijing"}})
```
## Delete Operations 删除

```js
db.dropDatabase() // 删除当前 database
```
```js
db.collection.remove({}) //从 collection 中删除所有 documents

db.collection.remove({name:"001"}) //根据条件删除 documents
db.collection.remove({age:{$lt:50}})

db.collection.drop()// 从 db 中删除 collection
```


# 参考链接
- [mongodb doc](https://docs.mongodb.com/manual/reference/method/)
- [Query Selectors](https://docs.mongodb.com/manual/reference/operator/query/)