## Class基本语法
> ES6引入了Class（类）的概念，作为对象的模板。通过class关键字，可以定义类。基本上，ES6的class可以看作只是一个语法糖，它的绝大部分功能，ES5都可以做到，新的Class写法只是让对象原型的写法更加清晰、更像面向对象编程的语法而已）
```
# ES5写法
function Point(x,y){
    this.x = x;
    this.y = y;
}
Point.prototype.toString = function(){
    return '('+this.x+','+this.y+')'
}
var p = new Point(1,2)
# ES6写法
class Point{
    constructor(x,y){
        this.x=x;
        this.y=y;
    }
    toString(){
        return '('+this.x+','+this.y+')'
    }
}
var p6= new Point(1,2)
```
> 如上，ES6的写法，Point类中，constructor代表原构造函数部分，而其他方法，则默认挂在实例的原型上。
```
class Point{
    //...
}
typeof Point //'function'
Point === Point.prototype.constructor  //true
```
- 如上：类的数据类型就是函数，类本身就指向构造函数。构造函数的prototype属性，在ES6的类上面继续存在。事实上，类的所有方法都定义在prototype属性上面的。
```
class Point(){
    constructor(){
        // ...
    }
    toString(){
        // ...
    }
    toValue(){
        // ...
    }
}
# 等同于
Point.prototype={
    toString(){},
    toValue(){}
}

# constructor一致。在类实例上调用方法，实际就是调用原型上的方法
class B {}
let b = new B();
b.constructor === B.prototype.constructor // true

# 类的方法新增
class Point{
    constructor(){//....}
}
Object.assign(Point.prototype,{
    toString(){},
    toValue(){}
})

# prototype对象的constructor属性，直接指向“类”本身，这与ES5的行为是一致的。
# 类的内部定义的方法是不可枚举
class Point{
    constructor(x,y){
        // ...
    }
    toString(){// ...}
}
Object.keys(Point.prototype)  // []
Object.getOwnPropertyNames(Point.prototype)  // ["constructor","toString"]

```
### constructor方法
> constructor方法是类默认方法，通过new命令生成对象实例时，自动调用该方法。一个类必须有constructor方法，如果没有显式定义，一个空的constructor方法会被默认添加。
- constructor方法默认返回实例对象（即this)，完全可以指定返回另一个对象
- 类的构造函数，必须使用new调用，否则会报错
```
class Point{
    constructor(){
        return Object.create(null)
    }
}
var foo = new Point()
foo instanceof Point //false,因为返回了一个新对象，而不再是默认的this

var bar  = Point()  //报错，必须用new调用
```

### 注意事项
#### 1. 不存在变量提升
```
new Foo()  //ReferenceError
class Foo{//...}
```
#### 2. class表达式
> Class也可以使用表达式的形式定义
```
const MyClass = class Me{  // 这里的类名是MyClass，而Me只是在类内部定义的，指代当前类
    getClassName(){
        return Me.name;
    }
}
let inst = new MyClass();
inst.getClassName()  //Me（类自带的name属性，Me指当前类）
Me.name //ReferenceError:Me is not defined(因为Me只是类内部定义的，外部拿不到)

# 立即执行Class
let person = new class {
    constructor(name){
        this.name = name;
    }
    sayName(){
        console.log(this.name)
    }
}('张三')
person.sayName() //张三
```

#### 3. 私有方法
> 私有方法是常见需求，但ES6不提供，只能通过变通方法模拟实现。
```
# 方法一（在class外部定义）
class Point{
    foo(baz){
        bar.call(this,baz)
    }
}
function bar(baz){
    return this.snaf = baz;
}

# 方法二（利用Symblo值的唯一性，外界无法调用）
const bar = Symbol('bar')
const snaf = Symbol('snaf')
export default subclassFactory({
    // 公有方法
    foo(baz){
        this[bar](baz)
    }
    // 私有方法
    [bar](baz){
        return this[snaf]=baz;
    }
})
```

