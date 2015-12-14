## Promise的含义

> Promise，简单说就是一个容器，里面保存着某个未来才会结束的事件（通常是一个异步操作）的结果。语法上，Promise是一个对象，从它可以获取异步操作的消息。
> Promise提供统一的API，各种异步操作都可以用同样的方法进行处理。

> 特点：
- 对象的状态不受外界影响。Promise对象有三种状态：Pending（进行中）、Resolved（已完成）、Rejected（已失败）。 只有异步操作的结果，可以决定当前是哪种状态，任何其他操作都无法改变这个状态。
- 一旦状态改变，就不会再变，任何时候都可以得到这个结果。Promise对象状态改变只有两种可能：A、从Pending到Resolved，B、从Pending到Rejected。

> 缺点：
- 无法取消Promise，一旦新建它就会立即执行，无法中途取消。
- 如果不设置回调函数，Promise内部掘的错误不会反应到外部。
- 当处于Pending状态时，无法得知目前进展到哪一个阶段（刚开始还是即将完成）

> 基本使用：
```
var promise = new Promise(function(resolve,reject){
    //... some code
    if(/*异步操作成功*/){
        resolve(value)
    }else{
        reject(error)
    }
})
promise.then(function(value){
    // success
},function(error){
    // failure
})

```
> 如上：Promise接收一个function参数，fun的两个参数resolve，reject是由JS引擎提供的，不用自己部署。
- resolve函数：将Promise对象的状态从“未完成”变成“成功”（Pending ——> Resolve），可接收参数（可以是值的参数，也可以是一个promise对象，用于链式操作）
- reject函数：将Promise对象的状态从“未完成”变成“失败” （Pending ——> Reject），可接收参数（一般为Error对象的实例）
- promise实例生成后，可以用then方法分别指定Resolved和Reject状态的回调函数
- then(resolveCB,rejectCB) ,CB:回调函数，其中rejectCB不一定要提供
- Promise在新建后就会立即执行

> 例：异步加载图片
```
function loadImageAsync(url){
    return new Promise(function(resolve,reject){
        var image = new Image();
        image.onload=function(){
            resolve(image)
        }
        image.onerror=function(){
            reject(new Error('Could not load image at '+url))
        }
        image.src = url;
    });
}
```
> 例：链式操作
```
var p1 = new Promise(function(resolve,reject){//...})
var p2 = new Promise(function(resolve,reject){
    // ....
    resolve(p1)
})
# 上面代码中，p1,p2都是Promise实例，但p2的resolve方法将p1作为参数，即一个异步操作的结果是返回另一个异步操作。这时p1的状态就会传递给p2，即p1的状态是pending时p2的回调会等待p1的状态改变，只有p1是resolve或rejected时，p2的回调函数会立刻执行
```
### Promise.prototype.then()
> Promise实例具有的then方法，是定义在原型对象Promise.prototype上的。它的作用是为promise实例添加状态改变时的回调函数。
> then方法返回的是一个新的Promise实例（注意，不是原来那个Promise实例）。因此可以采用链式写法，即then方法后再调用另一个then方法。
```
getJSON('/posts.json').then(function(json){
    return json.post;
}).then(function(post){
    // ...
})
```
> 如上，第一个函数完成后，将返回结果作为参数，传入第二个回调参数。
### Promise>prototype.catch()
> 用于指定发生错误时的回调函数，Promise.prototype.catch方法是.then(null,rejection)的别名。
- 当then方法指定的回调函数，如果在运行中抛出错误，也会在catch中捕获。
- Promise对象的错误具有“冒泡”性质，会一直向后传递，直到捕获为止。
- 如果不指定错误处理的回调函数，则当Promise对象抛出的错误不会传递到外层代码，即不会有任何反应。
- catch方法返回的还是一个Promise对象，因此后面还可以接着调用then方法。
- 如果没有报错，则会跳过catch方法
```
getJSON("/post/1.json").then(function(post){
    return getJSON(post.commentURL)
}).then(function(comments){
    // some code
}).catch(function(error){
    // 处理前面三个Promise产生的错误
})
# catch后的链式调用
var someAsync = function(){
    return new Promise(function(res,rej){
        resolve(x + 2) // 报错，X没有声明
    })
}
someAysnc()
.catch(function(error){
    console.log('on no',error)
})
.then(function(){
    console.log('carry on');
})
// oh no [ReferenceError:x is not defined]
// carry on
```

### Promise.all()
> Promise.all方法用于将多个Promise实例，包装成一个新的Promise实例。
```
var p = Promise.all([p1,p2,p3]);

```
> 如上代码：
- 只有p1,p2,p3的状态都变成fulfilled，p的状态才会变成fulfilled，此时p1，p2,p3的返回值组成一个数组，传递给p的回调函数。
- 只要p1,p2,p3中有一个rejected，则p状态变为rejected，此时第一个被rejecte的实例的返回值会传递给p的回调函数。
### Promise.race()
> 同样是将多个Promise实例，包装成一个新的Promise实例。不同在于，当多个promise实例中有一个状态率先改变，则新Promise实例的状态就跟着改变
### Promise.resolve()
> 将现在对象转为Promise对象，实例状态为resolve
```
var jsPromise = Promise.resolve($.ajax('/whatever.json'));
```
> 参数类型
1. 参数是一个Promise实例时：
- 直接返回这个实例
2. 参数是一个thenable对象时（即对象有then方法）
- 将这个对象转为Promise对象，并立即执行then方法
3. 参数不具有then方法，或根本不是对象时（一个原始值json之类的）
- 返回一个新的Promise对象，状态为Resolved
4. 不带任何参数
- Promise.resolve方法允许调用时不带参数，直接返回一个Resolved状态的promise对象。

### Promise.reject()
> 返回一个新的Promise实例，该实例的状态为rejected。参数用法与resolve方法一致

### done()
> Promise对象的回调链，不管以then方法或catch方法结尾，要是最后一个方法抛出错误，都有可能无法捕捉到（因为Promise内部的错误不会冒泡到全局）。因此可以实现一个done方法，其总处理回调链尾端，保证抛出任何可能出现的错误。
```
Promise.prototype.done = function(onFulfilled,onRejected){
    this.then(onFulfilled,onRejected)
    .catch(function(reson){
        // 抛出一个全局错误
        setTimeout(()=>{throw reason},0)
    })
}
```
### finally()
> 接受一个普通的回调函数作为参数，不管怎样都必须执行。用于指定不管Promise对象最后状态如何，都会执行的操作
```
Promise.prototype.finally=function(callback){
    let P = this.constructor;
    return this.then(
        value => P.resolve(callback()).then(()=>value),
        reason => P.resolve(callback()).then(()=>{throw reason})
    )
}
```