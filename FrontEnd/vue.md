# Vue

## 基本使用

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <!--引入vue.js文件-->
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
</head>
<body>
    <!--设置一个根节点，起个id-->
<div id="app">
    <!--通过双中括号设置模板，中间是数据-->
    <h1>{{hello}}</h1>
</div>

<script>
    //创建vue对象，指定id到el属性，和data中的数据对象
    let app=new Vue({
        el:"#app",
        data:{
            hello:"hello vue"
        }
    });

</script>
</body>
</html>
```

## 数据绑定

### 双向绑定 v-model

```html
<div id="app">
    <input v-model="username">  <!--通过v-model绑定username的值-->
    <h1>{{username}}</h1>		<!--改变的值会自动渲染到指定的模板-->
</div>
<script>
    let app = new Vue({
        el: "#app",
        data: {
            username: "张三"   //设置默认值
        }
    });
    app.username="李四";   //更改默认值
</script>
```

### 强制绑定 v-bind :

```html
<div id="app">
    <!--如果想让数据绑定到属性中，可以使用v-bind:属性名="exp"来绑定值-->
    <img v-bind:src="imgUrl" style="width: 50px;height: 50px">
    <!--简写方式-->
    <img :src="imgUrl" style="width: 50px;height: 50px">
</div>
<script>
    let app = new Vue({
        el: "#app",
        data: {
            //图片的url地址
            imgUrl:"https://cn.vuejs.org/images/logo.png"
        }
    });
</script>
```

## 模板语法  {{}}

```html
<div id="app">
    <input v-model="username">
    <p>{{username}}</p>  <!--通过双中括号设置模板-->
    <h1>{{username.toUpperCase()}}</h1>  <!--也可以直接使用js表达式-->
    <p v-text="alabel"></p> <!--通过v-text相当于设置textContent,会把内容理解为文本-->
    <p v-html="alabel"></p> <!--通过v-html 相当于设置innerHTML,会把内容理解为元素-->
</div>
<script>
    let app = new Vue({
        el: "#app",
        data: {
            username: "tom",
            alabel: "<a href='www.baidu.com'>Baidu</a>"
        }
    });
</script>
```

## 事件监听 v-on: @

```html
<div id="app">
    <button v-on:click="click1">按钮1</button>
    <!--通过v-on:事件类型="方法|表达式"来绑定事件,没有参数可以不用写括号-->
    <button @click="click2(username,$event)">按钮2</button>
    <!--简写为@事件类型，如果需要带参数，则加括号带。可以直接带入设置的属性，有需要的话需要通过$event手动传入-->
</div>
<script>
    let app = new Vue({
        el: "#app",
        data: {
            username: "tom",
        },
        methods: {   //在methods属性中写方法
            click1: function (event) { //不传任何参数会默认传入event对象，代表当前事件
                console.log(event.target.textContent);
            },
            click2(username, event) {   //如果了解es6的语法，可以简写为这样
                console.log(username);
                console.log(event);
            }
        }
    });
</script>
```

## 计算属性 computed

```html
<div id="app">
    姓:<input type="text" v-model="xing"><br>
    名:<input type="text" v-model="ming"><br>
    全名1:<input type="text" v-model="quanming1"><br>
</div>
<script>
    let app = new Vue({
        el: "#app",
        data: {
            xing:"",
            ming:"",
        },
        computed:{  //计算属性会根据相关联的属性改变时，计算出新的属性
            quanming1(){     
                return this.xing+"-"+this.ming;
            }
        }
        
    });
</script>
```

实现双向的效果

```html
<div id="app">
    姓:<input type="text" v-model="xing"><br>
    名:<input type="text" v-model="ming"><br>
    全名1:<input type="text" v-model="quanming1"><br>
</div>
<script>
    let app = new Vue({
        el: "#app",
        data: {
            xing:"",
            ming:"",
        },
        computed:{      //在computed中设置计算属性,使用其中的属性，对调用对应的方法
            quanming1:{     //计算属性也可以设置为对象，设置get，set方法
                get(){      //当相关属性改变时，调用get方法，计算出新值,可能有缓存
                    return this.xing+"-"+this.ming;
                },
                set(value){  //当自身改变时，调用set方法传入新值
                    let names=value.split("-");  //拆分
                    this.xing=names[0];   //赋值给相关属性
                    this.ming=names[1];
                }
            }
        }

    });
