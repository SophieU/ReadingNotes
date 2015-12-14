## 一、mongoDb
> 特性：
- 高可扩展性
- 分布式存储
- 低成本
- 结构灵活
### 1.1 window平台下安装MongoDB及环境搭建
- 下载安装包或压缩包
- 添加DB存储和日志存储文件夹
- 添加服务、配置环境变量、启动Mongo

> 为什么很多Node项目都搭配的是MongoDB而不是MySQL？？
- mongoDB原生支持json，可以用js语法进行操作
- mongoDB适用于关联性小，数据查询量大的操作。多查询，少写入。
- mongoDB不支持事务。MySQL需要使用SQL语句进行查询
- 用哪个其实看喜好。MySQL入门比MongoDB难一些。

> 1、下载安装
```
//下载地址
https://pan.baidu.com/s/1mhPejwO
//安装参考
http://www.imooc.com/article/18438
// 安装后设置MongoDB文件夹
|__ MongoDB
   |__data  //用于存放数据
   |__logs  //用于存放日志
   |__conf  //用于配置Mongo的服务，环境，启动等
      |__ mongo.conf
// 1、安装
安装后，默认放在C:\ProgramFiles\MongoDB

// 2、创建文件夹
在C盘或者其他任意盘，创建MongoDB文件夹，如上：

// 3、简单启动
// 在C:\Program Files\MongoDB\Server\3.4\bin中，打开cmd
mongod --dbpath c:\MongoDB\data 

// 4、进阶启动————配置环境变量
将mongodb的bin目录配置到环境变量中

// 5、配置环境变量后，可以任意位置通过cmd 启动mongo
mongod --dbpath c:\MongoDB\data

// 6、通过自定的配置文件来启动服务 
在mongo.conf文件中设置了所以配置信息，因此可能直接通过命令来启动
mongod --config c:\MongoDB\conf\mongo.conf

// 7、通过window服务来启动
    // 服务名为MongoDB时，我本机报了个错说名字占用，所以换了个名字装
    // 安装此服务后，默认是开机自启动服务，即开机就打开了mongodb。也可以通过在开始窗口搜索service，进到本机服务中手动启动、停止服务
mongod --config c:\MongoDB\conf\mongo.conf --install --serviceName "mongodb"

// 8、通过浏览器来访问mongo数据库
localhost:27017  //查看数据
localhost:28017  //查看数据库日志

// 注：mongodb为启动成功测试
通过 cmd页面，输入mongo命令，进入到对mongodb的操作，如果无法进入说明未启动mongodb
```

## 二、在Linux平台下安装MongoDB
- 下载安装
- 上传服务器、解压缩
- 添加db存储和日志存储文件夹

### 1、以阿里云服务器为例
> 登录服务器
- 通过shell登录
- ssh登录服务器（可传文件）
- 通过ftp登录并传输文件
> 详细ssh远程登录阿里云服务器步骤参考：
- 绑定/解绑SSH密钥对：https://help.aliyun.com/document_detail/51796.html?spm=5176.doc51798.2.5.5KCEh6
- 使用SSH密钥对连接Linux服务器：https://help.aliyun.com/document_detail/51798.html?spm=5176.doc25425.2.1.5rYfvB

```
// 1、绑定密钥对 —— 配置密钥对 —— 打开SSH工具远程连接服务器。
// 将阿里云生成的.pem么钥文件通过PuTTY转为.ppk私钥文件 (绑定密钥对后会生成pem)
// 通过PuTTY设置后，进行远程链接（参考https://help.aliyun.com/document_detail/51798.html?spm=5176.doc25425.2.1.5rYfvB）

// 2、成功打开远程服务器后，可以DOM窗口进行操作

// 3、在服务器上下载安装mongodb 
// 参考：http://www.runoob.com/mongodb/mongodb-linux-install.html


```
### 2、通过sftp方式上传文件到远程服务器中
- 工具：filezilla 百度可下
- 安装后，打开文件——站点管理——新建站点——配置服务器信息。
- 协议选择sftp
- 通过设置ssh登录信息后，点击连接实现ftp连接


### 3、mongoDB操作
> 1、使用mongo自带命令导入已有数据库
```
mongoimport -d db_demo -c Goods --file C:\Users\Tim\Desktop\goods.json
// db_demo:集合  users:数据文档  --file后跟文件所在地址
```
> 注：也可以导出mongoexport
> 2、常用命令
```
// 打开cmd 输入 mongo  高出mongodb命令工具
show dbs   查看数据库
use db_name  使用数据库
show collections 查看集合
db.collectionName.find() 查看集合中的数据
db.collectionName.findOne() 查找第一条数据
db.collectionName.update({"name":"a1"},{"age":10}) 更新(参数：查询条件，修改内容)
db.collectionName.remove({})  移除
db.collectionName.insert() 添加
db.collectionName.drop() 删除集合
db.collectionName.find().skip(num) 路过多少条
db.collectionName.find().limit(num) 限制几条数据
db.runCommand({"dropDatabase":1}) 删除当前数据库

 db.collection.find({ "key" : { $gt: value } })    // 查找key的值大于value的数据，$lt:小于 $gte >=  $lte <=  $ne 不等于
 db.collection.find({ "key.subkey" :value })   内嵌对象中的值匹配，注意："key.subkey"必须加引号。
 db.collection.find().sort({ "key1" : -1 ,"key2" : 1 })    这里的1代表升序，-1代表降序
```
### 4、mongoose
> Mongoose：是在node.js异步环境下对mongodb进行便捷操作的对象模型工具,一套操作MongoDB数据库的接口包.
> 使用方法 ： http://cnodejs.org/topic/51ff720b44e76d216afe34d9
```
// 1、安装mongoose 
npm install mongoose --save-dev

// 2、引入mongoose 
const mongoose = require('mongoose');

// 3、创建连接数据集合  testDB为数据库名
const db = mongoose.createCollection("mongodb://localhost/testDB");

// 4、监听事件
db.on('error',(error)=>{
    console.log('error:'+error)
})
db.once('open',(cb)=>{
    console.log('成功连接数据库')
})

```
#### 4.1 Schema
- schema：是mongoose里用到的一种数据模式，可以理解为表结构的定义；
- 每个schema会映射到mongodb中的一个collection，它不具备操作数据库的能力
```
var Schema = mongoose.schema()
// 定义schema
var User = new Schema({
    userName:{type:String,default:'匿名'},
    userpwd:{type:String},
    userage:{type:Number},
    logindate:{type:Date}
})
```
#### 4.2 Model
- model是由schema生成的实例模型，可以对数据库的操作
- Mongoose.model(name, [schema], [collection], [skipInit]) 注：collection是字符串类型
- name <String, Function> model name or class extending Model
- [schema] <Schema>
- [collection] <String> name (optional, inferred from model name)
- [skipInit] <Boolean> whether to skip initialization (defaults to false)
```
//创建模型实例
var Goods = mongoose.model('goodsinfo',Goods,'goodsinfo')
var goods = new Goods({
    productId:"1009",
    productName: "中兴",
    salePrice:1499,
    checked:'yes',
    productNum:2000,
    productImage: "5.jpg"
})
goods.save(function(err){
    if(!err){
        console.log('保存成功')
    }
})
```




