## 一、let命令
> 用于声明变量，类似于var，但let命令所在的代码块内有效

> 特点：
- 只在代码块内有效
- 不存在变量提升（必须先声明后使用，否则会报错）
- 暂时性死区
- 不允许重复声明

### 1、代码块内有效
> 用let声明的变量只在当前块{}中有效,在块级作用域外是拿不到的
```
{
    let a = 10;
}
console.log(a)  // Reference error
```
### 2、不存在变量提升
> es5中 var声明的变量会有变量提升，即会赋初始值undefined，而在es6中，let声明的变量不存在变量提升。即：只有在let 声明变量后才能使用变量，否则会报错
```
console.log(a)  // undefined
console.log(b)  // reference error
var a = 10;
let b = 11;
```
### 3、暂时性死区
> ES6规定，在区块中只要存在let 和 const 命令，这个区块会对这些命令声明的变量，从一开始就形成封闭作用域，凡是在声明之前就使用这些变量都会报错。这在语法上，称为“暂时性死区”(temporal dead zone,简称TDZ)
```
var a = 10;
{ 
    a = 11; // referenc error
    let a;
}

# 例2
function bar(x=y,y=2){
    return [x,y]
}
bar(); //报错，因为x=y 这一步，y还未声明。建议y=2放在前面
```
### 4、不允许重复声明
> 在同一个区块内，不允许重复声明变量
```
function(arg){
    let arg =10; // SyntaxError： Indentifier has already been declared
}
function(arg){
    {
        let arg = 10; // 没问题
    }
}
```
## 二、块级作用域
> 在ES5中只有全局作用域和函数作用域，没有块级作用域，这会出现内层变量可能会覆盖外层变量的问题，以及在for循环中，用来控制循环的变量i，在循环结束后会泄露成为全局变量，造成全局污染

- 在ES6中规定，函数声明语句的行为类似于let,在块级作用域之外不可引用
- 在ES5中，块级作用域中声明的函数会提升到函数头部
- 考虑到环境导致的行为差异太大，应该避免在块级作用域内声明函数。如果确实需要，也应该写成函数表达式，而不是函数 声明语句

## 三、const命令
- 声明一个只读常量。一旦声明，常量的值不可改变
- 因为const声明的变量不得改变值 ，因此一旦声明变量，就必须立即初始化
- 作用域：只在声明的块级作用域内有效
- 同样存在暂时性死区，没有变量提升，只能在声明之后使用、不可重复使用

### 3.1  不可修改特性
> const声明的复合类型数据，只是保证变量名指向的地址不变，不保证该地址的数据不变
```
const arr = [];
arr.push(1) //可执行‘’
console.log(arr)  // [1]
arr=['newArr'] //报错

```
### 3.2  冻结对象
- Object.freeze(obj)  可用于冻结对象
```
// 冻结对象
const foo = {}
foo.prop1= 123;
foo.obj={
    num:11
}
console.log(foo.prop1)  //123
Object.freeze(foo)
foo.obj.num2=222;   // 可执行
foo.prop2 = 456;  //报错 object is not extensible
```
- 上述冻结了对象foo，使后面不能再向foo添加任何属性了。但是，当对象的属性也是对象时，就可以
```
// 如下函数用于深度冻结对象
var freeze = (obj)=>{
    Object.freeze(obj);
    Object.keys(obj).forEach((key,value)=>{
        if(obj[key]==='object'){
            freeze(obj[key])
        }
    })
}
```

> 在ES5中只有两种声明变量的方法：var 和 function 。在ES6中除了加入了let 和 const 命令，还加入了import ,class命令
### 3.3 全局对象的属性
> 全局对象是最顶层的对象，在浏览器中指window，在Node中指global。
- 在ES5中，全局对象的属性与全局变量是等价的。
- 在ES6中，用let,const声明的全局变量只在当前块级作用域内有效，不会持载到全局对象上。
```
var a = 10;
console.log(window.a) // 10;
let b = 11;
console.log(window.b)  // undefined;
```

