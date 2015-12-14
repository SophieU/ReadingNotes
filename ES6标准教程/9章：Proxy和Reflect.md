## Proxy概述
> Proxy用于修改某些操作的默认行为，等同于在语言层面做出修改，即对编程语言进行编程，属于一种“元编程”
> Proxy可以理解为在目标对象之前架设一层“拦截",外界对该对象的访问都必须先通过这层拦截，因此提供了一种机制，可以对外界的访问进行过滤和必定。
> Proxy：原意为代理，用在这里表示由它来”代理“某些操作，可以译为"代理器"。
```
var obj = new Proxy({},{
      get:function(target,key,recevier){
        console.log(`getting ${key}!`)
        return Reflect.get(target,key,recevier)
      },
      set:function(target,key,value,recevier){
        console.log(`setting ${key}`)
        return Reflect.set(target,key,value,recevier)
      }
    })
    obj.count=1; // setting count
    ++obj.count;
     // getting count 
     // setting count
```
> 如上：proxy的用法基本就像上例中一样，通过 var proxy = new Proxy(target,handler)
- target:要设置拦截的target对象
- handler也是一个对象，表示用来定义拦截行为
```
# 拦截读取属性行为
var proxy = new Proxy({},{
    get:function(target,property){
        return 35;
    }
});
proxy.time //35
proxy.name // 35
```
- 要便利proxy起作用，必须针对Proxy实例进行操作，而不是针对目标对象
- 如果handler没有设置任何拦截效果，访问handler就等同于访问target
### Proxy支持的拦截操作 API
1. get(target,propKey,recevier)
> 拦截对象属性的读取，reveiver是一个对象，可选
2. set(target,propKey,value,receiver)
> 拦截对象属性的设置
3. has(target,propKey)
> 拦截propKey in proxy 的操作，以及对象的hasOwnProperty方法，返回一个布尔值
4. deleteProperty(target,propKey)
> 拦截delete proxy[target,propKey]的操作，返回一个布尔值
5. apply(target,object,args)
> 拦截Proxy实例作为函数调用的操作，比如proxy(...args)，proxy.call(object,...args),proxy.apply(..args)
6. construct(target,args)
> 拦截proxy实例作为构造函数调用的操作，如new proxy(...args)

## Reflect概述
> Reflect对象与Proxy对象一样，也是ES6为了操作对象而提供的新API。设计目的如下：
1. 将object对象的一些明显属于语言内部的方法（如Object.defineProperty)，放到Reflet对象上。
2. 修改某些Object方法的返回结果，让其变得更合理。（如Object.defineProperty(obj,name,desc)在无法定义属性时，会抛出一个错误，而Reflect.defineProperty(obj,name,desc)则会返回false）
3. 让Object操作都变成函数行为
4. Reflect对象的方法与Proxy对象的方法一一对应，只要是Proxy对象的方法，就能在Reflect对象上找到对应的方法。

### Reflect的方法
1. Reflect.get(target,name,receiver)
> 查找并返回target对象的name属性，如果没有该属性，则返回undefined。如果name属性部署了读取函数，则读取孟函数的this绑定reveiver
2. Reflect.set(target,name,value,receiver)
> 设置对象的name属性等于value，如果name属性设置了赋值函数，则赋值函数的this绑定reveiver
```
# Reflect.get(target,name,receiver)
var obj ={
    get foo(){return this.bar();}
    bar:function(){..}
}
// 下面语句会让this.bar()变成调用wrapper.bar()
Reflect.get(obj,"foo",wrapper)
```
## 二进制数组
> 二进制数组（ArrayBuffer对象，TypedArray视图和DataView视图）是JavaScript操作二进制数据的一个接口。

> 设计目的：
> 与WebGL项目有关。（WebGL：web图形库，是一种JavaScript Api，用于在任何兼容的Web浏览器中呈现交互式3D和2D图形，而无需使用插件，WebGL通过引入一个与OpenGL ES 2.0紧密相符合的API，可以在HTML5 <canvas> 元素中使用。） —— 所谓WebGl，就是指浏览器与显卡之间的通信接口

> WebGL 与 二进制数组的关系
> 浏览器与显卡之间的通信数据必须是二进制的

> 作用：   允许开发者以数组下标的形式，直接操作内存，大大增强了JavaScript处理二进制数据的能力，使开发者可能通过JS与操作系统的原生接口进行二进制通信。

### 二进制数组由三类对象组成
1. ArrayBuffer对象：代表内存之中的一段二进制数据，可以通过“视图”进行操作。 ——即代表原始二进制数据
2. TypedArray视图：用来读写简单类型的二进制数据
3. DataView视图： 可以自定义复合格式的视图，（用于读写复杂类型的二进制数据）

> 用到二进制数组操作二进制数据的常用API
- File API
- XMLHttpRequest
- Fetch API
- Canvas
- WebSockets

### ArrayBuffer对象
- 代表二进制数据的一段内存，不能直接读写，只能通过视图来读写，视图的作用是以指定格式解读二进制数据
```
var buf = new ArrayBuffer(32); // 生成一段32字节的内存区域，字节的值默认都是0
var dataView = new DataView(buf);  //创建视图并指定
dataView.getUint8(0) //0  读取第一个元素
```
