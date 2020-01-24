# ECMAScript

## 一,ES5

### 严格模式

在js的第一行加上这个字符串即开启严格模式

```js
"use strict"

//1,必须使用var 声明变量
var a=1   //a=1       错误

//2,自定义函数中的this不能指向window
function Person(name) {
    this.name=name
}
new Person("aa")   //Person("aa")   错误  this会指向window

//3,创建eval作用域   eval中定义的变量只能在eval中使用
var str="zs"
eval("var str='ls';console.log(str)")  //非严格ls  严格ls
console.log(str) //非严格ls 严格zs

//4.对象不能用重名属性
```

### Object扩展

create

```js
var obj1={name:"zs"}
var obj2={}
//Object.create可以以一个对象为原型，作为prototype,并可以添加自定义属性,得到新对象
obj2=Object.create(obj1,{
    age:{         //属性名
        value:12, //默认值
        writable:true, //可以被修改，默认为false
        configurable:true,//可以被删除，默认为false
        enumerable:true//可以被for in 枚举，默认为false
    }
})

console.log(obj2)//{age:12}
console.log(obj2.__proto__)//{name:'zs'}
```

defineProperties

```js
var obj1={xing:"zhang",ming:"san"}

Object.defineProperties(obj1,{     //扩展一个对象的属性，属性可以被计算和监听
    quan:{                         //配置对象的属性
        get:function () {         //get方法得到属性，可以通过已有的属性计算
            return this.xing+" "+this.ming
        },
        set:function (data) {    //set方法，当修改属性时调用，修改已有属性
            var names=data.split(" ")
            this.xing=names[0]
            this.ming=names[1]
        }
    }
})

console.log(obj1)//{ xing: 'zhang', ming: 'san' }  计算属性会惰性加载不用不会显示
console.log(obj1.quan)//zhang san     取值的时候则会调用改属性的get方法计算
obj1.quan="li si"            //设置属性的时候，会调用set方法，一起修改原有属性
console.log(obj1)//{ xing: 'li', ming: 'si' }

```

存取器属性

定义对象时也可以直接设置存取器属性`get 属性名(){}`和`set 属性名(){}`

```js
var obj1={
    xing:"zhang",
    ming:"san",
    get quan(){          
        return this.xing+" "+this.ming
    },
    set quan(data){
        var names=data.split(" ")
        this.xing=names[0]
        this.ming=names[1]
    }
}
console.log(obj1)//{ xing: 'zhang', ming: 'san', quan: [Getter/Setter] }
console.log(obj1.quan)//zhang san     取值的时候则会调用改属性的get方法计算
obj1.quan="li si"            //设置属性的时候，会调用set方法，一起修改原有属性
console.log(obj1)//{ xing: 'li', ming: 'si', quan: [Getter/Setter] }
```

### Array扩展

map filter

```js
var arr=[3,2,65,7,82,4,7]

//1,通过值找第一个下标
console.log(arr.indexOf(7))
//2，通过值找最后一个下标
console.log(arr.lastIndexOf(7))
//3，遍历数组
arr.forEach(function (item,index) {
    console.log(item,index)
})
//4,映射一个新的数组，值为加工后的值
var arr2=arr.map(function (item, index) { 
    return item+"px"
})
console.log(arr2)//[ '3px', '2px', '65px', '7px', '82px', '4px', '7px' ]
//5,过滤数组，返回布尔值来判断
var arr3=arr.filter(function (item,index) {
    return item>10
})
console.log(arr3)//[ 65, 82 ]

```

### Function扩展

bind

```js
var obj={name:"zs"}

function fun1(p1, p2) {
    console.log(this,p1,p2)
}

fun1(1,2)//window 1,2  直接执行函数，this为window
fun1.call(obj,1,2)//{ name: 'zs' } 1 2   通过call立即执行，绑定this为obj
fun1.apply(obj,[1,2])//{ name: 'zs' } 1 2  同上，只是其他参数需要封装为array

var fun2=fun1.bind(obj)//bind方法会返回一个函数，但是不会立即执行
fun2(1,2)//{ name: 'zs' } 1 2   通过调用执行

fun1.bind(obj)(1,2)//{ name: 'zs' } 1 2   简写

setInterval(function () {    //需要传回调函数的情况，只能bind
    console.log(this)        //call和apply都会立即执行，只会传返回值
}.bind(obj),1000)


```

