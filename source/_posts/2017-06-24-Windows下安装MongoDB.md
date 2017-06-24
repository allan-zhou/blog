---
title: Windows下安装MongoDB
date: 2017-06-24 15:21:10
categories:
- 技术
- 数据库
tags:
- MongoDB
---

# 下载MongoDB
官网下载：[mongodb-win32-x86_64-2008plus-ssl-3.4.5-signed.msi](https://www.mongodb.com/download-center)

 <!-- more -->

# 安装MongoDB
双击安装包，一路next。
其中，可修改默认安装路径如下：
![mongodb01](/images/mongodb/mongodb01.png)
![mongodb02](/images/mongodb/mongodb02.png)

本例使用默认安装路径`C:\Program Files\MongoDB\Server\3.4`

# 配置环境变量Path
将MongoDB的bin目录`C:\Program Files\MongoDB\Server\3.4\bin`加入系统环境变量 Path

# 配置Mongo的 data 目录
MongoDB需要一个data目录来存储所有数据。它的默认data目录位于安装盘的根目录`\data\db`。  
安装MongoDB时并没有创建此文件夹，需要我们手动创建。
```
md C:\data\db
```

# 启动MongoDB
```
mongdb
```
![mongodb03](/images/mongodb/mongodb03.png)
如果提示 `waiting for connections on port 27017`，代表启动成功。 **27017**是MongoDB的默认端口。  
此时发现目录`C:\data\db`初始化很多数据文件

# 连接MongoDB
```
mongo
```
![mongodb04](/images/mongodb/mongodb04.png)
提示MongoDB Shell的版本，并提示输入，代表连接成功。  
我们输入`db`，发现MongoDB默认连接的是test数据库。

# 配置MongoDB为windows服务
首先，需要先创建mongod的配置文件。  
创建配置配置文件`C:\Program Files\MongoDB\Server\3.4\mongod.cfg`

> systemLog:  
&nbsp;&nbsp;&nbsp; destination: file  
&nbsp;&nbsp;&nbsp; path:c:\data\log\mongod.log  
storage:  
&nbsp;&nbsp;&nbsp; dbPath: c:\data\db  

安装windows服务
```
mongod --config "C:\Program Files\MongoDB\Server\3.4\mongod.cfg" --install
```

# 启动/关闭 MongoDB服务
启动MongoDB服务
```
net start MongoDB
```
关闭MongoDB服务
```
net stop MongoDB
```

# 卸载MongoDB服务

```
mongod --remove
```
以上命令仅卸载MongoDB服务。  
MongoDB的数据文件、程序文件，需手动删除。