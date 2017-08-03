---
title: About mongoDB
date: 2017-04-11 20:39:19
tags:
- 数据库
- mongoDB
catalogies:
- 学习笔记
---

# 前言

第二次使用了mongoDB了，记录下关于mongoDB的配置与一些操作知识点，关于mongoose的之后也会写一下。

## 更新

2017.4.12 推介一款mongoDB GUI管理工具[robomongo](https://robomongo.org/)

<!--more-->

# 安装

安装很简单，无非只有要不要写入环境变量这个地方。

```shell
export PATH=<mongoDB的安装目录>/bin:$PATH
```

# 配置

启动mongoDB有两种，一种直接将配置写入命令行中，类似：

`mongod --dbpath=data/db --logpath=log/log.txt --fork`

另一种是用配置文件，也就是最使用的方法：

`mongod -f /etc/mongod.conf`   //配置文件的位置自己决定

这里是[官方配置文件说明](https://docs.mongodb.com/manual/reference/configuration-options/#configuration-file)

再来一些中文的配置解释
- 来自[简书](http://www.jianshu.com/p/f179ce608391)
- 来自[博客园](http://www.cnblogs.com/zhoujinyi/p/3130231.html)

配置还是挺多的，来个我用的最简略版本：

```
  port=27017 #端口号                          
  fork=true #以守护进程的方式运行，创建服务器进程
  # master=true #单主从配置时设为主服务器
  #salve=true ##单主从配置时设为从服务器
  logpath=/home/xliez/database/data/log.log #日志输出文件路径
  logappend=true #日志输出方式
  dbpath=/home/xliez/database/data/db/ #数据库路径
  # replSet=testrs #设置富本集的名字
  # shardsvr=true #设置是否分片
  # auth=true#是否开启授权
```

## 关于认证

mongoDB默认是没有用户和密码的，只要连接就能登录上，这很不安全，看[这里](http://www.jianshu.com/p/a4e94bb8a052)来给它加上管理员用户，记得将配置文件中的auth开启！

# 操作

OK，现在来后台启动mongoDB

```shell
➜  bin ./mongod -f ~/database/data/mongodb.conf 
about to fork child process, waiting until server is ready for connections.
forked process: 20425
child process started successfully, parent exiting
```

然后启动mongoDB自带的shell进行一些操作吧

```shell
➜  bin ./mongo
MongoDB shell version v3.4.2
connecting to: mongodb://127.0.0.1:27017
MongoDB server version: 3.4.2
```

如果不了解mongoDB的一些基本操作的，可以看下这里[mongoDB教程](http://www.runoob.com/mongodb/mongodb-tutorial.html)，在这里就不赘述了。

# 一些注意点

这里会不时更新下一些使用过程中的坑

## _id 与 ObjectId

_id是mongoDB默认自带的主键，数据类型是ObjectId()，如果你插入的数据中没有id主键的话就会自动生成。要在mongo shell之外的地方使用它的话记得要使用`ObjectId().str`改变数据类型为string格式。