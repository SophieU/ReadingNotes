## 一、在linux下安装配置node
> 在阿里云服务器端 安装配置 nodejs环境
- wget https://nodejs.org/dist/v8.4.0/node-v8.4.0-linux-x64.tar.xz
- xz -d node-v8.4.0-linux-x64.tar.xz / tar -xzvf node-v8.4.0-linux-x64.tar.gz（根据下载的格式不同.xz,.gz用不同的解压命令）
- tar -xvf node-v8.4.0-linux-x64.tar 
- ln -s /node-v8.4.0-linux-x64/bin/node /usr/local/bin/node
- ln -s /node-v8.4.0-linux-x64/bin/npm /usr/local/bin/npm
### 1、直接在Linux服务器上下载安装Node
```
// 在linux环境下，通过命令 wget获取
// 安装包地址从node官网获取
wget https://nodejs.org/dist/v8.4.0/node-v8.4.0-linux-x64.tar.xz

// 对文件进行解压 xz -d 
xz -d node-v8.4.0-linux-x64.tar.xz / tar -xzvf node-v8.4.0-linux-x64.tar.gz

// 再解压(.xz的文件解压一次后，会生成.tar格式的压缩包，需要再解压)
tar -xvf node-v8.4.0-linux-x64.tar

// 建软连接，指向node和npm
// 软连接 link ，参考shell命令
- ln -s /node-v8.4.0-linux-x64/bin/node /usr/local/bin/node
- ln -s /node-v8.4.0-linux-x64/bin/npm /usr/local/bin/npm 
```
> 总结：
1. 通过 wget [option] [url] 获取对应url文件 ———— 即下载安装包（node源文件,解压即可用）
2. 也可以通过在本地下载源文件，然后通过ftp,或ssh的方式上传到远程服务器上
3. 命令：xz，tar 解压源文件
4. ln 命令建立软链接 ———— 即将node命令加入到全局，方便随时调用
5. linux相关命令参考其他资料

## 二、node简介
> 文档地址：http://nodejs.cn/api
### 1、 特性
- 基于Chrome V8引擎
- 单线程
- 使用JavaScript开发后端代码
- 非阻塞IO
### 2、细节点
> A、exports 和 module.exports的区别
- module.exports 初始值为一个空对象{}
- exports 是指向的module.exports的引用
- require()返回的是module.exports而不是exports
- 当使用module.exports = object时，module.exports被深覆盖，此时module.exports与exports的链接断开
- 最终导出的是 module.exports对应的内容
- 当module.exports与exports断裂时，导出的模块内容为module.exports，所以最好使用module.exports

> B、Path模块
- Path模块是Node自带的公共模块之一，用于处理项目中的路径问题
- path.join() ： 方法使用平台特定的分隔符把全部给定的 path 片段连接到一起，并规范化生成的路径
```
    path.join('/foo','bar','baz')
    // 返回 '/foo/bar/baz'
```
- path.posix: 属性提供了 path 方法针对 POSIX 的实现。(posix可移植操作系统接口，用于系统平台统一标准，适应于多系统的标准)
- path.dirname('path') :返回'path'对应的目录名
- path.resolve([from ...],to) :将路径或者路径片段解析成为一个绝对路径.最后一个参数是相对路径
- __dirname :是一个全局变量，表示当前文件的绝对路径
```
// 传入路径从右至左解析，遇到第一个绝对路径
// 例：webpack.base.config 中一段代码

function resolve(dir){
    return path.join(__dirname, '..', dir)
}
// __dirname 指向当前文件的绝对路径， '..'则表示向上一级， 'dir' 传入文件名
// 例 webpack.base.config在项目文件 myproject/build/webpack.config.js
// 则 path.join(__dirname,'..') 返回的是myproject根目录
```

> C、http模块（需使用require('http')
- 为了支持各种可能的 HTTP 应用，Node.js 的 HTTP API 是非常底层的。 它只涉及流处理与消息解析。 它把一个消息解析成消息头和消息主体，但不解析具体的消息头或消息主体。
- 在node文档中，可以看到http下有多个子类及方法。（http.Agent类，http.ClientRequest类，http.sever类，http.SeverResponse类，http.IncomingMessage类...）
- http.createSever([requestListener]) 创建并返回一个http.Server实例
- url.parse() 解析一个 URL 字符串并返回一个 URL 对象。
- util.inspect() 方法返回 object 的字符串表示，主要用于调试。  ( 附加的 options 可用于改变格式化字符串的某些方面。)