## 二，ES6

### let   和    const

**let** 用于声明变量，取代var

1,不能重复定义

```js
var a = 1
var a = 2
console.log(a)//2

let b = 1
let b = 2 //SyntaxError: Identifier 'b' has already been declared
console.log(b)
```

2,没有变量提升

```js
console.log(a)//undefined
var a = 1

console.log(b) // ReferenceError: b is not defined
let b = 1
```

3,有自己的块级作用域

```js
var btns = document.getElementsByTagName("button")
//给所有按钮都绑定事件

for (var i = 0; i < btns.length; i++) {  //直接给button绑定事件
    btns[i].onclick = function () {       //由于i的作用域，在循环完后，已经是3
        console.log(i)                  //打印出来的都是3 3 3
    }
}
//需要通过闭包来限制i的作用域
for (var i = 0; i < btns.length; i++) {
    (function (i) {
        btns[i].onclick = function () {
            console.log(i)     //此时为1 2 3
        }
    })(i)                    //i作为变量传入作用域
}

for (let i = 0; i < btns.length; i++) {  //使用let声明的变量，在循环中有自己的作用域
    btns[i].onclick = function () {
        console.log(i)   //1 2 3 
    }
}
```

**const**用来定义常量，拥有let的特性，无法被修改

```js
const a=1
a=2     //TypeError: Assignment to constant variable.
console.log(a)
```

### 解构赋值

以前从对象中取多个属性时,重复操作

```js
let obj={name:"zs",age:12}
let name=obj.name
let age=obj.age
```

解构对象

通过中括号声明，变量名必须和目标对象的属性名一致，一次声明多个变量，不匹配的变量值为undefined

```js
let obj={name:"zs",age:12}

let {name,sex,age}=obj       //通过let {变量名1，变量名2}=对象
console.log(name,age,sex)//zs 12 undefined

//方法的形参也能解构赋值
function fun1({name,age}){
    console.log(name,age)  //zs 12
}
fun1(obj)
```

解构数组

通过方括号，按照下标来声明赋值，通过逗号`,`来占位，得到想要的下标，越界则undefined

```js
let arr=[1,2,3,4,5]

let [,,a,,b,c]=arr

console.log(a,b,c)//3 5 undefined
```

### 模板字符串

以前拼凑字符串需要用加号

```js
let obj={name:"zs",age:12}
console.log("我是"+obj.name+",今年"+obj.age+"岁")//我是zs,今年12岁
```

使用模板字符串可以更方便的拼字符串,使用(1左边那个点)来包含，中间使用 ${变量}  来使用变量

```js
let obj={name:"zs",age:12}       //使用  `模板字符串`
console.log(`我是${obj.name},今年${obj.age}`)//我是zs,今年12岁
```

### 对象简写

```js
let name="zs"
let age=12

let p1={      //以前的写法    
    name:name,   // 属性名: 属性值
    age:age,
    say:function () {    //方法名:function(){}
        console.log(this.name)
    }
}
p1.say() //zs

let p2={   //简写方式
    name,  //属性名和变量名一致，直接写即可
    age,
    say(){ //方法名(){}   
        console.log(this.name)
    }
}
p2.say()//zs

```

### 箭头函数

定义

`(参数)=>{代码块}`

```js
let fun=function(){      
    console.log(1)
}

//使用箭头函数实现同样的效果
let fun1=()=>console.log(1)

//仅当参数只有一个时，()可以省略 
//代码块只有一行时，{}可省略，并且会返回那一行执行的结果
let fun2=(x,y)=>{   
    console.log(this)  //箭头函数的this取决于定义箭头函数时的环境  
    return x+y
}

console.log(fun1(1,2))
```

### 三点运算符

可变参数

```js
//可变参数，可以收集最后部分的形参，只能写在最后
function fun1(p1,...ps) {
    console.log(p1)  //1     占位了一个参数
    console.log(ps)  //[2,3,4]  真数组,可以遍历，用来取代arguments
}
fun1(1,2,3,4)
```

扩展运算符

