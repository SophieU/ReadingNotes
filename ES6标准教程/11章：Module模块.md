## Module
> ES6的Class只是面向对象编程的语法糖，升级了ES5的构造函数的原型链继承的写法，并没有解决模块化问题。Module功能就是为了解决这个问题而提出的。
> 浏览器使用ES6模块的语法如下：
```
<script type="module" src="foo.js"></script>
# 因为type属性为module，所以浏览器知道这是一个ES6模块
```
### 严格模式
> ES6的模块自动采用严格模式，不管你有没有在模块头部加“use strict"
> 严格模式主要有以下限制
- 变量必须声明后再使用
- 函数的参数不能有同名属性，否则报错
- 不能使用with语句
- 不能对只读属性赋值，否则报错
- 不能使用前缀0表示八进制数，否则报错
- 不能删除不可删除的属性，否则报错
- 不能删除变量 delete prop ，会报错，只能删除属性 delete global[prop]
- eval 不会在它的外层作用域引入变量
- eval 和arguments不能被重新赋值
- arguments不会自动反映函数参数的变化
- 不能使用arguments.callee
- 不能使用arguments.caller
- 禁止this指向全局对象
- 不能使用fn.caller和fn.arguments获取函数调用的堆栈
- 增加了保留字（比如 protected、statci和interface）

### export 命令
> 模块功能主要由两个命令构成：export 和 imoprt 。export命令用于规定模块的对外接口，import命令用于输入其他模块提供的功能。
- 一个模块就是一个独立的文件，该文件内部的所有变量，外部无法获取，如果你希望外部能够读取模块内部的某个变量，就必须使用export关键字输出该变量。
```
# 基本用法
export var name='michael'
# 常规用法
var name = 'michael'
export {name}
# 导出函数
export function multiply(x,y){
    return x * y
}
# 使用as关键字重命名
function v1(){...}
function v2(){...}
export {
    v1 as streamV1,
    v2 as streamV2
}
# 注意：export命令规定的是对外的接口，必须与模块内部的变量建立一一对应关系
# 报错
export 1
var m = 1
export m;
# 纠正
export var m = 1;  // 对应 import * as m from '../util.js'
#或
var m= 1;
export {m}  // 对应 import * as m from '../util.js'
```
### import 命令
> import 加载模块.注意：import命令具有提升效果，会提升到整个模块的头部，首先执行。
- import命令接受一个对象，里面指定要从其他模块导入的变量名，大括号里面的变量名，必须与被导入模块（profile.js)对外接口的名称相同。
- ipmort重命名，使用as关键字，将输入的变量重命名
```
# 导入多个
import {firstName,lastName,year} from './profile'

# 重命名
import {lastName as surname} from './profile'

# ipmort的提升效果 (如下：不会报错，因为变量提升)
foo() 
import {foo} from 'my_module'

# 整体加载 (导入circle的模块全部到circle对象中)
import * as circle from './circle'
```
### export default 命令
> 在使用import命令时，用户需要知道所要加载的模块的变量名或函数名，才能指定加载。因此可以使用export default为模块指定默认输出。
```
# export.js
export default function(){
    console.log('hello')
}
# import.js. 这里的import后不用{}
import customName from './export'
customName(); 

# 对比默认输出export deafult和正常输出export
// 输出
export deafult function crc32(){...}
//输入
import crc32 from 'crc32'

// 输出 
export function crc32(){...}
// 输入
import {crc32} from 'crc32'
```
> 注意
1. export deafult 只能默认输出一次，不能重复用。
2. export deafult后面不能跟变量声明语句
3. 有引入默认输出模块之后，还可以引入其他模块
4. export default可输出class类
```
# 注意2 
export deafult var a =1 //报错
# 注意3 (同时引入)
import customName,{otherModule} from './export'

```
### 模块的继承
```
# 全部继承并输出  circlePlus 继承circle
// circlePlus.js
export * from 'circle' //引入circle并全部导出（所有属性和方法）
export var e = 2.18145642 //导出变量
export default function(x){  //导出默认方法
    return Math.exp(x)
}
# 导出部分
export {area as circleArea} from 'circle'   // 只输出circle模块的area方法，并改名为circleArea

# 加载以上模块
import * as math from 'circlePlus'
import exp from 'circlePlus'

```

### ES6模块加载的实质
> ES6模块加载的机制，与CommonJS模块完全不同。CommonJS模块输出的是一个值的拷贝，而ES6模块输出的是值的引用
- 因为CommonJS模块输出的是一个值的拷贝，所以当输出模块的内部发生改变时，不会影响到这个值
```
# 区别 CommonJS 
var counter = 3;
function counterInc(){
    counter++
}
module.exports = {
    counter:counter,
    counterIcn:couterInc
}
// 引入
var mod = require('./lib')
console.log(mod.counter) //3
mod.counterInc();
console.log(mod.couter)  // 3
# 如上：无论内部couterInc如何改变了couter，已经引入的不会变

# ES6模块
export var couter = 3;
export function couterInc(){
    couter++;
}
//引入
import {couter,couterInc} from './lib'
console.log(couter) //3
couterInc()
console.log(conter)  // 4
# 如上：已经调用couterInc后，原couter值也发生了改变。说明ES6是值的引用
```
#### CommonJS模块的加载原理
> CommonJS的模块就是一个脚本文件。require命令第一次加载该脚本，就会执行整个脚本，然后在内存生成一个对象。如下，Node内部加载模块后生成的一个对象.
- 实际上，Commonjs在加载模块时，是将导入的模块进行了缓存，所以是值的拷贝。
```
{
    id:'...',
    exports:{...},
    loaded:true,
    ...
}
```


## 类的修饰
> 修饰器（Decorator）是一个函数，用来修改类的行为。这是ES7的一个提案，目前Babel转码器已经支持

```
function testable(target){
    target.isTestable = true;
}
@testable
class MyTestableClass{}
console.log(MyTestableClass.isTestable) //true
```
- 如上，@testable就是一个修饰器。它修改了MyTestableClass这个类的行为，为它加上了静态属性isTestable
```
@decorator
class A{}
// 等同于
class A{}
A = decorator(A) || A
```
- 修饰器本质就是编译时执行的函数

> 修饰器不能用于函数
- 只能用于类和类的方法，不能用于函数，因为函数存在提升