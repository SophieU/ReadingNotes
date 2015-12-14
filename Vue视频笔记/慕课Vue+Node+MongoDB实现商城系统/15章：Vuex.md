## 一、简介
> Vuex是什么？
- 专为Vue设计的状态管理器。用于管理全局的状态。什么是状态？各组件中data中所包含的属性即为状态。（可以理解为类似window上的属性）

> 核心概念
- State ：用于存储状态
- mutations ：用于改变state的值
- getters 
- actions
- modules

## 二、核心
#### 2.1 State
- State是唯一的数据源，是一个数据载体。所有抽取出的变量都放其中
- 单一的状态树
```
const Counter={
    template : `<div>{{count}}</div>`,
    computed:{
        count(){
            //当注册了vuex到全局时，this.$store就指向了全局的vuex对象
            return this.$store.state.count 
        }
    }
}
```

#### 2.2 Getters
- 基于state，来处理后，返回想要的状态。如：state中存储状态count，要求count不能超过99时，可以通过getters来限制。
```
const store = new Vuex.Store({
    state:{
        todos:[
            {id:1,text:'...',done:true},
            {id:2,text:'...',done:false}
        ]
    },
    getters:{
        doneTodos:state=>{
            return state.todos.filter(todo=>todo.done)
        }
    }
})
```

#### 2.3 Mutations
- 用于提交更改store中的状态
- 注意：mutation是更改store中状态的唯一方法
```
const store = new Vuex.Store({
    state:{
        count:1
    },
    mutations:{
        increment(state){ //函数名可随意
            //变更状态
            state.count++
        }
    }
})
// 外部触发
store.commit('increment')
```

#### 2.4 actions
- actions用于提交mutation，而不是直接变更状态
- actions可以包含任意异步操作
- 应用场景（mutation只能用于同步改变state，而当需要异步操作时，就可以使用actions）
```
const store = new Vuex.Store({
    state:{
        count:1
    },
    mutaions:{
        increment(state){ //函数名可随意
            //变更状态
            state.count++
        }
    },
    actions:{
        increment(context){ //context为全局注册vuex时自动添加的
            context.commit('increment') // 触发mutations
        }
    }
})
```

#### 2.5 Modules
- 当应用程序很大时，需要管理的状态很多时，需要将state进行拆分，分割成模块（modules)，最后统一管理
```
const moduleA = {
    state:{...},
    mutations:{...},
    actions:{...},
    getters:{...}
}
const moduleB = {
    state:{...},
    mutations:{...},
    actions:{...},
    getters:{...}
}
const store = new Vuex.Store({
    modules:{
        a:moduleA,
        b:moduleB
    }
})

store.state.a
store.state.b
```
#### 2.6 项目结构
|—— index.html
|—— main.js
|—— api
|   |——。。。#抽取出的API请求
|—— components
|   |—— App.vue
|   |__  。。。
|—— store
    |—— index.js  # 我们组装模块并导出store的地方
    |—— actions.js  # 根级别的action
    |—— mutations.js #根级别的mutation
    |—— modules
        |—— cart.js  #购物车模块
        |—— product.js #产品模块

## 三、实践
- 引入：当页面直接用script标签引用vuex时，不需要再注册。若使用vue-cli搭建的spa应用，需要使用Vue.use(vuex)
> 1、基本使用
```
#html
    <counter></counter>
## js引用
<scrit src="../../node_modules/vue/dis/vue.js/>
<scrit src="../../node_modules/vuex/dis/vuex.js/>
## js
const counter = {
    template:`<div>{{count}}</div>`,
    computed:{
        count(){
            return this.$store.state.count
        }
    }
}
const store = new Vuex.Store({
    state:{
        count:10
    }
})
new Vue({
    el:'#app',
    data:{},
    components:{
        counter
    }
})
```
> 2、改变state
- 关键：在外部通过this.$store.commit('mutationsName')来触发mutations中定义的事件名，
```
#html
    <button @click='add'>add</button>
#js
    const store= new Vuex.Store({
        state:{
            count:12
        },
        mutations:{
            increment(state,num){ //num为传入的参数
                state.count+=num
            }
        }
    })
    new Vue({
        ...
        methods:{
            add(){
                this.$store.commit('increment',100) //注：increment是一个字符串
            }
        }
        ...
    })
```
> 3、异步提交actions
- 关键：外部通过this.$store.dispatch('actionName',param)触发action
- store内，通过context.commit('mutationsName',param) 触发mutation
```
# store中
    mutations:{
        increment(state,num){state.count+=num;}
    }
    actions:{
        incrementAction(context){
            context.commit('increment',num)
        }
    }
# vue实例中
    methods:{
        addAction(){
            this.$store.dispatch('incrementAction',100)
        }
    }
```
> 4、getters
```
# store中
    state:{userName:'jack'},
    getters:{
        helloUser(state){
            return state.userName+',hello';
        }
    }
# 实例中
    computed:{
        userName:{
            return this.$store.getters.helloUser
        }
    }
```
> 5、mapState
- vuex提供的辅助性函数，通过传入一个要使用的state的名字的数组来在组件中调用对应state
```
# 实例中
import {mapState} from 'Vuex'
    computed:{
        ...mapState(['userName','count'])
    }
# 等价于
    computed:{
        userName(){
            return this.$store.state.userName
        },
        count(){
            return this.$store.state.count
        }
    }
```