```js
let arr2=[2,3,4,5]

arr=[1,...arr2,6]  //三点加载数组前，相当于得到数组的每一个值
console.log(arr)  //[ 1, 2, 3, 4, 5, 6 ]
console.log(...arr)//1 2 3 4 5 6
```

### 形参默认值

```js
function fun1(a = 1) {  //在设置形参的时候设置默认值，如果没有传入参数，则使用默认值
    console.log(a)
}

fun1(2)  //2
fun1()   //1

```

### promise对象

当执行异步任务的时候,如ajax,定时器，当ajax请求成功或者失败后，也许还会有异步任务，异步任务是需要回调函数

直接写会导致，回调函数层层嵌套，俗称回调地狱

promise 对象可以把后续的异步任务提取出来执行,避免回调地狱

```js
var prms=new Promise((resolve, reject)=>{    //创建  接受一个回调函数
    //初始化promise对象  状态为pending
    console.log(1)
    //执行异步任务时，如ajax，成功或者失败可以掉方法.传参数
    setTimeout(()=>{
        console.log(3)
        //resolve(200)   执行成功调用  修改状态为fullfilled 已完成
        reject(404)    //执行失败调用  修改状态为rejected   不合格
    },3000)
})
console.log(2)

prms.then((status)=>{  //第一个参数为，成功的回调函数 
    setTimeout(()=>{    //后续异步任务
        console.log(status)
    },2000)
},(status)=>{          //第二个为失败的回调函数
    setTimeout(()=>{
        console.log(status)
    },2000)
})
```

### Symbol属性

symbol是一个方法  ，会返回一个不会重复的值，用于属性的解决命名冲突，是一种基本数据类型

```js
let obj={}

let sb1=Symbol()
console.log(sb1)//Symbol()
console.log(typeof sb1)//symbol   属于基本类型
obj[sb1]="zs"        //添加属性
console.log(obj)//{ [Symbol()]: 'zs' }

let sb2=Symbol("s")    //可以传入参数作为标识
console.log(sb2)//Symbol(s)
obj[sb2]="ls"
console.log(obj)//{ [Symbol()]: 'zs', [Symbol(s)]: 'ls' }
//symbol属性不能被for in 和 for of 遍历
```

还可以定义常量

```js
const MY_KEY=Symbol("mykey")
console.log(MY_KEY)     //Symbol(mykey)
```

### iterator接口

实现了iterator接口的对象可以用for of 遍历

```js
for (var i of "243423") {
    console.log(i)
}
```

### Generator函数

函数在`function`后面加一个`*`号表示`Generator`函数,通过`yield`关键字来暂停函数的流程

```js
function* myGenerator() {
    console.log("start.....") //2.start.....  开始执行,遇到yield暂停，并返回值
    let res=yield "status1"//5,第二次运行上次暂停的地方开始，接受next的参数
    console.log("second..."+res)//6.second...666  第二次执行，使用参数,遇到下一个yield暂停
    yield "status2" //9，第三次执行
    console.log("end..") //10,end.. 执行代码 ，后面没有yield则方法的对象done属性为true
    return 2         //有return 则value为return的值，没有则为undefined
}

let mg=myGenerator()   //初始化不会允许方法的代码，会返回一个指针对象
console.log(mg.next()) //1.通过next方法开始执行函数的代码
//3. { value: 'status1', done: false } 会得到第一个yield的返回值，done表示是否全部完成，false可以继续next
console.log(mg.next("666"))//4,再次通过next方法，这次传入一个参数，会成为上次暂停位置的返回值
//7.{ value: 'status2', done: false } 得到第二个yield的返回值
console.log(mg.next())//8,第三次执行，
//11,{ value: 2, done: true }  接受参数 true表示已经完成

```

### async函数

async只是generator的语法糖，替换了关键字，但是不需要通过next调用，执行完异步操作会自动执行后续代码

```js
async function fun1() {
    await console.log(1)
    await console.log(2)
}

fun1()

```

### class关键字