```
// 创建服务，监听服务
const http = require('http') //创建http服务
const url = require('url') //用于解析请求 
const util = require('util') // 提供util.inspect()，用于将对象转字符串

http.createServer((req,res)=>{
// url.parse(),只能获取到相对路径，不能获取到完整的url，即不包含 协议，端口，hash值
  var re = util.inspect(url.parse(req.url))
  res.statusCode = 200;
  res.setHeader('Content-Type','text/plain;charset=utf-8')
  res.end(re) // 注：res.end只接受字符串参数
}).listen('3001','127.0.0.1',()=>{
  console.log('服务已启动，正在监听服务')
})
```
### 3、node加载静态资源（原生）
> 原理：通过fs模块读取文件并发送
```
const http = require('http')
const url = require('url')
const util = require('util')
const fs = require('fs')

http.createServer((req,res)=>{
  var reqName = url.parse(req.url).pathname;
  console.log("file:"+ reqName.substring(1))
  fs.readFile(reqName.substring(1),(err,data)=>{
    if(err){
      res.writeHead(404,{
        'Context-Type':'text/html'
      })
    }else{
      res.writeHead(200,{ 
        'Context-Type':'text/html'
      });
      res.write(data.toString());
    }
    res.end() //注：fs.readFile是异步读取，所以res.end()要写在回调里面
  })

}).listen('3001','127.0.0.1',()=>{
  console.log('服务已启动，正在监听服务')
})
```
### 4、node作为代理请求第三方接口
> 调用 微信SDK，调用百度云API，或者调用java后台其他服务接口。nodejs作为中间层，起到请求第三方并处理返回数据，最终返回给客户端。
> github上的free api : https://github.com/EChu-web/public-apis
```
var https = require('https')

const req = https.request('https://dog.ceo/api/breeds/list/all',function(res){
  let rawData = ''
  res.on('data',(chunk)=>{
    rawData+=chunk
  })
  res.on('end',()=>{
    try {
      const parseData = JSON.parse(rawData);
      console.log(parseData)
    } catch(e){
      console.error(e.message)
    }
  }).on('error',(e)=>{
    console.log(`错误：${e.message}`)
  })
})
req.end() //使用 http.request() 必须总是调用 req.end() 来表明请求的结束，即使没有数据被写入请求主体。
```
## 三、Express应用框架
- 安装 express generator
- 通过生成器自动创建项目
- 配置分析
### 3.1 express generator 快速生成应用服务
> express: 快速创建web应用的node框架
```
npm install express -g //安装express 

cnpm i -g express-generator  //安装生成器

express --version //查看版本（注：不要用-v，在这里 -v是 view）

express /tmp/foo   //在tmp/foo下创建应用服务
npm install //安装依赖
npm start //启动服务
```
### 3.2  app.js文件解析
```
var express = require('express');
var path = require('path');
var favicon = require('serve-favicon');
var logger = require('morgan'); //日志输出
var cookieParser = require('cookie-parser'); //cookie解析
var bodyParser = require('body-parser'); //中间件，处理返回数据

var index = require('./routes/index');
var users = require('./routes/users');

var app = express();

// view engine setup (模板引擎设置)
app.set('views', path.join(__dirname, 'views')); //设置模板引擎路径
app.set('view engine', 'jade'); //模板引擎jade

// 在/public下放置favicon后，可以取消第一行注释
// uncomment after placing your favicon in /public
app.use(favicon(path.join(__dirname, 'public', 'favicon.ico')));
app.use(logger('dev'));
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: false }));
app.use(cookieParser());
app.use(express.static(path.join(__dirname, 'public')));

app.use('/', index); //路由
app.use('/users', users);

// catch 404 and forward to error handler 404错误处理
app.use(function(req, res, next) {
  var err = new Error('Not Found');
  err.status = 404;
  next(err);
});

// error handler
app.use(function(err, req, res, next) {
  // set locals, only providing error in development
  res.locals.message = err.message;
  res.locals.error = req.app.get('env') === 'development' ? err : {};

  // render the error page
  res.status(err.status || 500);
  res.render('error');
});

module.exports = app; //导出
``` 
> 注：此处默认是使用的jade模板，如果不习惯jade语法，也可以使用html模板，即ejs模板引擎
```
cnpm i ejs --save
//app.js
// view engine setup (模板引擎设置)
app.set('views', path.join(__dirname, 'views')); //设置模板引擎路径
app.engine('.html',ejs.__express); //查找ejs引擎
app.set('view engine', 'html'); //设置模板引擎
```