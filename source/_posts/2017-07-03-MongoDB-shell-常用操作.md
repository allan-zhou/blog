---
title: MongoDB shell 常用操作
date: 2017-07-03 22:59:21
categories:
- MongoDB
tags:
- MongoDB
---

# Create Operations 插入



# Read Operations 查询
```js
db.collection.find() //从 collection 中查找所有 documents
```
# Update Operations 更新
# Delete Operations 删除
```javascript
db.collection.remove({}) //从 collection 中删除所有 documents

db.collection.remove({name:"001"}) //根据条件删除 documents

db.collection.drop()// 从 db 中删除 collection
```


# 参考链接
- [mongodb doc](https://docs.mongodb.com/manual/mongo/)