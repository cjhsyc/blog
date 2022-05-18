---
title: MongoDB
date: 2022-04-10 17:41:39
categories: 
- [MongoDB]
- [Node]
tags: 
- MongoDB
- mongoose
- express
- apiDoc
---

# 安装（win系统）

## 下载

[下载地址](https://www.mongodb.com/try/download/community)

下载zip压缩包，并解压。

## 创建服务

在`mongodb`目录下创建两个目录`data`和`logs`，分别用于存放数据和日志（和`bin`目录同级）

管理员身份运行cmd，进入`mongodb`下的`bin`目录。

cmd下进行安装：（两个path后输入自己新建的两个目录的地址，`logs`目录后需要加上`mongodb.log`）

```
mongod --install --dbpath D:\MongoDB\data --logpath D:\MongoDB\logs\mongodb.log
```

没有报错即为成功

验证：键盘点击win+R，输入`services.msc`，能找到MongoDB即可

## 启动服务

cmd中：

```
net start mongodb #启动
mongo #进入mongodb
exit #退出
```



# 基本使用

`MongoDB/bin`下打开cmd：输入`mongo`开始使用

- 查看数据库：`show databases`
- 选择数据库：`use test1`（隐式创建：选择不存在的数据库不会报错，如果之后该数据库有数据时会自动创建）
- 查看集合：`show collections`
- 创建集合：`db.createCollection('集合名')`
- 删除集合：`db.集合名.drop()`

# 增删改查

1. 插入数据：`db.集合名.insert(JSON数据)`

   ```
   use test2
   db.c1.insert({uname:'luo',age:18}) 
   #注：集合不存在则隐式创建
   #注：MongoDB会给每条数据添加一个_id(全球唯一)
   ```

   一次性插入多条数据：插入数组即可

   多次插入数据：mongodb支持部分js语法，所以可以使用循环：

   ```js
   for(var i=0;i<3;i++){
       db.c1.insert({uname:'a'+i,age:i}) 
   }
   ```

2. 查询数据：`db.集合名.find()`

   ```
   db.c1.find() #查询所有数据
   #输出：{ "_id" : ObjectId("6252c272dbdf6baae7f71b64"), "uname" : "luo", "age" : 18 }
   { "_id" : ObjectId("6252e60235f4e186f4e8dbfa"), "uname" : "a0", "age" : 0 }
   { "_id" : ObjectId("6252e60235f4e186f4e8dbfb"), "uname" : "a1", "age" : 1 }
   { "_id" : ObjectId("6252e60235f4e186f4e8dbfc"), "uname" : "a2", "age" : 2 }
   #注：MongoDB会给每条数据添加一个_id(全球唯一)
   ```

   格式化输出数据：`db.集合名.find().pretty()`

   条件查询：`db.集合名.find(条件)`

   ```
   db.c1.find({age:1}) #age=1的数据
   db.c1.find({uname:'a0',age:0}) #多条件查询
   ```

   其他运算符：`db.集合名.find({键:{运算符:值})`

   | `运算符` | `作用`   |
   | -------- | -------- |
   | `$gt`    | 大于     |
   | `$gte`   | 大于等于 |
   | `$lt`    | 小于     |
   | `$lte`   | 小于等于 |
   | `$ne`    | 不等于   |
   | `$in`    | in       |
   | `$nin`   | not in   |

   ```
   db.c1.find({age:{&gt:1}) #age>1的数据
   db.c1.find({age:{&in:[0,2]) #age=0或2的值
   ```

   查询列：（传入第二个参数）

   ```
   db.c1.find({},{age:1}) #只显示age列
   db.c1.find({},{age:0}) #显示除了age列的其他列
   #注：无论何时_id都会显示
   ```

3. 修改数据：`db.集合名.update(条件,新数据)`

   ```
   db.c1.update({uname:'luo'},{uname:'zhang'}) #不只是修改uname，而是直接用新数据替换（所以修改后age没有了）
   #注：默认只修改符合条件的第一条数据
   ```

   其他运算符：

   | 修改器  | 作用     |
   | ------- | -------- |
   | $inc    | 递增     |
   | $rename | 修改列名 |
   | $set    | 修改列值 |
   | $unset  | 删除列   |

   ```
   db.c1.update({uname:'luo'},{$set:{uname:'zhang'}}) #只修改uname，所以age还在
   db.c1.update({uname:'luo'},{$inc:{age:3}}) #age增加3（负数为减）
   db.c1.update({uname:'luo'},{ #同时进行多种修改
   	$set:{uname:'zhang'},
   	$inc:{age:3}
   })
   ```

   是否新增：第三个参数（true：未匹配到数据则新插入一条数据，false：默认值，不新增）

   是否修改多条：第四个参数（true：修改所有匹配的数据，false：默认值，修改匹配到的第一条数据）

4. 删除数据：db.集合名.remove(条件)

   是否只删除一条：第二个参数（true：只删除第一条数据，false：默认值，删除所有匹配的数据）

# 排序和分页

1. 排序：`db.集合名.find().sort({键：值})`

   ```
   db.c1.find().sort({age:0}) #根据age进行排序，1表示升序，-1表示降序
   ```

2. 分页：`db.集合名.find().skip(数字).limit(数字)`

   ```
   db.c1.find().limit(2) #查询两条数据，limit：限制查询的数量
   db.c1.find().skip(1) #skip：跳过指定的数量
   db.c1.find().skip(1).limit(2) #一起使用时，skip写在前面
   db.c1.find().count() #count:计数，显示结果的条数
   ```

# 聚合查询

```
db.集合名.aggregate([
	{管道:{表达式}}
	...
])
```

常用管道

```
$group	将集合中的文档分组，用于统计结果
$match	过滤数据，只要输出符合条件的文档
$sort	聚合数据进一步排序
$skip	跳过指定文档数
$limit	限制集合数据返回文档数
```

常用表达式

```
$sum	总和
$avg	平均
$min	最小值
$max	最大值
```

- 分组

  ```
  db.c1.aggregate([
  	{
  		$group:'$sex',  # $group：分组，按sex进行分组，sex前需加上$符
  		sum:{$sum:'$age'} # $sum：总和，同一组的age的总和，age前需加上$符，并将结果显示在sum列中
  	}
  ])
  
  db.c1.aggregate([
  	{
  		$group:null, # $group为null表示不分组
  		sum:{$sum:1}, # $sum为1表示统计每组的数据条数
  		avg:{$avg:'$age'} # $avg：平均值
  	}
  ])
  ```

- 多个管道

  ```
  db.c1.aggregate([
  	{
  		$group:'$sex',
  		sum:{$sum:'$age'}
  	}，
  	{
  		$sort:{sum:1} #sort:排序，sum:1表示按照sum升序
  	}
  ])
  ```

# 索引

索引是一个排序好的数据结构，可以提高数据查询的效率，但大量索引也会影响数据，因为每次插入和修改数据都需要更新索引

- 创建索引：`db.集合名.createIndex(待创建索引的列)`

  ```
  db.c1.createIndex({uname:1}) #按照name字段升序创建索引
  db.c1.createIndex({uname:1,age:1}) #复合索引
  ```

  自定义索引名：(第二个参数对象中，name:指定索引名)

  ```
  db.c1.createIndex({uname:1},{name:'unameIndex'})
  ```

  唯一索引：(第二个参数对象中，unique:是否唯一)

  ```
  #建立唯一索引后，所有数据的uname不能重复
  db.c1.createIndex({uname:1},{unique:true})
  ```

- 查看索引：`db.集合名.getIndexes()`

  ```
  db.c1.getIndexes()
  #结果：
  [
          {
                  "v" : 2,
                  "key" : {
                          "_id" : 1
                  },
                  "name" : "_id_"
          },
          {
                  "v" : 2,
                  "key" : { #根据哪个key建立的索引
                          "uname" : 1
                  },
                  "name" : "uname_1" #索引名
          }
  ]
  ```

- 删除索引：`db.集合名.dropIndex(索引名)`

  ```
  #先通过getIndexes()查看索引名
  db.c1.dropIndex('uname_1')
  db.c1.dropIndexes() #删除全部索引（除系统自带的）
  ```

## 分析（explain）

explain()帮助我们查看此次查询的相关数据（是否使用索引查询，查询速度）

基本使用：

```
db.c1.find({age:1}).explain()
```

# 权限机制

## 开启验证模式

1. 添加超级管理员（输入`mongo`进入MongoDB后`use admin`）

   ```
   #创建账号
   db.createUser({
   	"user":"账号",
   	"pwd":"密码",
   	"roles":[{
   		role:"角色",
   		db:"所属数据库" #就是admin
   	}]
   })
   #比如：
   db.createUser({
   	user:'admin',
   	pwd:'123456',
   	roles:[{
   		role:'root',
   		db:'admin'
   	}]
   })
   ```

2. 卸载服务（管理员身份运行cmd）

   ```
   #在MongoDB/bin目录下执行
   mongod --remove
   ```

3. 安装需要身份验证的MongoDB服务（添加`--auth`）

   ```
   mongod --install --dbpath D:\MongoDB\data --logpath D:\MongoDB\logs\mongodb2.log --auth
   注：.log文件名不能和之前安装时的重复
   
   #启动服务
   net start mongodb
   ```

4. 登录

   ```
   #进入MongoDB
   mongo
   #查看数据库，结果为空，因为没有身份验证
   show dbs
   ```

   通过超级管理员登录：

   1. 方式一：`mongo IP地址:端口/数据库 -u 用户名 -p 密码`

      ```
      #默认端口：27017
      mongo 127.0.0.1:27017/admin -u admin -p 123456
      #即可查看数据库
      show dbs
      ```

   2. 方式二：

      ```
      #先进入
      mongo
      #使用数据库
      use admin
      #登录(输出1：表示成功)
      db.auth('admin','123456')
      ```

   ## 创建其他角色

   ```
   #角色种类
   超级用户角色:root
   数据库用户角色：read,readWrite
   数据库管理角色:dbAdmin,userAdmin
   集群管理角色:clusterAdmin、clusterManager、clusterMontitor、hostManager;
   备份恢复角色:backup、restore;
   所有数据库角色:readAnyDatabase、readWriteAnyDatabase、userAdminAnyDatabase、dbAdminAnyDatabase
   
   #角色说明
   root：只在admin数据库中可用。超级账号，超级权限
   read：允许用户读取指定数据库
   readWrite：允许用户读写指定数据库
   dbAdmin：允许用户在指定数据库中执行管理函数，如索引创建、删除、查看统计或访问system.profile
   ```

   例：创建角色test1可以读写test1数据库

   ```
   use test1
   db.createUser({
   	user:'test1',
   	pwd:'test1',
   	roles:[{
   		role:'readWrite',
   		db:'test1'
   	}]
   })
   #注：创建的角色信息存在admin数据库下的system.users
   use admin
   db.system.users.find().pretty()
   ```

   退出，并登录为test1后，只能读写test1数据库

# 备份还原

## 安装MongoDB Database Tools

[下载地址](https://www.mongodb.com/try/download/database-tools)

选择zip进行下载，解压后将`bin`目录下的内容复制到`MongoDB/bin`下即可

## mongodump(备份)

语法：

```
# 导出数据
mongodump -h -port -u -p -d -o
# 说明
# -h	host 	服务器IP地址(一般不写 默认本机)
# -port		 	端口(一般不写 默认27017)
# -u	user 	账号
# -p 	pwd	 	密码
# -d	database数据库(注意：数据库不写则导出全局)
# -o	open	备份到指定目录下
```

备份所有数据：

```
# MongoDB/bin下执行，需新建一个备份目录（bak）
mongodump -u admin -p 123456 -o D:\MongoDB\bak
```

备份指定数据：

```
mongodump -u test1 -p test1 -d test1 -o D:\MongoDB\bak2
# 注：超级管理员只能备份全部数据库，不能备份单个数据库
```

## mongoerstore（还原）

语法：

```
mongorestore -h -port -u -p -d --drop 备份数据目录
#说明:
# -d		不写则还原全部数据库
# --drop	表示先删除数据库再导入
```

还原所有数据库

```
mongorestore -u admin -p 123456 --drop D:\MongoDB\bak
```

还原指定数据库

```
mongorestore -u test1 -p test1 -d test1 --drop D:\MongoDB\bak2\test1
```

# mongoose

是Node中提供的用来操作MongoDB的模块

[mongoose文档](http://www.mongoosejs.net/)

安装:

```
yarn add mongoose
或
npm i mongoose
```

使用：（js文件，node命令运行）

```js
//导入mongoose 
const mongoose = require('mongoose')
//连接数据库(参数1：url，参数2：options，参数3：回调函数)
const db = mongoose.connect('mongodb://admin:123456@localhost/admin', {}, error => {
  if (error) {
    console.log('error:' + error)
  } else {
    console.log('ok')
  }
})
```

## Schema

Mongoose 的一切始于 Schema。每个 schema 都会映射到一个 MongoDB 集合，并定义这个集合里的文档的构成。

```js
const Schema = mongoose.Schema;

const mySchema = new Schema({
  name: String,
  age: Number,
  date: {type: Date, default: Date.now()} //date字段的数据类型是Date，默认值为Date.now
});
```

允许使用的 SchemaTypes 有:

- String
- Number
- Date
- Buffer
- Boolean
- Mixed
- ObjectId
- Array

## Model

`Model`是从 `Schema` 编译来的构造函数。 它们的实例就代表着可以从数据库保存和读取的 documents（文档）。 从数据库创建和读取 document 的所有操作都是通过 model 进行的。

```js
//参数1：集合名（不是s结尾会自动加上s）
const model = mongoose.model('user', mySchema)
```

添加数据：

```js
const insertObj = new model({
  name: '罗小黑',
  age: 8
})
insertObj.save().then(res => {
  console.log(res)
})
```

查询数据：

```js
//查询一条
model.findOne({age: 8}).then(res => {
  console.log(res)
})
//查询所有
model.find({age: 8}).then(res => {
  console.log(res)
})
//跳过和分页
model.find({age: 8}).skip(1).limit(1).then(res => {
  console.log(res)
})
```

# 接口

接口就是一个文件（js/json/php等），主要响应JSON数据或XML数据。

推荐的JSON数据格式：

```
{
	meta:{
		status:状态码,
		msg:'提示信息'
	},
	data:数据
}
```

## 接口开发规范（Restful API）

Restful API：提供了接口设计规则和约束条件（一个规范），统一开发标准，便于团队协作。

举例：

```
列表页:访问-/模块名				 (get)
详情页:访问-/模块名/编号			(get)
添加页:访问-/模块名/create		 (get)
处理:访问-/模块名				  (post)
修改页:访问-/模块名/编号/edit		(get)
处理:访问-/模块名/编号			 (put)
删除:访问-/模块名/编号			(delete)

HTTP动词: get、post、put、delete
```

## 接口开发

使用express框架：

```
yarn add express
```

新建`http.js`

```js
const express = require('express')
const app = express()

app.listen(3000,()=>{
  console.log('http://localhost:3000')
})

app.get('/',((req, res) => {
  res.send('hello!')
}))
```

## 实战练习

编写学生添加接口：

新建`models/stu.js`:(操作数据库)

```js
const mongoose = require('mongoose')
mongoose.connect('mongodb://test1:test1@localhost/test1', {}, error => {
  if (error) {
    console.log('数据库连接失败:' + error)
  } else {
    console.log('数据库连接成功')
  }
})

const Schema = mongoose.Schema;

const mySchema = new Schema({
  name: String,
  age: Number,
  sex: String
});

const model = mongoose.model('stu', mySchema)

//添加学生
const insert = (data) => {
  const obj = new model(data)
  return obj.save().then(res => {
    console.log('添加数据成功')
    return res
  }, err => {
    console.log('添加数据失败：' + err)
  })
}

//获取学生列表
const findStus = (skip,limit) => {
  return model.find().skip(skip).limit(limit).then(res => {
    console.log('获取数据成功')
    return res
  }, err => {
    console.log('获取数据失败：' + err)
  })
}

module.exports = {
  insert,findStus
}
```

新建`constroller/stu.js`:(业务逻辑)

```js
const path = require('path')
const {insert, findStus} = require(path.resolve(__dirname, '../models/stu'))

//添加学生
const create = async (req, res) => {
  const data = req.body
  const result = await insert(data)
  if (result) {
    res.send({
      meta: {
        status: 200,
        msg: 'ok'
      },
      data: result
    })
  } else {
    res.send({
      meta: {
        status: 500,
        msg: 'error'
      }
    })
  }
}

//获取学生列表
const getStus = async (req, res) => {
  const {pageno, pagesize} = req.query //获取页数和每页显示的数量
  const result = await findStus((pageno - 1) * pagesize, pagesize)
  if (result) {
    res.send({
      meta: {
        status: 200,
        msg: 'ok'
      },
      data: result
    })
  } else {
    res.send({
      meta: {
        status: 500,
        msg: 'error'
      }
    })
  }
}

module.exports = {
  create,
  getStus
}
```

安装body-parser：（在 Express 中没有内置获取表单 POST 请求体的 API , 我们需要添加第三方插件库）

```
yarn add body-parser
```

`http.js`:

```js
const express = require('express')
const path = require('path')
const bodyParser = require('body-parser')
const app = express()

//配置 body-parser 中间件 (插件, 专门用来解析表单 POST 请求)
// parse application/x-www-form-urlencoded
app.use(bodyParser.urlencoded({extended: false}))
//parse application/json
app.use(bodyParser.json())

app.listen(3000, () => {
  console.log('http://localhost:3000')
})

app.get('/', ((req, res) => {
  res.send('hello!')
}))

//绝对路径
const stu = require(path.resolve(__dirname, 'controller/stu'))

//添加学生
app.post('/stu', stu.create)
//获取学生列表
app.get('/stu',stu.getStus)
```

使用接口调试工具（postman等）进行测试

## 接口文档（apiDoc）

[apiDoc文档](https://apidocjs.com/)

apiDoc是node的一个模块，能够根据注释快速生成接口文档。

全局安装：

```
npm i apidoc -g
```

项目根目录新建`apidoc.json`：

```
{
  "name": "接口文档名",
  "version": "0.1.0",
  "description": "接口文档描述",
  "title": "Custom apiDoc browser title",
  "url" : "http://loaclhost:3000"
}
```

编写注释：（在实现业务逻辑的函数前写注释）

```
/**
 * @api {get} /user   学生列表
 * @apiName GetUser
 * @apiGroup User
 *
 * @apiParam {Number} pageno     获取第几页
 * @apiParam {Number} pagesize   每页显示条数
 *
 * @apiSuccess {Object} meta 状态码&提示
 * @apiSuccess {Array} data  数据
 */
```

生成接口文档：`apidoc -i 接口注释所在目录 -o 接口文档生成目录`

```
apidoc -i ./controller -o ./apidoc
# 根目录执行
```

生成`apidoc`目录，该目录下的`index.htm`l即为接口文档





[参考视频](https://www.mongodb.com/try/download/community)

