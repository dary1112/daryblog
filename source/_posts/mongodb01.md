---
title: mongoDB的安装和使用mongoose
date: 2020-03-03 00:07

categories:
- 后端
tags:
- node
- mongodb
---

**本文主要介绍mongoDB的安装启动以及使用mongoose实现基本的增删改查**

@[toc]

<br>

## 数据库

什么是数据库？相信看到我这篇文章的读者不会有这样的疑问了吧。数据库就是**按照数据结构来组织、存储和管理数据的仓库**。而常见的数据库可以分为两大类，分别是关系型数据库和非关系型数据库，而这两者的代表无疑就是MySql和MongoDB了，下面我列出了这两种数据库的概念对比：

| MySql                   | MongoDB              |
| ----------------------- | -------------------- |
| 关系型数据库            | 非关系型数据库       |
| 数据存储使用二维表      | 数据存储采用json格式 |
| 表结构相对固定的        | 数据结构更加灵活     |
| database（数据库）      | database（数据库）   |
| table（表）             | collection（集合）   |
| row（行）               | document（文档）     |
| col（列）               | filed（字段）        |
| 使用sql语句进行增删改查 | api直接完成增删改查  |

```javascript
// mongoDB的一个collection里数据大概可以是这样的json结构
[
    {id: 1, name: 'zhangsan', age: 19},
    {id: 2, name: 'lisi', gender: 'male'}
]
```



## 安装和启动

1. 下载mongoDB服务：https://www.mongodb.org/dl/win32/   选择3.1.2的版本是最容易安装的，当然如果最自己的电脑环境有足够的自信也可以选择较新版本

2. 安装

   ![mongoDB安装](/img/article/mongoDB安装.png 'mongoDB安装')

   注意这里要选择custom，其他每步都直接下一步安装即可

3. 新建一个空目录，位置不要太深，比如： C:\db ， 这个目录是用来存放数据文件的

4. 运行mongoDB服务：进入mongoDB安装路径下的bin目录里，比如：C:\Program Files\MongoDB\Server\4.0\bin（这是作者的mongoDB服务安装路径，读者要自行查找并进入，默认目录一般是类似这样的），打开命令行工具，git bash 或者powershell那个能用用哪个（此处不必纠结，很有可能环境原因会导致powershell或者cmd能用但是git bash不能用）

5. 执行`mongod --dbpath c://db`（这里的C://db就是我们在第三步新建的空目录，需要替换成你自己的）。只要没有出现报错，如下图所示说明服务启动成功，默认端口号就是27017，这个窗口不要关闭！！！

   ![mongoDB服务启动](/img/article/mongoDB服务启动.png 'mongoDB服务启动')

6. 我们可以使用可视化工具对数据库进行查看，如：robo3t，navicat for mongoDB



## 使用mongoose

#### mongoose介绍

我们操作数据库时可以使用mongoDB的api来做，但是这些api使用起来可能并没有那么友好，因此给大家介绍一个包`mongoose`，官网 https://mongoosejs.com/docs/index.html 对mongoose的定义如下：

> elegant mongodb object modeling for node.js
>
> Mongoose是在node.js环境下对mongodb进行便捷操作的对象模型

这里的着落点在**对象模型**，什么是对象模型？以前我们学过 **BOM（浏览器对象模型）**和 **DOM（文档对象模型）**。BOM的顶级对象是`window`，这个对象里包含了各种操作浏览器的子对象；DOM的顶级对象是`document`，这个对象里也有各种属性和对象可以操作文档，那么我们的mongoose是mongoDB对象模型，所以同理可得，mongoose也有一个顶级对象，这个对象就是在这个包里定义的，我们可以导入他用来操作mongoDB，比mongoDB本身的api更好用，因为他是二次封装过的。

#### mongoose使用步骤

以本地localhost的admin数据库里的users这个collection为例来说明使用步骤

1. 创建一个空的目录，进入此目录，使用命令`npm init -y`初始化项目，然后执行`npm i mongoose` 安装mongoose依赖包