</script>
```

## 监视 watch

```html
<div id="app">
    姓:<input type="text" v-model="xing"><br>
    名:<input type="text" v-model="ming"><br>
    全名1:<input type="text" v-model="quanming1"><br>
</div>
<script>
    let vm = new Vue({
        el: "#app",
        data: {
            xing: "",
            ming: "",
            quanming1: ""
        },
        watch:{     //通过watch属性，中设置要监视的属性，属性要提前在data中声明，
            xing(newValue,oldValue){  //当被监视的属性改变时，会回调，传入新值和旧值
                this.quanming1=newValue+"+"+this.ming;  //this相当于这个Vue对象的实例vm
            }
        }
    });
    vm.$watch("ming",function (newValue) {  //也可以通过Vue实例的$watch方法来监视,传入属性名和回调函数
        this.quanming1=this.xing+"+"+newValue;
    });
    vm.$watch("quanming1",function (newValue) { //监视每一个属性实现双向数据绑定
        let names=newValue.split("+");  //拆分
        this.xing=names[0];   //赋值给相关属性
        this.ming=names[1];
    });
</script>
```

## 样式绑定

### class绑定

```css
.aClass{
    color: #221eff;
}
.bClass{
    color: #ff2321;
}
```

```html
<div id="app">
    <p :class="myClass">hello vue</p>    <!--为样式绑定属性-->
    <p :class="{aClass:isA,bClass:isB}">hello vue</p> <!--可以用对象，属性名为样式名，值为布尔型-->
    <p :class="['aClass']">hello vue</p> <!--也可以用数组，值为样式的字符串，-->
    <button @click="updateC">改变颜色</button>
</div>
<script>
    let vm = new Vue({
        el: "#app",
        data: {
            myClass:"aClass",//设置初始值
            isA:true,
            isB:false
        },
        methods:{
            updateC(){
                this.myClass="bClass"; //改变
                this.isA=!this.isA;   
                this.isB=!this.isB;
            }
        }
    });
</script>
```

### style绑定

```html
<div id="app">
    <!--传一个对象，写法和内联样式差不多-->
    <p :style="{color:myColor,fontSize:myFontSize+'px'}">hello vue</p>
    <button @click="updateC">改变颜色</button>
</div>
<script>
    let vm = new Vue({
        el: "#app",
        data: {
            myColor:"red", //设置默认值
            myFontSize:30
        },
        methods:{
            updateC(){
                this.myColor="blue"; //改变默认值
                this.myFontSize=50;
            }
        }
    });
</script>
```

## 条件渲染 v-if

```html
<div id="app">
    <p v-if="status1==1">第一步</p>  <!--使用v-if来断定值是否显示，值为布尔-->
    <p v-else-if="status1==2">第二步</p> <!--相关模块必须紧跟v-if,否则不识别-->
    <p v-else>结束</p>             <!--只会显示满足条件的属性，其他元素不存在-->

    <p v-show="ok">成功</p>  <!--v-show设置布尔值，也可以决定显示-->
    <p v-show="!ok">失败</p> <!--与if的区别是,v-show两个元素都有，只是false时，display属性为none-->
    <button @click="updateC">执行</button>
</div>
<script>
    let vm = new Vue({
        el: "#app",
        data: {
            status1:1
        },
        methods:{
            updateC(){
                this.status1++;  //改变值
                this.ok=!this.ok;
            }
        }
    });
</script>
```

## 列表渲染 v-for

```html
<div id="app">

    <ul>   <!--使用v-for进行遍历，值类似于for in循环，遍历数组会传入item和index-->
        <li v-for="(stu,index) in students" :key="index">{{index}}:{{stu.name}}+{{stu.age}}
            <!--设置一个key并绑定唯一的值可以让vue更快的追踪-->
            <button @click="delete1(index)">删除</button>
            <button @click="update1(index,{name:'zl',age:10})">更新</button>
        </li>
    </ul>
    <hr>
    <ul>		<!--遍历对象会传入value,key,index-->
        <li v-for="(value,keyname,index) in students[0]">{{index}}:{{keyname}}+{{value}}</li>
    </ul>
