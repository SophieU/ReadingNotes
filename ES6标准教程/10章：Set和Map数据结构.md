## Set数据结构
> ES6提供了新的数据结构Set，它类似于数组，但成员的值都是唯一的，没有重复的值
```
# 通过add方法向Set结构加入成员，表明Set结构不会添加重复的值
    var s = new Set();
    [2,3,4,5,2,2].map(x=>s.add(x));  // add() 方法为set结构数据特有，表示向其末位加数据
    for(let i of s){
      console.log(i)
    }
    // 2 3 4 5  去掉了重复的数，每一位元素都是唯一的
# Set函数可接收参数用来初始化 _  数组去重
var set = new Set([1,2,3,4,4])
[...set]  // [1,2,3,4]

```
- 在set结构中，NaN和NaN是相等的，所以不能添加两个NaN
- 而两个对象总是不相等的，所以可以添加两个{}

### Set实例的属性和方法
#### 1、属性
- Set.prototype.constructor   :构造函数，默认就是Set函数
- Set.prototype.size   :返回set实例的成员总数
#### 2、方法
> 1)操作方法
- add(value)  :添加某个值，返回set结构本身
- delete(value)  :删除某个值，返回一个布尔值，表示是否删除成功
- has(value)   : 返回一个布尔值，表示该值是否为Set的成员
- clear()  :清除所有成员，没有返回值
> 2)、遍历方法
- keys()  返回键名的遍历器
- values()  返回键值
- entries()  返回键值对
- forEach()  使用回调函数遍历每个成员
```
  var s = new Set()
   s.add(1).add(2).add(2)
   s.size // 2
   s.has(1)  //true
   s.delete(1) //[2]
# 对比对象写法与set写法，判断对象是否包括一个键
// 对象写法
var properties = {
    'width':1,
    'height':1
}
if(properties[someName]){// do something}
// Set写法
var properties = new Set()
properties.add('width')
properties.add('height')
if(properties.has(someName)){// do something}

# 遍历方法
let set = new Set(['red','green','blue'])
for(let item of set.keys()){console.log(item)}  //'red','green','blue'
for(let item of set.values()){console.log(item)}  //'red','green','blue'
for(let item of set.entries()){console.log(item)}  //['red'，'red'],['green','green'],['blue','blue']
```
## Map结构的目的和基本用法
> 场景：在ES5中，JS的对象，本质上是键值对的集合（Hash结构），但是传统上只能用字符串当作键。这使其有限制。

> Map数据结构，类似于对象，也是键值对集合，但“键”的范围不限于字符串，各种类型的值都可以当作键。
```
var items=[
    ['name','jack'],
    ['job','Author']
]
items.forEach(([key,value])=>map.set(key,value))
```

### 属性和操作方法
1. size 返回Map结构的成员总数
2. set(key,value) 设置key所对应的键值，返回整个Map结构，因此可以采用链式写法
3. get(key)  读取key对应的键值
4. has(key)  返回布尔值，表示某个键是否在Map数据结构中
5. delete(key) 删除某个键，成功返回true，失败返回false
6. clear()   清除所有成员，没有返回值
> 遍历方法
1. keys()
2. values()
3. entries()
4. forEach()