## 一、关于Ecmascript
> 关于ecmascript,2013年3月，ES6的草案封闭，不再接受新功能了。而新的功能将被加入到ES7当中。
> 任何人都可以向TC39提案，从提案到正式标准需要经过五个阶段
- Stage 0  （展示阶段）
- Stage 1  （征求意见阶段）
- Stage 2  （草案阶段——进入这个阶段后，就差不多等于肯定会包换在ES7中）
- Stage 3 （侯选人阶段）
- Stage 4 （定案阶段）
## 二、关于ES6的工具
- babel  @5版支持浏览器中使用，有在线转换，命令行转换，与其他工具Mocha,ESLint结合使用
- traceur  谷歌公司开发的转码器，支持直接插入网页，在线转换，命令行转换，Node环境中使用
### 1、 es-checker  查看正在使用的Node环境对ES6的支持情况
```
 $   npm install -g es-checker
 $   es-checker
```

### 2、 Babel转码器

> 概要
- .babelrc 配置文件
- babel-cli 命令工具
- babel-core 核心方法
- babel-node 直接执行js转码的REPL环境
- babel-polyfill  转码新的JS API 模块
- babel-register  转码通过require加载的文件
- browser.js   在浏览器中实时转码（影响性能）

#### 1）babel配置文件  ————只有在写好配置文件的前提下，才能进行下一步转码
> babel关键配置文件 .babelrc  (先安装对应规则 babel-preset-*,再配置使用规则)
```
{
    //设置转码规则
    "presets":[
        "env", // 4选一
        "es2015",
        "react",
        "stage-2"
    ], 
    "plugins":[]
}
# ES2015转码规则
$ npm install --save-dev babel-preset-es2015
# react 转码规则
$ npm install --save-dev babel-preset-react
# ES7不同阶段语法提案的转码规则（共4个阶段），选装一个
$ npm install --save-dev babel-preset-stage-0
$ npm install --save-dev babel-preset-stage-1
$ npm install --save-dev babel-preset-stage-2
$ npm install --save-dev babel-preset-stage-3
```
#### 2） 命令行转码  babel-cli

> A、全局命令转码

```
# 全局安装babel
npm install -g babel-cli
# 编辑js文件
babel example.js
# 常用命令

# 指定输出文件
babel example.js -o compiled.js
# 整个目录转码
babel src -d lib
# 生成source map 文件
babel src -d lib -s
```
- 以上代码是在全局环境下，进行babel转码，因此全局环境中必须安装了babel，因而项目产生了环境信赖，且这样做无法支持不同项目使用不同版本的babel。
- 解决办法：将babel-cli安装在项目中

> B、项目中转码
```
# 安装bable-cli到项目中
npm i babel-cli --save-dev
# 添加命令到package.json
{
    "script":{
        "build":"babel src -d lib"
    }
}
# 运行命令
npm run build
```
#### 3)、babel-node命令
- babel-node命令是随着babel-cli一起安装的，通过在cmd中输入babel-node进入到babel的转码环境REPL，从而可以直接在命令行中执行ES6代码
```
$ babel-node   // 进入babel转码环境REPL
> (x=>x*2)(1)  // 一个立即执行函数
2              // 执行结果 2
```
- 此外babel-node后还可以跟js文件，代表直接执行该文件
```
babel-node es6.js
```
#### 4)、babel-register命令
> babel-register模块必定了require命令，为它加上了钩子函数，每当使用require加载.js,.jsx,.es,.es6的文件时，都会先使用Babel进行转码
```
# 安装
npm i --save-dev babel-register
# 使用时，放在最前，其后通过require加载的文件都会自动转码
require('babel-register')
require('./index.js')
```
#### 5)、babel-core
> 某些代码需要调用Babel的Api进行转码时，就需要这个模块
> 应用场景：在代码内手动调用babel的api操作时
```
#安装
npm i babel-core
# 使用
var es6code = 'let x=n=>n+1';
var es5code = require('babel-core').transform('es6code',{
    presets:['es2015']
}).code;
# 结果
通过babel-core将es6code转码为es5
```
#### 6）、babel-polyfill
> 默认情况babel只转换新的JS语法，而不会转换新的API，而像新的ES6API（如：Iterator，Generator，Set，Maps，Proxy等）就需要使用babel-polyfill来配置
```
# 安装
npm i --save babel-polyfill
# 使用（在js文件头部加入代码）
import 'babel-polyfill';
// 或者
require('babel-polyfill')
```
#### 7)、在浏览器环境下使用ES6
> babel从6.0版本开始不再直接提供浏览器版本，而是需要用构建工具构建出来。如果不想用构建工具，可以通过安装5.x版本的babel-core模块获取
```
# 安装
npm i babel-core@5
# 找到安装的browser.js
在node_modules/babel-core/下找到babel的浏览器版本browser.js 或 browser.min.js

# 使用
<script src='browser.js'></script>
<script type='text/babel'> // 设置类型为 text/babel
// 你的ES6代码
</script>
```
- 网页中实时将Es6转码为ES5会对性能有影响，因为最后是在生产环境下先转码脚本

### 3、Traceur转码器
- 由Google公司开发的转码器

#### 1)、命令行转换
- 加个标记
```
# 安装
npm i -g traceur
# 使用1 —— 直接运行脚本
traceur calc.js
# 使用2  —— es6转Es5保存
traceur --script calc.es6.js --out calc.es5.js  --experimental
// --script 指定输入文件，--out 指定输出文件，--experimental防止编译不成功
```