```js
class Person{  //class 类名  {}
    //类的构造方法
    constructor(name,age){
        this.name=name
        this.age=age
    }              //方法直接不要加逗号
    
    //普通方法
    say(){          //在class里必须全部简写
        console.log(this.name)
    }

}

let p1=new Person("zs",12)  //通过new关键字调用构造方法
console.log(p1)   //Person { name: 'zs', age: 12 }
p1.say()        //zs   通过对象调用方法


class Student extends Person{     //通过extends继承一个 ，拥有父类的普通方法
    constructor(name,age,grade){
        super(name,age)        //必须在构造方法中通过super调用父类方法
        this.grade=grade
    }
    say(){             //重写父类方法
        console.log(this.name,this.grade)
    }
}

let stu1=new Student("qq",13,3)
console.log(stu1) //Student { name: 'qq', age: 13, grade: 3 }
stu1.say()    //qq 3

```

### string扩展

```js
let str="abcde"
console.log(str.includes("b"))//true      包含
console.log(str.startsWith("a"))//true    以什么开头
console.log(str.endsWith("e"))//true      以书面结束
console.log(str.repeat(3))//abcdeabcdeabcde  重复n次
```

### Number扩展

```js
console.log(0b1010) //10    二进制用0b
console.log(0o56)   //46    八进制用0o

console.log(Number.isFinite(Infinity))//false  是否有限大
console.log(Number.isNaN(NaN))    //true   是否是非数字

console.log(Number.isInteger(1.2))//false  是否是整数
console.log(Number.isInteger(123))//true

console.log(Number.parseInt("123abc"))//123  解析字符串转为数字
console.log(Number.parseInt("er123abc"))//NaN

console.log(Math.trunc(12.22))//12  去掉小数
```

### Array扩展

```js
function fun1() {
    //1,Array.from(伪数组或可迭代对象) 返回真数组
    Array.from(arguments).forEach((item,index)=>{
        console.log(item,index)  //a 0
    })
}
fun1("a","b","c")

//2,Array.of(v1,v2,v3)  将一系列值转为数组
let arr=Array.of(1,2,3,4,5)
console.log(arr)//[1,2,3]

//4,array.find(fun)  返回第一个返回true的元素
let res=arr.find(item=>item>3)
console.log(res) //4

//5,array.findIndex(fun)  返回第一个返回true的元素的下标
res=arr.findIndex(item=>item>3)
console.log(res) //3
```

### Object扩展

```js
//1,Object.is(v1,v2)  比较两个对象是否完全一样
console.log(Object.is(0,-0))  //f
console.log(Object.is(NaN,NaN))//t
console.log(Object.is({},{}))//f

//2,Object.assign(target,source1,source2...)  将属性赋值到目标对象
let obj={}
let obj1={name:"zs"}
let obj2={age:12}
Object.assign(obj,obj1,obj2)
console.log(obj) //{ name: 'zs', age: 12 }

//3,通过 实例.__proto__ 直接操作原型对象
let obj3={name:"ls"}
let obj4={}
obj4.__proto__=obj3
console.log(obj4.name)//ls
```

### Set Map 容器

set是一个不能重复的集合

```js
let set1=new Set([1,2,3,4,3])     //通过new Set() 创建 可以传一个数组
console.log(set1)//Set { 1, 2, 3, 4 } 去掉重复的元素
console.log(set1.size)//4
set1.add(5)        //添加元素
set1.delete(2)     //删除元素
console.log(set1)//Set { 1, 3, 4, 5 }
console.log(set1.has(1))//true  判断是否存在
set1.clear() //清空
console.log(set1)//Set {}
```

map是一个键值对集合

```js
let map1=new Map([["name","zs"],[22,"age"]])   //通过new Map() 可以传入一个二维数组，分别为键值
console.log(map1)//Map { 'name' => 'zs', 22 => 'age' }
console.log(map1.size)//2         
map1.set("k3","v3")       // set(key,value)      添加键值对
map1.delete("name")       // delete(key)   通过键 删除键值对
console.log(map1.get("k3"))//v3     通过键得到
console.log(map1.has(22))//true    通过键判断
map1.clear()            //清空
console.log(map1)//Map {}
```

### for of 循环

可以遍历  数组，伪数组，字符串，Set，Map

```js
let set1=new Set([1,2,3,4])
for (let i of set1) {
    console.log(i)
}

```

## 三,ES7

### 指数运算符 

```js
console.log(3**3)//27   通过**求幂运算
```

### Array扩展

```js
let arr=[1,2,3]
console.log(arr.includes(2))//true   通过数组对象的includes 判断包含
```



