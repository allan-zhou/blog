---
title: MongoDB学习笔记（3）MongoDB CRUD
date: 2017-07-03 21:41:44
categories:
- MongoDB
tags:
- MongoDB
---

# Create Operations
![mongodb](/images/mongodb/crud-annotated-mongodb-insertOne.bakedsvg.svg)  
> 注意：
- insert operations 作用于单个 collection
- 所有 write operations 都具有原子性
- 如果 the collection 不存在，insert operations 会创建 the collection

## Insert Methods

方法名 | 描述 | 备注
---------|----------|---------
[db.collection.insert()](https://docs.mongodb.com/manual/reference/method/db.collection.insert/) | inserts a single document or multiple documents into a collection. | 
[db.collection.insertOne()](https://docs.mongodb.com/manual/reference/method/db.collection.insertOne/#db.collection.insertOne) | Inserts a single document into a collection. | New in version 3.2
db.collection.insertMany()  | inserts multiple documents into a collection. | New in version 3.2

## db.collection.insert()
语法：
```js
db.collection.insert(
   <document or array of documents>,
   {
     writeConcern: <document>,
     ordered: <boolean>
   }
)
```

## Write Concern
```js
{ w: <value>, j: <boolean>, wtimeout: <number> }
```

# Read Operations
![mongodb](/images/mongodb/crud-annotated-mongodb-find.bakedsvg.svg)

# Update Operations
![mongodb](/images/mongodb/crud-annotated-mongodb-updateMany.bakedsvg.svg)

# Delete Operations
![mongodb](/images/mongodb/crud-annotated-mongodb-deleteMany.bakedsvg.svg)

# 参考链接
- [mongodb docs](https://docs.mongodb.com/manual/crud/)