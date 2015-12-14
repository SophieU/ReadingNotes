## 异步操作
> 在ES6，异步编程的方法有：回调函数、事件监听、发布/订阅、Promise对象。在ES7中，Async函数更是提出了异步编程的终极解决方案。
### 基本概念
#### 异步：
- 简单说,异步即一个任务分成两段，先执行第一段，然后转而执行其他任务，等做好了准备，再回头执行第二段。这种不连续的执行就叫做异步。（相应的，连接执行就叫做同步）

#### 回调函数
- JavaScript语言对异步编程的实现，就是回调函数。所谓的回调函数，就是把任务的第二段单独写在一个函数里面，等到重新执行这个任务的时候，就直接调用这个函数。
```
fs.readFile('/etc/passwd',function(err,data){
    if(err) throw err;
    console.log(data);
})
```
> 为什么NodeJS约定，回调函数的第一个参数，必须是错误对象err(如果没有错误，该参数就是null)？
- 因为执行分成两段，在这两段之间抛出的错误，程序无法捕捉，只能当作参数，传入第二段。

#### Promise
> 当多个回调函数嵌套时，代码会乱成一团，无法管理。这种情况称为“回调函数噩梦”（callback hell)。Promise就是为了解决这个问题而提出的。它不是新的语法功能，而是一种新的写法，允许将回调函数的嵌套，改成链式调用。
- 例：链式读取多个文件
```
readFile(fileA)
.then(function(data){
    console.log(data.toString());
})
.then(function(){
    return readFile(fileB);
})
.then(function(data){
    console.log(data.toString());
})
.catch(function(err){
    console.log(err);
})
# 上面使用了fs-readfile-promise模块，用于返回一个Promise版本的readFile函数
```

#### Generator函数
- 协程（coroutine)，多个线程互相协作，完成异步任务。
> 协程的执行过程如下：
1. 协程A开始执行。
2. 协程A执行到一半，进入暂停，执行权转移到协程B
3. （一段时间后）协程B交还执行权
4. 协程A恢复执行

```
function* asyncJob(){
    // ...其他代码
    var f = yield readFile(fileA)
    // ...其他代码
}
```
> 如上代码的函数asyncJob是一个协程，当执行到yield命令时，执行权交给其他协程。等到执行权返回，再从暂停的地方继续往后执行。

> Generator函数的概念
- Generator函数是协程在ES6的实现，最大特点就是可以交出函数的执行权（即暂停执行）。
- 整个Generator函数就是一个封装的异步任务，或者说是异步任务的容器。异步操作需要暂停的地方，都用yield语句注明。
```
function* gen(x){
    var y = yield x + 2;
    return y;
}
var g = gen(1)
g.next()  //{value:3,done:false}
g.next() //{value:undefined done true}
```
- generator函数返回的是一个内部状态指针，指向第一个遇到的yield语句
- Generator函数能封闭异步任务的根本原因是其可以暂停执行和恢复执行

>  它可以作为异步编程的完整解决方案：函数体内外的数据交换和错误处理机制（next方法返回的value属性是Generator函数向外输出的数据。next方法还可以接受参数，这是向Generator函数体内输入数据）
- 例：Generator函数封闭异步任务
```
var fetch = require('node-fetch')
function* gen(){
    var url = 'https://api.github.com/users/github'
    var result = yield fetch(url);
    console.log(result.bio)
}
# 执行
var g = gen();
var result = g.next();
result.value.then(function(data){
    return data.json();
}).then(function(data){
    g.next(data)
})
```

### Async函数
> async函数是Generator函数的语法糖（语法糖：自行封闭了一种代码的便捷式写法，编译器会帮忙做转换。）