</div>
<script>
    let vm = new Vue({
        el: "#app",
        data: {
            students: [//数组对象
                {name: "zs", age: 12},
                {name: "ls", age: 13},
                {name: "ww", age: 14}
            ]
        },
        methods: {
            delete1(index) {  //通过索引删除数组中的元素
                this.students.splice(index, 1);
            },
            update1(index,newStu){
                // this.students[index]=newStu; 直接修改并不会被vue检测到
                this.students.splice(index,1,newStu);
                //splice方法会从指定索引删除指定个数的元素，并插入指定元素到原位置
            }
        }
    });
</script>
```

## 生命周期

## 过滤器

```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script src="https://cdn.bootcss.com/moment.js/2.22.1/moment.js"></script><!--操作时间的js库-->
</head>
<body>
<div id="app">
    <!--通过 竖线 后面接过滤器的名字，可以带参数-->
    <p>{{mydate}}</p> <!--"2018-09-09T16:14:08.495Z"-->
    <p>{{mydate|dateConvert}}</p>  <!--2018-09-10 00:14:08-->
    <p>{{mydate|dateConvert('YYYY-MM-DD')}}</p>  <!--2018-09-10-->
    <p>{{mydate|dateConvert('HH:mm:ss')}}</p>  <!--00:14:08-->

    <p>{{username|daxie}}</p> <!--ABCD-->
</div>
<script>
    //全局过滤器需要实例化vm对象之前设置，参数为名称和函数
    Vue.filter("daxie",function (rawValue) {
        return rawValue.toUpperCase();  //转大写
    });

    let vm = new Vue({
        el: "#app",
        data: {
            mydate:new Date(),
            username:"abcd"
        },
        filters:{ //局部过滤器在filters属性中声明
            dateConvert(mydate,formatStr){  //第一个参数为要过滤的原值,第二个参数为自定义
                if(formatStr==undefined){//没传参数则为undefined
                    return moment().format("YYYY-MM-DD HH:mm:ss");
                }
                return moment().format(formatStr);
            }
        }
    });
</script>
```

# Vue-cli

vue-cli 是创建项目的工具

```
npm i webpack -g    全局安装webpack
npm i vue-cli -g    全局安装vue-cli
```

安装完后，直接执行vue命令查看是否安装成功

**初始化项目**

```
vue init webpack test1        在当前目录创建名为test1的项目,不写则直接使用当前文件夹
```

运行项目

```
npm run dev
```

这时候会提示你项目已经启动在8080端口

 I  Your application is running here: http://localhost:8080

## 组件化编码

创建好的文件目录下，我们的代码会写在src目录下，先删除默认的components，main.js，App.vue

### 组件 components

创建components文件夹，新建一个vue模板文件,Hello.vue,(在webstrom中可以添加或修改vue模板)

```vue
<template>  <!--在template中编写html-->
  <div>  <!--必须要有根节点-->
     <p class="msg">{{msg}}</p>
  </div>
</template>

<script>
  export default {  //es6模块化语法,默认格式,默认对外暴露一个对象,语法和vue一样
    //es6语法，属性值为匿名函数简写，相当于data:function(){}
    data(){   // data属性除了main.js可以写对象，其他地方必须函数，返回数据对象
      return {
        msg:"hello vue-cli"
      };
    }
  }
</script>

<style scoped>
  .msg{
    color: red;
    font-size: 50px;
  }
</style>

```

### 根组件  App.vue

在src目录下创建App.vue,

```vue
<template>
  <div>
    <img class="logo" src="./assets/logo.png" alt="logo"><!--引入资源的方式就参考当前文件的路径即可-->
    <Hello/> <!--三，使用组件标签-->
  </div>
</template>

<script>
  import Hello from "./components/Hello.vue"   //一，引入组件,es6的模块化语法，用./开头

  export default {
    components:{   //二，在components属性中映射组件标签
      Hello       //es6语法，属性名和属性值一致，则简写，相当于 Hello:Hello
    }
  }
</script>

<style>
  .logo{
    width: 100px;
    height: 100px;
  }
</style>

```

### 入口  main.js

在src创建main.js，名称不可变

```js
import Vue from "vue"
import App from "./App.vue"