#### 4. 严格模式
> 类和模块的内部，默认就是严格模式，所以不需要使用use strict指定运行模式。（ES6实际上把整个语言升级到了严格模式）
#### 5. name属性
> ES6的class只是ES5的构造函数的一层包装，所以函数的许多我都被class继承，包括name属性。
- name属性总是返回紧跟在class关键字后的类名
```
class Point{}
Point.name //Point
```

### Class的继承
- class之间可以通过extends关键字实现继承，这比ES5通过修改原型链的方式实现继承要清晰和方便。
```
class ColorPoint extends Point{
    constructor(x,y,color){
        super(x,y)  //调用父类的constructor(x,y)
        this.color = color
    }
    toStrin(){
        return this.color + ' ' +super.toString(); //调用父类的toString()
    }
}
```
> 如上：
1. super关键字：表示父类的构造函数，用来新建父类的this对象
2. 子类必须在constructor 方法中调用super方法，否则新建实例时会报错。（因为子类没有自己的this对象，而是继承父类的this对象，然后对其进行加工。如果不调用super方法，子类就得不到this对象）
3. this的使用： 在子类的构造函数中，只有调用了super之后，才能使用this关键字，否则会报错。

#### 类的prototype属性和__proto__属性
> 大多数浏览器的ES5实现之中，每一个对象都有__proto__属性，指向对应的构造函数的prototype属性。Class作为构造函数的语法糖，同时有prototype属性和__proto__属性，因此同时存在两条继承链。(注意，两条下划线__)
1. 子类的__proto__属性，表示构造函数的继承，总是指向父类。
2. 子类的prototype属性的__proto__属性，表示方法的继承，总是指向父类的prototype属性
```
   class A{}
   class B extends A {}
   console.log(B.__proto__ === A)  // true
   console.log(B.prototype.__proto__ == A.prototype)  // true

```
#### Extends 的继承目标
- extends关键字后面可以跟多种类型的值
```
class B extends A {}
```
- 上面代码中A，只要是一个有prototype属性的函数，就能被B继承。（由于函数都有prototype属性，因此A可以是任意函数）
- Object.getPrototypeOf() 可以从子类上获取父类。（因此可以用些方法判断一个类是否继承了另一个类）
```
Object.getPrototypeOf(ColorPoint) === Point // true
```

#### super关键字
> super 两种用法，含义不同
1. 作为函数调用时（即super(...args)），super代表父类的构造函数。
2. 作为对象调用时（即super.prop或super.method()），super代表父类。（此时，super既可以引用父类的实例的属性和方法，也可以引用父类的静态方法）
#### 实例的__proto__属性
> 子类的原型的原型指向父类的原型。通过子类的__proto__.__proto__属性可以修改父类实例的行为
```
var p1 = new Point(2,3)
var p2 = new ColorPoint(2,3,'red')
p1.__proto__ === p2.__proto__.__proto__  // true
p2.__proto__ === p1.__proto__ // false
# 子类通过原型修改父类
p2.__proto__.__proto__.printName = function(){
    console.log('Ha')
}
p1.printName() //'Ha'
```
#### 原生构造函数的继承
> 原生构造函数：语言内置的构造函数，通常用来生成数据结构。ECMAScript原生构造函数有如下：
- Boolean()
- Number()
- String()
- Array()
- Date()
- Function()
- RegExp()
- Error()
- Object()
> 在ES6之前，原生构造函数是无法继承的。因为子类无法获得原生构造函数的内部属性。ES6允许继承原生构造函数定义子类，因为ES6先新建父类的实例对象this，然后再用子类的构造函数修饰this，使得父类的所有行为都可以继承。
```
class MyArray extends Array{
    constructor(...args){
        super(...args)
    }
}
var arr = new MyArray();
arr[0] = 12;
arr.length // 1
arr.length = 0;
arr[0]  //undefined
```
> 可以在原生数据结构的基础上，定义自己的数据结构。

