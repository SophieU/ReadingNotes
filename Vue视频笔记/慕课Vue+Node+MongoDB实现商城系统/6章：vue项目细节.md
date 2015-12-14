# 一、项目初建的细节
> 1、webpack.bask.conf.js中有一段关于映射的配置： 其中 ‘@’就代表了src目录
```
resolve: {
    extensions: ['.js', '.vue', '.json'],
    alias: {
      'vue$': 'vue/dist/vue.esm.js',
      '@': resolve('src'),
    }
  },
```
> 2、*.vue文件中都包含template,style,script三个标签字段。但如果style中没有写样式，最好将style删除。因为它会导致最终页面会生成很多 空style标签
> 3、W3C标签规定，html标签要使用小写，所以在引入的组件名称为大写时，转为html标签需要用 ‘-’连接
```
js:
    import NavHeader from '../components/Header'
    export default {
        name: 'hello',
        components: {
            NavHeader
        }
    }
html: 
    <nav-header></nav-header>
```
> 4、数据mock配置： 在dev-server.js文件中配置node后台相关的内容。本项目使用的是express应用框架来开发，所以可应用express的router模块
- 新建文件 /mock/goods.json 模拟数据
- 配置dev-server服务，针对/goods地址，返回对应数据
- 页面配合axios，完成数据请求
```
//goods.json
{
    "status":"0",
    "msg": "",
    "result":[
        {
            "productId":"100001",
            "productName":"iphoneX",
            "productPrice":"7899",
            "productImg":"1.jpg"
        }
    ]
}
// dev-server.js 后端
var router = express.Router();
var goodsData = require('../mock/goods.json');
router.get('/goods',function(req,res,next){
    res.json(goodsData); //直接返回json数据
})
app.use(router)
// 前端
axios.get('/goods').then((data)=>{
    if(data.data.status == 0){
        this.goodsLists = data.data.result
    }
})
// template。注意：img的src属性当使用v-bind:src时，“”内就是表达式，因而字符串需要加''并拼接
<ul class='goodsLists'>
    <li v-for="goods in goodsLists">
        <div class="pic">
            <a href="#"><img :src="'static/'+good.productImg" alt=""></a>
        </div>
        <div class="main">
            <div class="name">{{good.productName}}</div>
            <div class="price">{{good.productPrice}}</div>
            <div class="btn-area">
                <a href="javascript:;" class="btn btn--m">加入购物车</a>
            </div>
        </div>
    </li>
</ul>
```
# 二、其他扩展
## 1、 vue图片懒加载
- api文档地址：
- 安装插件
> npm install vue-lazyload --save  
```
//main.js
import VueLazyload from 'vue-lazyload'

Vue.use(VueLazyload) //直接使用
Vue.use(VueLazyload,{ //配置参数使用
    preLoad: 1.3,
    error: 'dist/error.png',
    loading: 'dist/loading.gif' //指定图片加载之前的loading效果
})
// *.vue中
<img v-lazy="'/static/'+item.productImg"> 
/*
1、使用 v-lazy指令代替原 :src属性，用于懒加载操作
2、当图片未加载完成时就会根据 loading配置的对应路径图片来进行loading显示
3、图片请求完成后再把真实地址附上
*/
```
> 项目中使用的svg动态效果
```
//main.js
Vue.use(VueLazyload,{
    loading: '/static/loading/loading-ball.svg'
})
//loading-ball.svg
更改 fill= "#f00" 来改变填充色
//*.vue
<img v-lazy="'/static/'+item.productImg"> 
```