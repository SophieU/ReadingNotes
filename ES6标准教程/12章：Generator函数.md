## Generator简介
> 语法上理解：Generator函数是一个状态机，封闭了多个内部状态。（提供了一种可以暂停执行的函数。通过yield语句来标记暂停）

> 执行Generator函数会返回一个遍历器对象，返回的遍历器对象可以依次遍历Generator函数内部的每一个状态

> 形式上，Generator函数是一个普通函数，但有两个特征。一：function关键字与函数名之间有一个星号；二：函数体内部使用yield语句，定义不同的内部状态（yield语句在英语里的意思是“产出”）
```
function* helloGenerator(){
    yield 'hello';
    yield 'world';
    return 'ending';
}
var hw = helloGenerator();
# 此函数有三个状态： hello,world,return语句（结束执行）

hw.next()
// {value:'hello;,done:false}
hw.next()
// {value:'world',done:false}
hw.next()
// {value:'ending',done:true}
hw.next()
//{value:undefined,done:true}
```
- Generator函数调用方法与普通函数一样。不同的是，Generator函数调用后，该函数不执行，返回的也不是函数运行结果，而是一个指向内部状态的指针对象，即遍历器对象（Iterator Object）
- 下一步：调用遍历器对象的next方法，使指针移向下一个状态。即每次调用next方法，内部指针就从函数头部或上一次停下来的地方开始执行，直到遇到下一个yield语句（或return 语句）为止。
- 换言之：Generator函数是分段执行的，yield语句是暂停执行的标记，而next方法可以恢复执行

### yield语句
> 由于Generator函数返回的遍历器对象，只有调用next方法才会遍历下一个内部状态，所以其实提供了一种可以暂停执行的函数。yield语句就是暂停标志。

> 遍历器对象的next方法运行逻辑
1. 遇到yield语句，暂停执行后面的操作，并将紧跟在yield后面的那个表面式的值，作为返回的对象的value属性值
2. 下一次调用next方法，再继续往下执行，直到遇到下一个yield语句
3. 如果没再遇到yield语句就运行到函数结束，直到return语句为止，并将return后的表达式值作为返回的对象的value属性值
4. 如果没有return语句，则返回的对象的value属性值为undefined

> yield后面的表达式，只有当调用next方法、内部指针指向该语句时才会执行，因此等于为JS提供了手动的“惰性求值”的语法功能

> Generator函数可以不用yield语句，这时它变成了一个单纯的暂缓执行函数。只有调用next方法时，才会执行
```
function* f(){
    console.log('执行了')
}
var generator = f();

setTimeout(function(){
    generator.next()
},2000)
```

> for...of循环可以自动遍历调用Generator函数时生成的Iterator对象，且此时不再需要调用next方法。
```
function* foo(){
    yield 1;
    yield 2;
    yield 3;
    yield 4;
    return 6;
}
for(let v of foo()){
    console.log(v)
}
// 1 2 3 4 6
```

#### yield* 语句
> 用于在一个Generator函数里执行另一个Generator函数。（默认情况下在一个Generator函数里执行另一个Generator函数是没有什么作用的）
```
function* foo(){
    yield 'a';
    yield 'b';
}
function* bar(){
    yield 'x';
    yield* foo();
    yield 'y';
}
# 等同于
function* bar(){
     yield 'x';
    for(let v of foo()){
        yield v;
    }
    yield 'y';
}
```
### 作为对象属性的Generator函数
```
let obj ={
    * myGeneratorMethod(){
        ...
    }
}
```
### Generator函数的this
> Generator函数总是返回一个遍历器，ES6规定这个遍历器是Generator函数的实例，也继承了prototype对象上的方法.
> 但是Generator函数不是构造函数，不能用new运行，其返回的总是遍历器对象，而不是this对象
```
functin* g(){}
g.prototype.hello = function(){return 'hi';}
let obj = g();
obj instanceof g  // true
obj.hello()  // 'hi'

# generator函数内的this，遍历器对象是拿不到的
function* g(){this.a=11;}
let obj = g();
obj.a //undefined  

# 解决让实例获取函数内部this对象的方案
function* F(){
    this.a = 1;
    yield this.b= 2;
    yield this.c = 3;
}
var f = F.call(F.prototype)
f.next() //Object{value:2,done:false}
f.next() //Object{value:3,done:false}
f.next() //Object{value:undefined,done:true}
f.a //1
f.b //2
f.c //3

```