2. 引入模块，连接数据库

   ```javascript
   // 引入包
   const mongoose = require('mongoose')
   
   // 连接本地的admin数据库
   mongoose.connect('mongodb://localhost/admin', {useNewUrlParser: true})
   const db = mongoose.connection
   db.on('error', console.error.bind(console, 'connection error:'))
   db.once('open', function() {
     // 这里就代表成功连接了
     console.log("we're connected!")
   })
   ```

3. 得到一个schema，schema是定义数据结构，讲本身非关系型数据库做一个结构化处理，因为虽然非关系型数据库结构可以更加灵活，但是在实际项目中这并不一定是优点，很多时候我们还是希望数据更加整齐，而不是松散

   ```javascript
   // new一个schema：把一个本身非结构化的数据变成结构化
   const userSchema = new mongoose.Schema({
     name: String,
     password: String,
     age: Number
   })
   ```

4. 基于schema得到一个model，model是把schema跟collection进行关联，

   ```javascript
   // 根据schema得到一个model，这个model是一个class
   // 这个方法的第一个参数就是collection的名称，这个名称必须要加s，这里不加的话也会默认帮我们加上，所以自己的事情自己做，加上他吧
   const User = mongoose.model('users', userSchema)
   ```

5. 操作数据库

   * 新增一条：把document数据作为参数传递，得到一个model的实例，这个实例调用save方法就可以存入数据库了

     ```javascript
     // new一个model
     var dary = new User({ name: 'dary', password: '123456', age: 18 })
     
     // 把一条document存入collection里，save是model实例的方法，所以是dary来调用
     dary.save(function (err, user) {
       if (err) return console.error(err);
       console.log(user)
     })
     ```

   * 查询数据：直接调用model的find方法

     ```javascript
     // 查询所有数据
     User.find((err, docs) => {
       if (err) return console.error(err)
       console.log(docs)
     })
     
     // 查询name为dary的数据
     Position.find({ name: 'dary' }, (err, docs) => {
       if (err) return console.error(err)
       console.log(docs)
     })
     
     // 查询salsary>=20的数据，$gt大于；$lt小于；$lte小于等于
     User.find({ age: {$gte: 20 } }, (err, docs) => {
       if (err) return console.error(err)
       console.log(docs)
     })
     
     // 查询条件支持正则，查询name以字母d开头的数据
     User.find({ name: /^d/ }, (err, docs) => {
       if (err) return console.error(err)
       console.log(docs)
     })
     
     // 查询name里包含字母d的数据的age和gender
     User.find({ title: /d/ }, 'age gender', (err, docs) => {
       if (err) return console.error(err)
       console.log(docs)
     })
     
     // 查询name里包含字母d的数据skip跳过一条limit只查询两条，意味着这里只能拿到第二条和第三条数据
     User.find({ name: /d/ }, null, { skip: 1, limit: 2 }, (err, docs) => {
       if (err) return console.error(err)
       console.log(docs)
     })
     ```

   * 更新：update、updateOne（更新一条）和updateMany（更新多条），三个方法用法一致，结果不同，以其中一个为例

     ```javascript
     // 找到第一个满足条件的并更新：把年龄小于20的密码改为666
     User.update({ age: { $lt: 20 } }, { password: '666' }, (err, obj) => {
       if (err) return console.error(err)
       // obj里有三个属性
       //  n: 条件匹配到的数据条数
       //  ok: 1代表语句执行成功
       //  nModified: 更新的数量
       console.log(obj)
     })
     ```

   * 删除：deleteOne和deleteMany，删除一条和删除多条

     ```javascript
     // 将年龄为40的数据删除掉
     User.deleteOne({ age: 40 }, (err, obj) => {
       if (err) return console.error(err)
       // obj里有三个属性
       //  n:条件匹配到的数据条数
       //  ok: 1代表语句执行成功
       //  deletedCount： 被删除的数量
       console.log(obj)
     })
     ```

