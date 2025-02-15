---
title: linux安装mongodb及常见命令
tags:
  - 数据库
copyright: true
comments: true
date: 2019-07-20 14:20:40
categories: 工具
photos:
top: 260
---

{% fi http://cdn.mydearest.cn/blog/images/mongodb.jpg, MongoDB, MongoDB %}

MongoDB 是一个基于分布式文件存储的数据库。由 C++ 语言编写。旨在为 WEB 应用提供可扩展的高性能数据存储解决方案。
---
<!--more-->

## 安装、配置
```shell
## 下载
curl -O https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-3.6.5.tgz

## 解压
tar -zxvf mongodb-linux-x86_64-3.6.5.tgz 

## 移动到指定目录
mv mongodb-linux-x86_64-3.6.5/ /usr/local/mongodb

## MongoDB 的可执行文件位于 bin 目录下，所以可以将其添加到 PATH 路径中：当前终端有效(可跳过)
export PATH=/usr/local/mongodb/bin:$PATH

## 到根目录创建文件夹 或者mongodb下
mkdir data/db
mkdir data/log

## vim /etc/profile 添加到全局命令
export PATH=/usr/local/mongodb/bin:$PATH 

## 如果有多个
export PATH=$JAVA_HOME/bin:$JRE_HOME/bin:/usr/local/mongodb/bin:$PATH

## 保存，退出，然后运行
source /etc/profile
```

```shell
## mongodb.conf
dbpath=/usr/local/mongodb/db
logpath=/usr/local/mongodb/log/mongodb.log
bind_ip=0.0.0.0
fork=true
```

```shell
## 关闭
ps -ef|grep mongod
kill -9

## 后台启动 开启用户认证
nohup mongod --auth -f /usr/local/mongodb/mongodb.conf > myLog.log 2>&1 &

nohup mongod -f /usr/local/mongodb/mongodb.conf > myLog.log 2>&1 &
```

## 常见命令

### 设置用户和命令
```shell
## 权限登录
mongo admin -u cosyer -p xxx

## 创建管理员
mongo

use admin

db.createUser(
  {
    user: "testuser",
    pwd: "testpassword",
    roles: [ { role: "readWriteAnyDatabase", db: "admin" } ]
  }
)

## 认证登录
db.auth("admin", "password")

## 显示当前系统用户
db.system.users.find()

## 删除用户(删除用户的时候需要切换到用户管理的数据库才可以删除)
db.dropUser("testuser")

## 修改密码
db.addUser('testUser','111')

db.changeUserPassword('tank2','test')

## 修改用户权限
db.updateUser("cosyer",{roles:[ {role:"root",db:"admin"} ]})
```

### mongoose账号密码连接
```js
// mongodb://admin:123456@localhost:27017 //有用户名密码的情况
mongoose.connect("mongodb://user:pwd@111.231.121.29/ticket", {
  authSource: "admin",
  useMongoClient: true
});
```

## 导入导出表字段
```js
mongoexport -d book -c books -o books.json --type json

mongoimport -d book -c books --file /home/mongodump/articles.json --type json
```

## 备份恢复数据库
```js
mongodump -h 127.0.0.1 -d book -o D:\iview-book-admin\static\js

mongorestore -h dbhost -d book --dir D:\iview-book-admin\static\js\book
```

## homebrew安装go
- [mac 使用brew 安装Python](https://www.jianshu.com/p/b821a8d1d8dc)

### 安装
```bash
brew install go 
```

### 配置golang的相关环境变量
***默认go 安装的文件地址是 /usr/local/Cellar/go/***
然后看一下自己的libexec在什么地方然后记录下整体的地址 我的地址是`/usr/local/Cellar/go/1.11.4/libexec`

### 将go的配置写入环境
```bash
cd ~
vim ./bash_profile

#GO
#GOROOT
export GOROOT=/usr/local/Cellar/go/1.11.4/libexec
#GOPATH
export GOPATH=$HOME/treasure/go
#Bin
export PATH=${PATH}:$GOPATH/bin

source ~/.bash_profile
```

### 检查环境变量和版本
```bash
go env
go version
```
- [Go系列](https://eddycjy.com/go-categories/)

## 安装java
- https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html