#### Class的取值函数（getter）和存值函数（setter)
> 与ES5一样，在Class内部可以使用get和set关键字，对某个属性设置存值函数和取值函数，拦截该属性的存取行为
```
class MyClass{
    constructor(){
        // ...
    }
    get prop(){
        return 'getter'
    }
    set prop(value){
        console.log('setter' + value)
    }
}
let inst = new MyClass()
inst.prop = 123
// setter 123
inst.prop  //'getter'
```
#### Class的Generator函数
> 在某个方法之前加上星号（*），就表示该方法是一个Generator函数
```
class Foo(){
    constructor(...args){
        this.args = args
    }
    * [Symbol.iterator](){
        for(let arg of this.args){
            yield arg;
        }
    }
}
for(let x of new Foo('He','Wo')){
    console.log(x)
}
```
> 上面代码中，Foo类的Symbol.iterator方法前有一个星号，表示该方法是一个Genrator函数。Symbol.iterator方法返回一个Foo类的默认遍历器，for...of循环会自动调用这个遍历器。

#### Class的静态方法
> 在一个方法前，加上static关键字，表示该方法不会被实例继承，而是直接通过类来调用，这就称为“静态方法”
```
class Foo{
    static classMethod(){
        return 'hello';
    }
}
Foo.classMethod() //'hello'
var foo = new Foo()
foo.classMethod() //TypeError:foo.classMethod is not a function
```
- 如上，Foo类的classMethod方法前有static表示此为静态方法，不会被实例继承，只能通过Foo类调用。

> 父类的静态方法可以被子类继承
```
class Foo{
    static test(){
        return 'hello'
    }
}
class Bar extends Foo{}
Bar.test() //hello
```
> 静态方法可以在super对象上调用
```
class Foo{
    static test(){
        return 'hello'
    }
}
class Bar extends Foo{
    static test(){
       return super.test() + 'world'
    }
}
Bar.test() //hello world
```
#### Class的静态属性和实例属性
>  静态属性指的是Class本身的属性，而不是实例对象（this）的属性
```
# 有效写法
class Foo{}
Foo.prop = 1 //Foo.prop为静态属性
console.log(Foo.prop) //1
# 无效写法
class Foo{
    prop:2
    static prop:2
}
Foo.prop //undefined
```
- 上面无效写法是因为ES6明确规定，Class内部只有静态方法，没有静态属性
> ES7中有一个静态属性的提案，目前Babel转码器支持
```
# 实例属性 —— 如下，myprop是实例的属性，可以在实例上读取这个属性
class MyClass{
    myProp = 42;
    constructor(){
        console.log(this.myProp) //42
    }
}
# 实例属性在ES6中写法
class Myclass{
    constructor(){
        this.myProp = 42
    }
}

# 类的静态属性
class MyClass{
    static myStaticProp = 42
    constructor(){
        console.log(MyClass.myProp) //42
    }
}
```
#### new.target属性
> new是从构造函数生成实例的命令。ES6为new命令引入了一个new.target属性，返回new命令作用于的那个构造函数。如果构造函数不是通过new命令调用的，new.target会返回undefined。
```
function Person(name){
    if(new.target!==undefined){
        this.name = name;
    }else{
        throw new Error('必须使用new 生成实例')
    }
}
var person = new Person('张三') //正确
var notAperson = Person.call(person,'张三') //报错
```
### Mixin模式的实现
> Mixin模式指的是，将多个类的接口“混入”（mix in)另一个类。实现如下：
```
function mix(...mixinx){
    class Mix{}
    for(let mixin of mixins){
        copyProperties(Mix,minxins)
        copyProperties(Mix.prototype,mixin.prototype)
    }
}
function copyProperties(target,source){
    for(let key of Reflext.ownKeys(sourct)){
        if(key !=='constructor'
        && key != 'prototype'
        && key !== 'name'){
            let desc = Object.getOwnPropertyDescriptor(source,key)
            Object.defineProperty(target,key,desc)
        }
    }
}

class DistributedEdit extends mix(Loggable,Serializable){}
```
- 上面代码的mix函数，可以将多个对象合成为一个类。使用的时候，只要继承这个类即可