//一,入口的js，在这里创建vue实例
new Vue({
  el:"#app",  //编译好的组件会被渲染到项目根目录下的index.html中的元素
  components:{
    App        //二，映射根组件
  },
  template:"<App/>"  //三，使用组件标签作为模板，vue会去编译
})

```

### 主页面  index.html

项目根目录下会有index.html,只需要一个div节点指定id

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>test1</title>
  </head>
  <body>
    <div id="app"></div>
    <!-- built files will be auto injected --><!--编译好的文件将会自动注入-->
  </body>
</html>

```

如果已经运行了项目，则去指定的端口号刷新即可看到效果

## 打包发布

### 打包

再项目根目录执行，会创建dist文件夹，里面则是打包好的文件

```
npm run build
```

### 发布

首先全局安装一个静态服务器

```
npm i serve -g
```

这个服务器会运行一个文件夹，并以index.html为默认页面,这里我们直接运行dist

```
serve dist
```

默认端口为5000,访问http://localhost:5000

如果需要发布到动态服务器如tomcat,则需要修改配置

在build中webpack.prod.conf.js修改

```js
  output: {
    publicPath:"/test1/"  //在output中添加该属性，值为项目名称
  },
```

重新编译打包  npm run build

然后拷贝dist出来，更改文件夹名为刚才的项目名,把文件夹拷贝到springboot下的static目录下,即可按路径访问

## 组件间通信

### props

在根组件App.vue中定义，todos数组和删除的方法,通过给组件设置属性传入对象

```vue
<template>
  <div>
    <todo-list :todos="todos" :delTodoA="delTodoA"/>
      <!--标签的使用可以 TodoList 也可以 todo-list 推荐小写-->
      <!--通过设置属性把对象传入，可以是对象，方法，变量-->
  </div>
</template>

<script>
  import TodoList from "./components/TodoList"
  //1导入组件
    export default { 
      components:{
        TodoList  //2声明组件为标签
      },
      data(){
        return {
            todos:[
                {title:"aaa"},
                {title:"bbb"},
                {title:"ccc"}
            ]
        }
      },
      methods:{
        delTodoA(index){
          this.todos.splice(index,1)
        }
      }
    }
</script>

<style>

</style>

```

在todolist组件中通过props接收参数，并声明类型

```vue
<template>
  <ul>
    <todo-item v-for="(todo,index) in todos" :key="index" 
               :index="index" :todo="todo" :delTodoA="delTodoA"/>
      <!--通过v-for遍历子组件，并向子组件传入数据-->
  </ul>
</template>

<script>
  import TodoItem from "./TodoItem"  //注意路径
    
    export default {
      components:{
        TodoItem
      },
      props:{         //通过props接收参数并声明类型
        todos:Array,
        delTodoA:Function
      }
    }
</script>

<style>

</style>


```

在todoitem中继续接收参数

```vue
<template>
	<li>任务:{{todo.title}} <button @click="delTodo">删除</button></li>
</template>

<script>
    export default {
      props:{          //继续接收参数
        todo:Object,
        index:Number,
        delTodoA:Function
      },
      methods:{
        delTodo(){
          let {delTodoA,index,todo}=this     //解构赋值
          if(window.confirm(`确认删除${todo.title}吗`)){  //模板字符串
            delTodoA(index)      //调用根组件传过来的方法
          }
        }
      }
    }
</script>

<style>

</style>

```

组件之间父子关系，通过父组件给子组件设置属性的方式传参数，子组件通过props接收，可以逐级传递

### 自定义事件  @

如果子组件想要调用父组件的方法，可以通过props传方法，也可以自定义事件

```vue
<template>
  <div>
    <todo-head @addTodoA="addTodoA"/>
    <!--通过@事件名="回调函数"  来为一个组件绑定自定义事件-->
  </div>
</template>

<script>
  import TodoHead from "./components/TodoHead"

    export default {
     components:{
         TodoHead
     },
     data(){
        return {
            todos:[
                {title:"aaa"},
                {title:"bbb"},
                {title:"ccc"}
            ]
        }
      },
      methods:{
        addTodoA(todo){       //添加的函数
          this.todos.push(todo)
        }
      }
    }
</script>

<style>

</style>

```

在todohead中，通过emit来触发事件

