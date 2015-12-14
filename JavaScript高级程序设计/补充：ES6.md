# 一、ES6
> ES6又叫 Harmony，最2015年推出的新的JS规范
## 一、一般性变化
### 1.1 常量 const
- const声明的变量在初始赋值后就不能再重新赋值
- 不能修改
```
const test = 1;
var test =2 ; //报错
test = 3; //修改不成功，test依然为1
```
### 1.2 块级作用域
- 使用let 定义块级作用域（方法如下）
- 法一：在代码块内使用let定义变量并为变量赋值。其定义的变量只在let所在代码块内有效。
- 法二：创建let语句，在其中定义只能在后续代码块中使用的变量
```
//法一：
for(let i = 0;i<10;i++){}
console.log(i); //报错！i未定义
//法二：
var num = 5;
let(num=10,mult=2){ //使用let创建了自己的作用域，内部变量与外部无关
    console.log(num+mult) //12
}
console.log(num) //5
```
## 二、函数
### 2.1 剩余参数与分布参数
> ES6中不再有arguments对象，所以无法在函数中通过它来读取未声明的参数。
- 剩余参数rest arguments语法获取其他未声明参数。 语法： ... + 标识符 
- 分布参数spread arguments。通过分布参数，可以向函数传入一个数组，然后数组中的元素会映射到函数的每个参数上。
- rest 和 spread的区别在于：spread是在函数调用时使用，rest是在函数定义时使用
```
//rest参数 ...nums
function sum (num1,num2,...nums){ //num1,num2是必要参数，剩余参数数量不定
    var result = num1+num2;
    for(i=0,len=nums.length;i<len;i++){
        result+=nums[i]
    }
    return result;
}
var result = sum(1,2,3,4,5); //15
//spread参数 ...[]
var result2 = sum(...[1,2,3,4,5]) //15
```
### 2.2 默认参数
- 在定义函数时，直接给参数赋初始值即设置了默认参数
```
function sum(num1,num2=0){
    return num1+num2
}
var result = sum(5); //5
```
### 2.3 生成器
- 在需要一系列值，而每一个值又与前一个值存在某种关系的情况下，可以使用生成器。
```
    function* test(){
        for(let i =0;i<5;i++){
            yield i*2
        }
    }
    var ge = test();
    console.log(ge.next().value) //0
    console.log(ge.next().value) //2
    console.log(ge.next().value) //4
    console.log(ge.next().value) //6
```