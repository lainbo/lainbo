---
title: 'Vue2的12种和Vue3的7种组件通信'
date: 2019-11-28 21:04:20
tags: [经验技巧]
description: Vue2、3有非常多的传值方式，他们适合的场景各不相同，我进行了全面的总结。
---

![](https://gitee.com/Lainbo/image-beds/raw/master/img/VueComponents.jpg)

# Vue 2
## 1. props
### 就是父传子的属性; props 值可以是一个数组或对象;
``` js
// 数组:不建议使用
props:[]

// 对象
props:{
 inpVal:{
  type:Number, //传入值限定类型
  // type 值可为String,Number,Boolean,Array,Object,Date,Function,Symbol
  // type 还可以是一个自定义的构造函数，并且通过 instanceof 来进行检查确认
  required: true, //是否必传
  default:200,  //默认值,对象或数组默认值必须从一个工厂函数获取如 default:()=>[]
  validator:(value) {
    // 这个值必须匹配下列字符串中的一个
    return ['success', 'warning', 'danger'].indexOf(value) !== -1
  }
 }
}
```
&nbsp;

## 2. $emit
### 触发子组件触发父组件给自己绑定的事件,其实就是子传父的方法
``` js
// 父组件
<home @title="title">
// 子组件
this.$emit('title',[{title:'这是title'}])
```
&nbsp;

## 3. vuex
+ vuex 是一个状态管理器 
+ 是一个独立的插件,适合数据共享多的项目里面,因为如果只是简单的通讯,使用起来会比较重 
+ API
``` js
state:定义存贮数据的仓库 ,可通过this.$store.state 或mapState访问
getter:获取 store 值,可认为是 store 的计算属性,可通过this.$store.getter 或
       mapGetters访问
mutation:同步改变 store 值,为什么会设计成同步,因为mutation是直接改变 store 值,
         vue 对操作进行了记录,如果是异步无法追踪改变.可通过mapMutations调用
action:异步调用函数执行mutation,进而改变 store 值,可通过 this.$dispatch或mapActions
       访问
modules:模块,如果状态过多,可以拆分成模块,最后在入口通过...解构引入
```
&nbsp;

## 4. attrs和listeners
**2.4.0 新增 这两个是不常用属性,但是高级用法很常见**
<br />
+ attrs使用场景:如果父传子有很多值,那么在子组件需要定义多个 props
解决:attrs获取子传父中未在 props 定义的值
``` js
// 父组件
<home title="这是标题" width="80" height="80" imgUrl="imgUrl"/>

// 子组件
mounted() {
  console.log(this.$attrs) //{title: "这是标题", width: "80", height: "80", imgUrl: "imgUrl"}
},
```
<br />

相对应的如果子组件定义了 props,打印的值就是剔除定义的属性
``` js
props: {
  width: {
    type: String,
    default: ''
  }
},
mounted() {
  console.log(this.$attrs) //{title: "这是标题", height: "80", imgUrl: "imgUrl"}
},
```
&nbsp;
+ listeners使用场景:子组件需要调用父组件的方法
解决:父组件的方法可以通过 v-on="listeners" 传入内部组件——在创建更高层次的组件时非常有用
``` js
// 父组件
<home @change="change"/>

// 子组件
mounted() {
  console.log(this.$listeners) //即可拿到 change 事件
}
```
&nbsp;
如果是孙组件要访问父组件的属性和调用方法,直接一级一级传下去就可以

+ inheritAttrs默认值为true，true的意思是将父组件中除了props外的属性添加到子组件的根节点上(说明，即使设置为true，子组件仍然可以通过$attr获取到props意外的属性)将inheritAttrs:false后,属性就不会显示在根节点上了
``` js
// 父组件
<home title="这是标题" width="80" height="80" imgUrl="imgUrl"/>

// 子组件
mounted() {
  console.log(this.$attrs) //{title: "这是标题", width: "80", height: "80", imgUrl: "imgUrl"}
},
```
&nbsp;

## 5. provide和inject
+ 2.2.0 新增
+ 描述:provide 和 inject 主要为高阶插件/组件库提供用例。并不推荐直接用于应用程序代码中;并且这对选项需要一起使用;以允许一个祖先组件向其所有子孙后代注入一个依赖，不论组件层次有多深，并在起上下游关系成立的时间里始终生效。
``` js
//父组件:
provide: { //provide 是一个对象,提供一个属性或方法
  foo: '这是 foo',
  fooMethod:()=>{
    console.log('父组件 fooMethod 被调用')
  }
},

// 子或者孙子组件
inject: ['foo','fooMethod'], //数组或者对象,注入到子组件
mounted() {
  this.fooMethod()
  console.log(this.foo)
}
//在父组件下面所有的子组件都可以利用inject
```
provide 和 inject 绑定并不是可响应的。这是官方刻意为之的。 然而，如果你传入了一个可监听的对象，那么其对象的属性还是可响应的,对象是因为是引用类型
``` js
//父组件:
provide: { 
  foo: '这是 foo'
},
mounted(){
  this.foo='这是新的 foo'
}

// 子或者孙子组件
inject: ['foo'], 
mounted() {
  console.log(this.foo) //子组件打印的还是'这是 foo'
}
```
&nbsp;

## 6. $parent和$children
+ $parent:父实例
+ $children:子实例
``` js
//父组件
mounted(){
  console.log(this.$children) 
  //可以拿到 一级子组件的属性和方法
  //所以就可以直接改变 data,或者调用 methods 方法
}

//子组件
mounted(){
  console.log(this.$parent) //可以拿到 parent 的属性和方法
}
```
children和parent 并不保证顺序，也不是响应式的 只能拿到一级父组件和子组件
&nbsp;
## 7. $refs
+ 即可拿到子组件的实例,就可以直接操作 data 和 methods
``` js
// 父组件
<home ref="home"/>

mounted(){
  console.log(this.$refs.home)
}
```
&nbsp;
8. $root
``` js
// 父组件
mounted(){
  console.log(this.$root) //获取根实例,最后所有组件都是挂载到根实例上
  console.log(this.$root.$children[0]) //获取根实例的一级子组件
  console.log(this.$root.$children[0].$children[0]) //获取根实例的二级子组件
}
```
&nbsp;

## 9. sync
+ 在 vue@1.x 的时候曾作为双向绑定功能存在，即子组件可以修改父组件中的值; 在 vue@2.0 的由于违背单项数据流的设计被干掉了; 在 vue@2.3.0+ 以上版本又重新引入了这个 .sync 修饰符;
``` js
// 父组件
<home :title.sync="title" />
//编译时会被扩展为
<home :title="title"  @update:title="val => title = val"/>

// 子组件
// 所以子组件可以通过$emit 触发 update 方法改变
mounted(){
  this.$emit("update:title", '这是新的title')
}
```
&nbsp;

## 10. v-slot
+ 2.6.0 新增 1.slot,slot-cope,scope 在 2.6.0 中都被废弃,但未被移除 2.作用就是将父组件的 template 传入子组件 3.插槽分类: A.匿名插槽(也叫默认插槽): 没有命名,有且只有一个;
<br />
+ 默认插槽
``` HTML
// 父组件
<todo-list> 
    <template v-slot:default>
       任意内容
       <p>我是匿名插槽 </p>
    </template>
</todo-list> 

// 子组件
<slot>我是默认值</slot>
//v-slot:default写上感觉和具名写法比较统一,容易理解,也可以不用写
```
&nbsp;
+ 具名插槽: 相对匿名插槽组件slot标签带name命名的;
``` HTML
// 父组件
<todo-list> 
    <template v-slot:todo>
       任意内容
       <p>我是匿名插槽 </p>
    </template>
</todo-list> 

//子组件
<slot name="todo">我是默认值</slot>
```
&nbsp;
+ 作用域插槽: 子组件内数据可以被父页面拿到(解决了数据只能从父页面传递给子组件)
``` js
// 父组件
<todo-list>
 <template v-slot:todo="slotProps" >
   {{slotProps.user.firstName}}
 </template> 
</todo-list> 
//slotProps 可以随意命名
//slotProps 接取的是子组件标签slot上属性数据的集合所有v-bind:user="user"

// 子组件
<slot name="todo" :user="user" :test="test">
    {{ user.lastName }}
 </slot> 
data() {
    return {
      user:{
        lastName:"Zhang",
        firstName:"yue"
      },
      test:[1,2,3,4]
    }
  },
// {{ user.lastName }}是默认数据  v-slot:todo 当父页面没有(="slotProps")
```
&nbsp;

## 11. EventBus（事件总线）
+ 就是声明一个全局Vue实例变量 EventBus , 把所有的通信数据，事件监听都存储到这个变量上;
+ 类似于 Vuex。但这种方式只适用于极小的项目 3.原理就是利用on和emit 并实例化一个全局 vue 实现数据共享
``` js
// 在 main.js
Vue.prototype.$eventBus=new Vue()

// 传值组件
this.$eventBus.$emit('eventTarget','这是eventTarget传过来的值')

// 接收组件
this.$eventBus.$on("eventTarget",v=>{
  console.log('eventTarget',v);//这是eventTarget传过来的值
})
```
+ 可以实现平级,嵌套组件传值,但是对应的事件名eventTarget必须是全局唯一的
&nbsp;
## 12. 路由传参
有三种方法
+ 第一种方法
``` js
// 路由定义
{
  path: '/describe/:id',
  name: 'Describe',
  component: Describe
}
// 页面传参
this.$router.push({
  path: `/describe/${id}`,
})
// 页面获取
this.$route.params.id
```
&nbsp;
+ 第二种方法
``` js
// 路由定义
{
  path: '/describe',
  name: 'Describe',
  omponent: Describe
}
// 页面传参
this.$router.push({
  name: 'Describe',
  params: {
    id: id
  }
})
// 页面获取
this.$route.params.id
```
&nbsp;
+ 第三种方法
``` js
// 路由定义
{
  path: '/describe',
  name: 'Describe',
  component: Describe
}
// 页面传参
this.$router.push({
  path: '/describe',
    query: {
      id: id
  }
)
```
&nbsp;
**三种方案对比 方案二参数不会拼接在路由后面,页面刷新参数会丢失 方案一和三参数拼接在后面,丑,而且暴露了信息**

## 13. Vue.observable
+ 2.6.0 新增
+ 用法:让一个对象可响应。Vue 内部会用它来处理 data 函数返回的对象;
返回的对象可以直接用于渲染函数和计算属性内，并且会在发生改变时触发相应的更新;
+ 也可以作为最小化的跨组件状态存储器，用于简单的场景。
+ 通讯原理实质上是利用Vue.observable实现一个简易的vuex
``` js
// 文件路径 - /store/store.js
import Vue from 'vue'

export const store = Vue.observable({ count: 0 })
export const mutations = {
  setCount (count) {
    store.count = count
  }
}
```
``` HTML
//使用
<template>
    <div>
        <label for="bookNum">数 量</label>
            <button @click="setCount(count+1)">+</button>
            <span>{{count}}</span>
            <button @click="setCount(count-1)">-</button>
    </div>
</template>

<script>
import { store, mutations } from '../store/store' // Vue2.6新增API Observable

export default {
  name: 'Add',
  computed: {
    count () {
      return store.count
    }
  },
  methods: {
    setCount: mutations.setCount
  }
}
</script>
```

&nbsp;

# Vue 3

## props
```HTML
// Parent.vue 传送
<child :msg2="msg2"></child>
<script setup>
    import child from "./child.vue"
    import { ref, reactive } from "vue"
    const msg2 = ref("这是传给子组件的信息2")
    // 或者复杂类型
    const msg2 = reactive(["这是传级子组件的信息2"])
</script>

// Child.vue 接收
<script setup>
    // 不需要引入 直接使用
    // import { defineProps } from "vue"
    const props = defineProps({
        // 写法一
        msg2: String
        // 写法二
        msg2:{
            type:String,
            default:""
        }
    })
    console.log(props) // { msg2:"这是传级子组件的信息2" }
</script>
```

## $emit
```HTML
// Child.vue 派发
<template>
    // 写法一
    <button @click="emit('myClick')">按钮</buttom>
    // 写法二
    <button @click="handleClick">按钮</buttom>
</template>
<script setup>
    
    // 方法一 适用于Vue3.2版本 不需要引入
    // import { defineEmits } from "vue"
    // 对应写法一
    const emit = defineEmits(["myClick","myClick2"])
    // 对应写法二
    const handleClick = ()=>{
        emit("myClick", "这是发送给父组件的信息")
    }
    
    // 方法二 不适用于 Vue3.2版本，该版本 useContext()已废弃
    import { useContext } from "vue"
    const { emit } = useContext()
    const handleClick = ()=>{
        emit("myClick", "这是发送给父组件的信息")
    }
</script>

// Parent.vue 响应
<template>
    <child @myClick="onMyClick"></child>
</template>
<script setup>
    import child from "./child.vue"
    const onMyClick = (msg) => {
        console.log(msg) // 这是父组件收到的信息
    }
</script>
```

## expose / ref

```HTML
// Child.vue
<script setup>
    // 方法一 不适用于Vue3.2版本，该版本 useContext()已废弃
    import { useContext } from "vue"
    const ctx = useContext()
    // 对外暴露属性方法等都可以
    ctx.expose({
        childName: "这是子组件的属性",
        someMethod(){
            console.log("这是子组件的方法")
        }
    })
    
    // 方法二 适用于Vue3.2版本, 不需要引入
    // import { defineExpose } from "vue"
    defineExpose({
        childName: "这是子组件的属性",
        someMethod(){
            console.log("这是子组件的方法")
        }
    })
</script>

// Parent.vue  注意 ref="comp"
<template>
    <child ref="comp"></child>
    <button @click="handlerClick">按钮</button>
</template>
<script setup>
    import child from "./child.vue"
    import { ref } from "vue"
    const comp = ref(null)
    const handlerClick = () => {
        console.log(comp.value.childName) // 获取子组件对外暴露的属性
        comp.value.someMethod() // 调用子组件对外暴露的方法
    }
</script>
```

## attrs
```HTML
// Parent.vue 传送
<child :msg1="msg1" :msg2="msg2" title="3333"></child>
<script setup>
    import child from "./child.vue"
    import { ref, reactive } from "vue"
    const msg1 = ref("1111")
    const msg2 = ref("2222")
</script>

// Child.vue 接收
<script setup>
    import { defineProps, useContext, useAttrs } from "vue"
    // 3.2版本不需要引入 defineProps，直接用
    const props = defineProps({
        msg1: String
    })
    // 方法一 不适用于 Vue3.2版本，该版本 useContext()已废弃
    const ctx = useContext()
    // 如果没有用 props 接收 msg1 的话就是 { msg1: "1111", msg2:"2222", title: "3333" }
    console.log(ctx.attrs) // { msg2:"2222", title: "3333" }
    
    // 方法二 适用于 Vue3.2版本
    const attrs = useAttrs()
    console.log(attrs) // { msg2:"2222", title: "3333" }
</script>
```

## v-model
```HTML
// Parent.vue
<child v-model:key="key" v-model:value="value"></child>
<script setup>
    import child from "./child.vue"
    import { ref, reactive } from "vue"
    const key = ref("1111")
    const value = ref("2222")
</script>

// Child.vue
<template>
    <button @click="handlerClick">按钮</button>
</template>
<script setup>
    
    // 方法一  不适用于 Vue3.2版本，该版本 useContext()已废弃
    import { useContext } from "vue"
    const { emit } = useContext()
    
    // 方法二 适用于 Vue3.2版本，不需要引入
    // import { defineEmits } from "vue"
    const emit = defineEmits(["key","value"])
    
    // 用法
    const handlerClick = () => {
        emit("update:key", "新的key")
        emit("update:value", "新的value")
    }
</script>
```

## provide / inject

+ provide / inject 为依赖注入
+ provide：可以让我们指定想要提供给后代组件的数据或
+ inject：在任何后代组件中接收想要添加在这个组件上的数据，不管组件嵌套多深都可以直接拿来用
```HTML
// Parent.vue
<script setup>
    import { provide } from "vue"
    provide("name", "小明")
</script>

// Child.vue
<script setup>
    import { inject } from "vue"
    const name = inject("name")
    console.log(name) // 小明
</script>
```

## Vuex
```js
// store/index.js
import { createStore } from "vuex"
export default createStore({
    state:{ count: 1 },
    getters:{
        getCount: state => state.count
    },
    mutations:{
        add(state){
            state.count++
        }
    }
})
```

```js
// main.js
import { createApp } from "vue"
import App from "./App.vue"
import store from "./store"
createApp(App).use(store).mount("#app")
```

```HTML
// Page.vue
// 方法一 直接使用
<template>
    <div>{{ $store.state.count }}</div>
    <button @click="$store.commit('add')">按钮</button>
</template>

// 方法二 获取
<script setup>
    import { useStore, computed } from "vuex"
    const store = useStore()
    console.log(store.state.count) // 1

    const count = computed(()=>store.state.count) // 响应式，会随着vuex数据改变而改变
    console.log(count) // 1 
</script>
```