```vue
<template>
  <div>
    <input v-model="title" @keyup.enter="addTodoA" placeholder="请输入任务"/>
      <!--输入框绑定回车事件-->
  </div>
</template>

<script>
    export default {
      props:{
		//不再接收函数
      },
      data(){
        return{
          title:""
        }
      },
      methods:{
        addTodoA(){
          let {title}=this   //解构赋值
          let todo={title}

          //通过vm对象的$emit("事件名",数据)来触发自定义事件,只能用于直接父子组件
          this.$emit("addTodoA",todo)
            
          this.title=""
        }
      }
    }
</script>

<style>

</style>

```

### 消息系统  pubsub

由于自定义事件无法用于多重父子组件的关系，可以通过pubsub-js来实现消息订阅和发布，来触发事件

1,安装pubsub

```bash
npm i -s pubsub-js
```

2,引入pubsub

```js
import PubSub from "pubsub-js"
```

3,在根组件的生命周期方法中，订阅消息，

```js
   export default { 
	//其他部分省略
       
      //生命周期函数，初始化
      mounted(){
         //通过subscribe("主题",回调函数)
        PubSub.subscribe("delTodo",function (msg,data) {
          console.log(`sub ${msg} `)  //此处的msg为发布消息的主题
          this.delTodo(data)
        }.bind(this))//由于是回调函数，函数内部的this不再是vm，通过bind函数，传入vm，或者使用箭头函数
      
      },
      methods:{
        delTodo(index){
          this.todos.splice(index,1)
        }
      }
    }
```

在子孙组件中发布消息，会触发回调函数

```js
PubSub.publish("delTodo",data)   //第一个参数为主题，也是回调函数的msg
```

通过该插件可以不用考虑关系层数

### 插槽 slot

在父组件中计算好属性的标签通过插槽，到对应的位置 ,在使用子组件的时候在里面写模板标签,slot="name"

```html
<todo-foot :todos="todos" :delSelect="delSelect">
    <span slot="count">已完成{{completeSize}}项</span>
</todo-foot>
```

在子组件中通过slot标签来占位，父组件传入同名的slot

```html
<slot name="count">定义一个插槽占位</slot>
```

## ajax

在vue2中使用axios库来发送ajax，react也是用的这个

```bash
npm i -s axios
```

使用

```js
import axios from "axios"   

let url="https://api.github.com/search/repositories?q=v&sort=stars"
axios.get(url).then(function (response) {
    console.log(response.data.items[0].name) //vue
}).catch(function (error) {
    console.log(error)
})
```

# vue-router

路由可以在一个单页应用spa中，管理页面

## 基本使用

安装

```bash
npm i -s vue-router
```

路由组件，相当于页面，通常放在  pages下，在`src/pages`下创建两个路由组件,`Home.vue `  `About.vue`

```vue
<template>
    <div>
      Home page
    </div>
</template>

<script>
    export default {}
</script>

<style>

</style>

```

配置路由器，在`src/router `下创建`index.js  `,在路由器中注册路由组件

```js
import Vue from "vue"              //导入vue
import VueRouter from "vue-router" //导入路由

import Home from "../pages/Home"  //导入路由组件
import About from "../pages/About" //注意路径

Vue.use(VueRouter)         //使用路由

export default new VueRouter({  //配置映射
      routes:[          //routes 对应一个数组
        {
          path:"/",     //默认路径
          redirect:"/home"  //重定向到一个路径
        },
        {
          path:"/home",    //路径
          component:Home   //路由组件
        },
        {
          path:"/about",
          component:About
        }
      ]

})

```

注册路由器,在main.js中添加配置

```js
import Vue from 'vue'
import App from './App'
import router from "./router"  //导入路由器,会自动找index.js


new Vue({
  el: '#app',
  router,          //配置上路由器
  components: { App },
  template: '<App/>'
})

```

在`App.vue`中配置超链接和页面,使用`router-link`和`router-view`配置连接和显示

```vue
<template>
    <div>
      <router-link to="/home">home link</router-link>
      <!--配置两个超链接-->
      <router-link to="/about">about link</router-link>
        
      <router-view></router-view>     
      <!--这个标签会显示当前路由组件-->
    </div>
</template>

<script>
    export default {}
</script>

<style>

</style>

```

运行

```bash
npm start
```

可以在页面中看到两个`a`标签和主页面

# vuex

