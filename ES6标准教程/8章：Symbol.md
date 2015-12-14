## Symbol
> Symbol是一种新的原始数据类型，表示独一无二的值。它是JS语言的第七种数据类型。（前六种：undefined,null,boolean,String,Number,Object）
> Symbol是由Symbol函数生成。（对象的属性名现在可以有两种类型，一种是字符串「可重复」，一种是新境的Symbol类型，凡是属性名是Symbol类型的，就是独一无二的，可以保证不与其他属性名冲突）
```
let s = Symbol() // 注意没有new
typeof s  // symbol
# 接收参数
ley s1 = Symbol('foo')
s1 //symbol(foo)
s1.toString()  //"symbol(foo)"
# 运算报错
let sym = symbol()
"your symbol is" + sym  //TypeError:can't convert symbol to string
# 直接转换
var sym = symbol()
Boolean(sym) //true
# symbol值作为属性名
var mySym = Symbol();
var a = {}
a[mySym]='hello'; //方式一
Object.defineProperty(a,mySym,{value:'hello'}) // 方式二
a={
    [mySym]:'hello' // 在对象内部symbol值必须放在方括号之中
}
```
- 注意：symbol是一种原始数据类型，所以创建它时，不用new（它不是对象）。
- 上面代码中，变量s是一个独一无二的值
- Symbol值不是对象，所以不能添加属性，基本上，它是一种类似于字符串的数据类型
- symbol函数接收一个字符串作为参数，表示对symbol实例的描述，主要是为了在控制台显示，或者转为字符串时，比较容易区分。
- symbol值不能与其他类型的值进行运算，会报错(但可以显示转换其值)