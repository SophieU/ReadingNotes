## 一、简介
#### 1.1 对比grunt,gulp,webpack
- grunt,gulp 都是对js，css，html的压缩优化以及版本号的替换
- webpack更多的重点在于解析项目文件。webpack在打包时，把所有的资源都通过js处理转化。当文件格式不是默认的那几种时，会寻找loader插件来进一步识别再转成js，再打包输出
- 基于模块化的打包工具

#### 1.2 webpack基础
> 1、基础插件
- html-webpack-plugin ：根据同一个模板生成多个页面
- extract-text-webpack-plugin
- UglifyJSPlugin ： js压缩插件
- CommonsChunkPlugin  ： 把多个页面中公用的文件抽出
- clean-webpack-plugin ： 打包过程前清除以前的文件
- copy-webpack-plugin: 

> 2、常用loader解析器
- css-loader （解析css文件）
- sass-loader/less-loader/node-sass （预编译解析）
- file-loader/url-loader 解析图片（png，jpg/svg/gif）
- 给css添加前缀： postcss-loader，autoprefixer

> 3、webpack.config.js配置文件
```
//webpack3.0不再支持相对路径，所以在node项目中，可以使用path模块来将相对路径转为绝对路径
var path = require('path'); 

// 核心配置
module.exports={
    // 入口文件
    entry:'./src/lib/index.js', 
    // 出口配置
    output:{
        path:path.join(__dirname,'./dist'), //输入路径
        filename:'vue-toast-demo.js', //打包后文件名
        // 打包后的格式（三种规范amd,cmd,common.js)通过umd规范可以适应各种规范，以及全局window
        libraryTarget:'umd', 
        library: 'VueToastDemo'
    },
    module:{
        rules:[ //解析模块时需要的模块加载器
            {
                test:/\.vue$/,
                loader:'vue-loader'
            },
            { test: /\.js$/, exclude: /node_modules/, loader: "babel-loader" }
        ]
    },
    plugins:[]
}
```

## 二、开发一个vue-toast插件
- 借助npm平台发布一个vue插件
- 流程： 声明插件——写插件——注册插件——使用插件

> 官方文档中说明：写插件有四种方法：
1. 添加全局方法或属性
```
Vue.myGlobalMethod = function(){...}
```
2. 添加全局资源
```
Vue.directive('my-directive',{
    bind(el,binding,vnode,oldVnode){...}
})
```
3. 注入组件
```
    Vue.mixin({
        created:function(){}
    })
```
4. 添加实例方法
```
Vue.prototype.$myMethod =function(options){}
```

> 开发vue插件的几个基本步骤
- Vue.js 的插件应当有一个公开方法 install 。这个方法的第一个参数是 Vue 构造器，第二个参数是一个可选的选项对象 myplugin.install = function(Vue,options){...}
- 官方说明：https://cn.vuejs.org/v2/guide/plugins.html#使用插件
```

import ToastComponent from './vue-toast.vue' //引入vue模板组件

let Toast = {}
Toast.install = function(){ //通过install注册插件
    Vue.prototype.$toast = function(){
        Vue.extend(ToastComponent)
    }
} 
if(window.Vue){
    Vue.use(Toast) //如果是直接用script标签引入插件，可通过此法注册插件到vue
}
export default Toast;   //导出toast
```

## 实践
> 需求：一个toast弹层功能
> 1、template.vue。提供html模板
```
<template>
    <section class="toast-container" :class="visible?'fade-in':'fade-out'">
        <div class="toast">
            <span>{{message}}</span>
        </div>
    </section>
</template>
<script>
    export default {
        name:'tmp',
        data(){
            return{
                visible:true,
                message:'默认提示语'
            }
        }
    }
</script>

```
> 2、index.js
```
import ToastComponent from './vue-toast.vue'

let Toast = {}
Toast.install = function(Vue,options){
    var opt={
        duration:3000,
    }
    for(var key in options){
        opt[key] = options[key];
    }
    Vue.prototype.$toast=function(msg,option){
        if(typeof option =='object'){
            for(var key in option){
                opt[key]=option[key]
            }
        }
      const ToastController= Vue.extend(ToastComponent);

      var instance = new ToastController().$mount(document.createElement('div'))

      instance.message = msg;
      instance.visible = true;

      document.body.appendChild(instance.$el)
      setTimeout(()=>{
          instance.visible=false;
          document.body.removeChild(instance.$el)
      },opt.duration)
    }
    Vue.prototype.$toast['show']=function(msg,option){
        Vue.prototype.$toast(msg,option);
    }
    Vue.prototype.$toast['success'] = function(msg,option){
        Vue.prototype.$toast(msg,option);
    }
    Vue.prototype.$toast['info'] = function(msg,option){
        Vue.prototype.$toast(msg,option);
    }
    Vue.prototype.$toast['error'] = function(msg,option){
        Vue.prototype.$toast(msg,option);
    }
}
if(window.Vue){
    Vue.use(Toast)
}

export default Toast;
```
## 总结
- 使用基础Vue构造器，通过vue组件来创建一个子类：Vue.extend(component)
- 编写vue插件的四种方法：常用-Vue.prototype.$method, 其他：Vue.method,Vue.mixin(option),Vue.directive('method',option)
- webpack配置output的path必须为绝对路径
- webpack配置三大属性,entry,output,module,plugins