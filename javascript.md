# js概述

## 一，概述

javascript是由网景公司开发的一种语言，和java没用关系,解释型语言

## 二，helloworld

可以使用`<script>`标签,中写js代码,由上往下依次执行,结尾可以没有分号，但是推荐写分号,因为浏览器会自动添加分号，但是总会消耗资源，有时还会加错分号，所以**推荐自己写分号**

```html
    <script>
        //向body标签中添加元素
        document.write("<h1>页面元素</h1>");
        //在控制台输出
        console.log("控制台");
        //弹出警告框
        alert("弹出框");
    </script>
```

也可以在标签中绑定事件

```html
<button onclick="alert('点击事件');">按钮1</button>
```

也可以引用外部的js文件

在js文件夹下的`index.js`如下

```javascript
console.log("外部文件");
```

在页面中引入，现代浏览器的默认`type="text/javascript"`所以**不推荐**写，因为属性值写错了还可能不能执行js代码，由外部引入的js文件可能会存在缓存，浏览器不一定会拿到最新的外部js文件

```html
<script src="js/index.js"></script>
```

## 三，变量

我们直接打印一个a变量

```javascript
console.log(a);
```

会出现`Uncaught ReferenceError: a is not defined`错误，表示a没有被定义

```javascript
var a;
console.log(a);
```

使用`var`关键字声明一个变量，后面跟变量名，此时打印a的结果为`undefined`,

```javascript
var a;
a=123;
var b=234;
console.log(a);
```

使用`=`等号进行赋值，此时打印就是123，

**标识符**

1,变量名可以包含字母，数字，下划线`_`，美元符号`$`

```javascript
var $_a1=123;//正确
```

2,变量名可以包含数字但是不能以数字开头,会抛出Uncaught SyntaxError: Invalid or unexpected token,表示非法符号

```javascript
var 1a=123;//错误
```

3,变量名不能是关键字和保留字，如new，if，break等,Uncaught SyntaxError: Unexpected token if

```javascript
var new;//错误
```

4,一般使用驼峰式命名,js底层采用utf8编码，虽然可以用中文但是别用

```javascript
var studentName;//推荐
```

## 四，数据类型

js中包括六种数据类型，string 字符串,number 数值,null 空值,boolean 布尔,undefined 未定义,object 对象,

object属于**引用数据类型**，其他五种属于**基本数据类型**

### string

使用双引号或者单引号声明,可以嵌套使用

```javascript
var sr="张三:'你好'";
```

也可以使用反斜杠`\`来转义,`\n`表示换行,`\t`相当于tab，`\\`表示`\`,

```javascript
var str="\n张\t/\\三:\"你好\"";
console.log(str);
```

### number

在js中所有的数值都是number类型，包括整数和浮点数，

```javascript
var a=123;
console.log(typeof a);//使用typeof 变量  来判断一个变量的数据类型

console.log(Number.MAX_VALUE);//1.7976931348623157e+308,表示js中的最大数值
console.log(Number.MIN_VALUE);//5e-324,表示js的零以上的最小数值,类似于0.000...22;

var b=Number.MAX_VALUE*Number.MAX_VALUE;//假如有数值超过了js所支持的最大值
console.log(b);//则输出  Infinity  ,表示无穷

var c=Infinity;//Infinity也可以直接作为值,属于number类型

var d="aa"*"bb";//如果强行把非数值类型的变量做运算
console.log(d);//则返回NaN,表示 Not a Number ,非数字

var e=NaN;//NaN也是一个常量
console.log(typeof e);//属于number类型

console.log(123+455);//整数的运算在js中基本能保证精确
console.log(0.1+0.2);//-->0.30000000000000004,小数不能保证精确，涉及钱的拿到服务器处理
```

### boolean

布尔值表示真true，假false，用于逻辑判断

```javascript
var b1=true;
console.log(typeof b1);//属于boolean
```

### null

```javascript
var a=null;//用null专门表示一个空值
console.log(typeof a);//属于object
```

### undefined

```javascript
var a;//当声明一个变量却不赋值时
console.log(a);//输出undefined,表示未定义
console.log(typeof a);//属于undefined
```

### object

属于引用数据类型，以上五种属于基本数据类型

## 五，类型转换

### string

boolean和number类型转换为string类型可以调用toString（）方法

```javascript
var a=123;//声明number类型
a=a.toString();//调用toString方法并重新赋值给a
console.log(typeof a);//string类型
console.log(a);//值为123
```

null和undefined没有toString方法,会出现TypeError: Cannot read property 'toString' of null报错

可以使用String()方法，作为参数传入

```javascript
var a=null;
a=String(a);//此时a为string类型的"null"
```

快速转使用  变量+""

### number

使用Number()函数

```javascript
var a="123";
a=Number(a);//调用Number（）函数
console.log(typeof a);//此时a为number类型

var a="aerw";
a=Number(a);
console.log(a);//转换失败的结果为NaN

var a="  ";
a=Number(a);
console.log(a);//如果是""或者"      ",会转为0。

var a=true;
a=Number(a);
console.log(a);//布尔类型，true会转为1，false为0,

//null会转0,undefined转为NaN
```

parseInt()方法可以从左往右读取有效数字，直到无效，解析

```javascript
var a="123px";//包含数字和字母
a=parseInt(a);//解析
console.log(typeof a);//number类型
console.log(a);//123
```

parseFloat()可以读取小数,转换失败返回NaN,

```javascript
var a="123.22px";
a=parseFloat(a);
console.log(typeof a);
console.log(a);//123.22
```

快速转使用   +变量;

### boolean

使用Boolean()函数,

​	数字：除了0和NaN其他全是true

​	字符串：除了空串，其他全是true

​	null和undefined会转为false

快速转使用   !!变量

## 六，其他进制

```javascript
var a=0xff;//16进制的数字采用0加字母x作为前缀
console.log(a);//255
var b=070;//8进制的数字采用0作为前缀
console.log(b);//56
var c=0b10;//2进制的数字采用0b作为前缀，ie不支持
console.log(c);//2

var a="070";//当070作为字符串解析的时候，高级浏览器会按10进制，低版本的ie按8进制
a=parseInt(a);
console.log(a);//chrome,firefox等返回70，ie6以下返回56

var a="070";
a=parseInt(a,10);//可以通过parseInt的第二个参数指定进制来解析
console.log(a);
```

## 七，运算符

### 算数运算符 +-*/%

```javascript
//数字做加法运算时，会把其他数转换为number类型，string除外
console.log(12+34);//46
console.log(1+true);//2  true会转为1
console.log(true+false);//1  true转为1，false转为0
console.log(1+null);//1   null转为0
console.log(1+NaN);//NaN   NaN怎么运算都是NaN
console.log("123"+"456");//"123456"   字符串与其他类型做加法的时候，会进行拼接
console.log(123+"456");//"123456"
console.log("123"+456);//"123456"
console.log(1+2+"3");//33   多个值进行计算时，会从左往右一步步算，这里先算数字运算1+2等于3，3+"3"="33"
console.log("1"+2+3);//123  先算字符串拼接"1"+2="12",再算"12"+3="123"一次拼接
var c=123+"";//利用特性，可以直接把一个变量加上空字符串，转换为string类型
console.log(typeof c);//string

console.log(100-true);//99   做减法运算时其他类型会转为number，包括string,这里true转为1
console.log(100-"1");//99
console.log("100"-2);//98

console.log("2"*"3");//6   乘法同上
console.log(true*"2");//2
console.log(2*null);//0

console.log("6"/"2");//3    除法同上
console.log("10"/3);//3.33333335

console.log(9%4);//1   取模运算，取余数，9除以4得2余1，所以余数为1
console.log(9%10);//9   除数大于被除数时，余数为被除数本身
```

### 一元运算符+-

```javascript
var a="10";
a=+a;        //使用加号，相当于取正数，会把其他类型转为number
console.log(a);//10
console.log(typeof a);//number

var b=true;
b=-b;       //减号就是取负数
console.log(b);//-1
console.log(typeof b);//number

var c=1+ +"2"+3;//这里使用+,把字符串"2"转为number的2，也就是三个number做运算,两个加号之间空格
console.log(c);//6
var d=1+ "2"+3;//这里没有使用+号，所以做的是字符串的拼接
console.log(d);//123
```

### 自增和自减++ --

```javascript
var a=1;
a++;   //a++相当于在a的基础上加1，并覆盖原变量的值，相当于a=a+1;
console.log(a);//2

var b=1;
console.log(b++);//1   b++会先返回原值，在对原变量做自增,此时b的值为2，b的原值1作为返回值返回
console.log(++b);//3   ++b会先做自增，再返回自增后的值,此时b的值为3，自增后的值3返回,原值2不管
console.log(b);//3

var c=1;//这里c++的返回值为原值1，此时c为2,++c的返回值为3，此时c为3，再加上此时的c=3，再加上取了正数的1
c=c++ + ++c +c+ +"1";
//1   +  3  +3   +1
console.log(c);//8

var d=3;//自减同理
console.log(--d);//2
```

### 逻辑运算符

js提供三种逻辑运算符，**与，或，非**

#### 非 ！

```javascript
var a=true;
console.log(!a);//false  非运算可以取反
console.log(a);//true 但是不会改变原值

var b=1;
console.log(!b);// false非运算会对非布尔类型，先转为布尔类型再取反
console.log(!!b);//true  连续两次非运算可以快速把非布尔类型的值转为布尔类型
```

#### 与 &&

```javascript
var r=true&&false;//使用两个&号可以对两个布尔类型取值，两边都是true才显示true
console.log(r);//false

var a=false&&console.log("短路则不显示");//js的与属于短路与，只要第一个为false，则不再进行后面的运算
var b=true&&console.log("不短路则显示");
```

#### 或 ||

```javascript
var r=true||false;//使用双|号进行或运算，只要存在true则返回true
console.log(r);

var a=false||console.log("不短路则显示");//同样属于短路或
var b=true||console.log("短路则不显示");//第一个值为true则不进行后面的运算
```

#### 非布尔类型与或运算

会返回最近一次运算的原值,短路原则

```javascript
var r1=1&&2;//先运行1，转为true，再运算第二个，最近一次运算为第二个，直接返第二个值
console.log(r1);//2

var r2=0&&NaN;//先运算0转为false，根据短路原则，不运算后面的，则返回第一个值
console.log(r2);//0

var r3=1||2;//先运算1转为true，根据短路，不运算后面的，直接返回第一个值
console.log(r3);//1

var r4=NaN||0;//先运算NaN转为false，再运算后面的，无论后面是什么，返回第二个
console.log(r4);//0
```

### 赋值运算符+= 

```javascript
var a=5;//使用=赋值
a+=2;//相当于a=a+2
console.log(a);//7
a-=2;//相当于a=a-2
console.log(a);//5
a*=2;//相当于a=a*2
console.log(a);//10
a/=2;//相当于a=a/2
console.log(a);//5
a%=2;//相当于a=a%2;
console.log(a);//1
```

### 关系运算符 ><

```javascript
console.log(3<4);//true 数字正常逻辑
console.log(1>=true);//true 对于非数字会先转为数字，这里true转为1
console.log(1>NaN);//false  任何和NaN比较都是false
console.log("a"<="b");//true  两边都是字符串时，不做转换，字符串会依次按位比较unicode码
```

### Unicode码

可以参考[unicode大全](https://blog.csdn.net/hherima/article/details/9045861)

```javascript
//使用\u四位编码
console.log("\u2620")//☠,海盗头
console.log("\u2622")//☢,核武器
console.log("\u2764")//❤.爱心
```

在html中需要转为10进制，unicode本身为8进制

```html
<h1 style="font-size: 100px">&#9760;</h1><!--使用&#十进制编码;-->
```

### 相等运算符==

```javascript
console.log(1==2);//false  使用双等号，判断两个值是否相等
console.log("1"==true);//true  大多数情况会转为number，这里是1==1
console.log(undefined==null);//true  undefined衍生于null
console.log(null==0);//false   null不会转型
console.log(NaN==NaN);//false  NaN与谁都不等，包括他自己
console.log(isNaN(NaN));//true  使用isNaN()来判断NaN

console.log(1!="1");//false  不等于

console.log("123"==123);//true  双等号会做类型转换
console.log("123"===123);//false   全等于不做类型转换

console.log("123"!=123);//false
console.log("123"!==123);//true   全不等类似
```

### 三元运算符

条件表达式`?`语句1`:`语句2，根据条件表达式判断，true执行第一个语句并返回结果

```javascript
1>2?console.log("1大于2"):console.log("1小于2");
```

## 八，流程控制

### 代码块  {...}

```javascript
{//使用中括号来代表代码块，后面不用加分号，只有分组作用
    var a=1;
    console.log("hello");
}

{
    console.log(a);//变量的作用域不受代码块影响
}
```

### 条件判断语句 if

语法:      if(条件判断语句)     语句；

```javascript
if (false)
    console.log("true");//if判断只能控制紧随其后的一条语句,通常都会用代码块
console.log("false");

var a=prompt("请输入一个数字");//prompt函数可以弹出一个输入框，输入的值作为string返回
a=+a;//强转为number
if(a<10){//使用代码块来控制多条语句,条件成立会执行代码块中 
    console.log("a小于10");
    var c=1;//如果代码块没用执行 c为undefined
}else if(a>10&&a<15){//使用else if做第二次判断
    console.log("a小于15大于10");
    console.log(c);//undefined  
}else{//else只能有一个存在于最后
    console.log("a大于15");
}
```

### 条件分支语句 switch

```javascript
var a = 2;

switch (a) {  //括号里是表达式
    case 1:   //case后面跟表达式，冒号后面为语句
        console.log("一");
        break;
    case 2://会对比两个表达式做全等于比较，为true则进入case，并依次执行后面的case
        console.log("二");
        break;//使用case跳出switch
    default://都为false则执行default
        console.log("不是一和二");
        break;
}
```

### 循环语句

#### while

```javascript
var i=1;
while (i<10){  //括号中的表达式为true时，则不断执行代码块中的内容，直到为false
    console.log(i);//while 先判断再执行
    i++;
}//一定要设置循环边界，不然会造成浏览器崩溃，比如while(true){...}
```

#### do while

```javascript
var a=11;
do {//会先执行一次代码块
 console.log("a:"+a++);
}while (a<10);//条件满足再执行
//do while 是先执行再判断,可以保证最少执行一次
```

#### for

```javascript
for (var i=1;i<10;i++){//括号里为(初始化表达式;条件表达式;更新表达式）
    //先执行初始化，声明变量
    //进行判断，执行代码块
    //执行更新表达式，继续判断
    
    console.log(i);
}

var i=1;//可以声明变量在外面
for (;i<10;){
    console.log(i++);//更新在里面
}

for(;;){//全都不写，只写两个分号则为死循环
    console.log(1);
}

//-----------------九九乘法表
var str="";
for(var i=1;i<10;i++){
    for(var j=1;j<i+1;j++){
       str+=j+"x"+i+"="+i*j+" ";
       if(i===j){
           str+="\n";
       }
    }
}
console.log(str);
//可以使用break终止循环，默认终止最近的for循环，可以为循环指定标签，终止指定标签的循环
outer:
for (var i=1;i<9;i++){
    for (var j=1;j<9;j++){
        console.log("i:"+i+",j:"+j);
        break outer;
    }
}
//continue可以跳过当前循环，执行下次循环
console.time("a");//开启一个名为a的计时器
for(var i=1;i<9;i++){
    if(i==3){
        continue;
    }
    console.log(i);
}
console.timeEnd("a");//a: 2.663ms  关闭计时器a，并输出时间
```

## 九，对象

### 创建对象

```javascript
var obj=new Object();//创建对象 使用new关键字，调动构造方法
console.log(obj);//{}   空对象
```

### 设置属性

```javascript
obj.name="张三";//使用对象.属性=值; 添加属性
obj.age=12;
console.log(obj);//{ name: '张三', age: 12 }

var a = 1;
obj[a] = "qwer";//可以使用方括号去设置属性，里面放属性名，可以是变量
console.log(obj);//{ '1': 'qwer', name: '李四', age: 12 }
console.log(obj[a]);//qwer
```

### 修改属性

```javascript
obj.name="李四";//修改属性，直接覆盖
console.log(obj.name);//"李四",使用对象.属性取值
console.log(obj.gender);//undefined,不存在的属性为undefined
```

### 属性类型

```javascript
var son = new Object();
son.name = "王五";
obj.son = son;//属性也可以是对象
console.log(obj);//{ '1': 'qwer', name: '李四', age: 12, son: { name: '王五' } }
console.log(obj.son.name);//王五  依次通过.来调用对象中的对象的属性
console.log("name" in obj);//可以通过关键字in 来判断一个对象中是否包含该属性
```

### 快速创建

```javascript
var obj3 = {//可以直接用{}来声明对象，推荐,并可以在创建的时候设置属性
    		//{属性名1:属性值1,属性名2:属性值2}
    name: "张三",//属性名可以加引号，可以不加，一般不加，除非属性名有特殊字符
    age: 30,
    son: {
        name: "小张"
    }//最后一个属性后面最好不要加逗号
};
```

## 十，函数

### 定义函数

函数也是个对象,函数可以封装代码

```javascript
var fun1=new Function("console.log('hello func')");//使用构造函数来创建函数对象，参数为代码
console.log(fun1);//[Function: anonymous]
console.log(typeof fun1);//function
fun1();//调用函数
```

但是一般会用声明式创建函数

```javascript
function fun1() {
    console.log("hello func");
}
//function 函数名([参数1,参数2,...]){代码块...}
fun1();//调用

//或者匿名函数赋值给变量
var fun2=function () {
    console.log("匿名函数");
};
fun2();
```

### 参数

```javascript
function sum(a,b) {     //定义形参，没用赋值，相当于var a;
    console.log("a:"+a);
    console.log("b:"+b);
    console.log(a+b);
}

sum(123,333);//456  传递实参   赋值给对于位置的形参
sum("123",true);//123true    js不会检查数据类型
sum(123);//NaN             js不会检查参数的数量，参数不够，则为undefined
sum(123,123,true,null,"hello");//246   多的参数不会被赋值


//-------------------------------------------------------
function info(obj) {//参数也可以是对象，通过.的方式取值
    console.log("name="+obj.name+",age="+obj.age);
}

function tt(a,b) {
    console.log(typeof a);
    a(b);//参数也可以是函数,使用()调用 ,如果a不是函数，则TypeError: a is not a function
}

var swk={//创建一个对象
    name:"孙悟空",
    age:12
};

tt(info,swk);//这里传一个info函数对象，和swk对象
```

### 返回值

```javascript
function sum(a,b) {
    var c=a+b;
    return c;    //使用return关键字可以返回任意类型的值，如果只写return;或者没有return，则返回undefined
}

var r=sum(1,2);//函数返回什么，r就是什么
console.log(r);//3

//同上，返回值也可以是函数和对象
```

### 立即执行函数

```javascript
(function (a) { 		 //直接写一个函数，如果不起名字也不赋值变量，会报语法错误
    console.log(a);		 //使用括号包起不会报错
})(123);				//紧跟一个括号可以立即调用这个函数
```

### 函数作为属性

```javascript
var obj={};//创建一个对象
obj.say=function () {//可以把函数赋值给对象的属性
    console.log("hello");
};
obj.say();//调用对象的方法

var obj2={//也可以声明时，直接声明函数作为属性
    say2:function () {
        console.log("hello2");
    },
    name:"zs"
};
obj2.say2();//调用对象的方法
```

### 枚举属性

```javascript
var obj={//创建一个对象
    name:"张三",
    age:12
};

for (var index in obj){//使用for in循环可以枚举出一个对象的所有属性，
    				 //每次循环都会把属性名作为string赋值给index
    console.log(index); //属性名
    console.log(obj[index]);//对于属性名是变量，采用方括号来取值
}
```

### 作用域

#### 全局作用域

```javascript
var c=1;
console.log(window.c);//所有直接写的变量和函数，都会作为window对象的属性，
					//window对象会在页面打开时创建,关闭时销毁
b=2;//如果不使用var关键字，则相当于window.b=2,给window对象设置属性
function aa() {
    console.log(c);//全局变量可以在任意地方访问
}

aa();
```

#### 函数作用域

```javascript
var a="全局的a";   //声明一个全局变量
function fun1() {
    console.log(a);//undifined    这里输出undefined,但是没抱错是因为变量的声明提前,而不会去上级寻找
    var a="局部的a";     //声明提前会提前执行var a;但是不赋值,所以上面输出的是函数作用域中没有赋值的a
    fun2();     //这里能直接调用是函数的声明提前,直接声明的函数会在之前就创建好
    function fun2() {
        console.log(a);//局部的a     由于fun2中没有a，就近原则，去上级寻找
        console.log(window.a);//全局的a   若想访问全局，通过window去调用
    }
}
fun1();
console.log(a);//全局的a  
```

### 上下文对象 this

函数在调用的时，会默认传入一个this对象，this代表**调用当前函数的当前对象**

```javascript
var name="全局属性";
function aa() {
    console.log(this);      //Window{...}     {name:"张三",..}
    console.log(this.name); //全局属性          张三
}
aa();//直接调用的话，this就是window

var obj1={
    name:"张三",
    say:aa       //现在把aa方法作为obj的say属性
};
obj1.say(); //此时this就是obj1
```

### 构造函数

```javascript
function Person(name,age) {//立即创建一个对象，赋值给构造方法的this
    this.age=age;           //执行方法的代码
    this.name=name;
    this.say=function () {    //属性可以直接是方法
        console.log(this.name);
    };										
}
							//构造方法一般首字母大写，也可以称为类
var p1=new Person("张三",12);//通过new关键字调用
p1.say();
```

### 原型对象 prototype

```javascript
//把方法直接作为属性，每new一次对象，就会创建一个匿名函数，影响性能
function Person(name,age) {
    this.age=age;
    this.name=name;
}
//把公共的函数存放到原型对象中，直接用方法对象.prototype即可得到原型对象
Person.prototype.say=function () {
    console.log(this.name);
};
//当一个对象调用方法时，会从自己本身找，找不到回去原型对象中找
var p1=new Person("张三",12);
p1.say();
//方法本身的prototype属性和实例的__proto__属性都会指向同一个内存地址的原型对象
console.log(Person.prototype===p1.__proto__);//true   

console.log("say" in p1);//true  用in查找属性  会一路从本身查找到根原型
console.log(p1.hasOwnProperty("say"));//false  使用hasOwnProperty方法只会查找属于自己的
console.log(p1.__proto__.hasOwnProperty("say"));//true  此时say方法在原型中
console.log(p1.__proto__.hasOwnProperty("hasOwnProperty"));//false  但是hasOwnProperty不再原型中
console.log(p1.__proto__.__proto__.hasOwnProperty("hasOwnProperty"));//true  在根原型Object的原型中
console.log(p1.__proto__.__proto__.__proto__);//null    根原型的__proto__属性为null
```

### call和apply

```javascript
function fun1(a,b) {
    console.log(this)
    console.log(a)
    console.log(b)
}

fun1(1,2) //直接调用，this在浏览器下会是window对象，在node中是global对象
let zs={name:"zs"}
//当函数对象调用apply,call方法时，可以传入一个参数，这个参数会被作为函数中的this
//{ name: 'zs' }  1  2
fun1.apply(zs,[1,2])  //apply需要把其他参数封装到一个list里
fun1.call(zs,1,2)     //call 直接把参数跟在第一个参数后面即可
```

### arguments

```javascript
function fun1() {
    //当调用函数时，默认有个参数arguments ,无论写不写形参，都会把实参封装到这个对象中
    console.log(arguments) //{ '0': 'aaa', '1': 123 }
    console.log(arguments.length) //2  参数的数量 
    console.log(Array.isArray(arguments)) //false  不是数组，但是可以通过索引操作属性
    console.log(arguments[1]) //123  通过索引取值  
    console.log(arguments.callee) //[Function: fun1]  callee属性就是当前函数
    console.log(arguments.callee==fun1) //true  
}

fun1("aaa",123)
```



## 十一，数组

### 定义数组

```javascript
var arr=new Array();      //通过构造方法创建数组
arr[0]="张三";			//给索引0设置元素
arr[1]="李四";
console.log(arr);//[ '张三', '李四' ]
console.log(arr.length);//2      通过length属性查看数组的长度
arr[arr.length]="王五";       //这样可以总能给元素添加到数组的最后，因为索引从0开始，比长度少1
console.log(arr);//[ '张三', '李四', '王五' ]
arr.length=0;   //可以直接通过设置长度来删除元素，设置为0清空数组
console.log(arr);//[]
```

### 元素类型

```javascript
//数组可以直接用[] 声明，并在声明时设置元素
var arr=["张三",12,true,null,{name:"李四"},[1,2,3],function () {console.log("a")}];
//元素可以是任意类型
console.log(arr[1]);//12   
console.log(arr[4].name);//李四  对象
console.log(arr[5][0]);//1   相当于二维数组
arr[6]();//a      也可以是函数
```

### 常用方法

```javascript
var arr=[1,2,3];

arr.push(4); //添加元素到末尾,并返回长度
console.log(arr);//[ 1, 2, 3, 4 ]
arr.pop();  //删除并返回最后一个元素
arr.unshift("aa");   //添加元素至开头，并返回长度
console.log(arr);//[ 'aa', 1, 2, 3 ]
arr.shift();    //删除并返回第一个元素
console.log(arr);//[ 1, 2, 3 ]
```

### 遍历

```javascript
var arr=[1,2,3];
//传统方式，for循环遍历
for(var i=0;i<arr.length;i++){
    console.log(arr[i]);
}
//foreach 遍历
for (var i in arr){
    console.log(arr[i]);
}
//数组的forEach中传回调函数 ,回调函数会依次传，元素，索引，原数组
arr.forEach(function (element,index,array) {    			
    console.log("元素:"+element+",索引:"+index+",原数组:"+array);
});

```



## 十二，常用类

### Date

```javascript
var date1=new Date()  //得到执行这一行代码的当前时间
console.log(date1)
//  chrome69,ie10 中显示 Thu Oct 04 2018 13:38:33 GMT+0800 (中国标准时间)
//  firefox62 ,node9.8中显示  2018-10-04T05:36:08.050Z

var date2=new Date("12/03/2016 13:56:33:22") //得到一个指定的时间
console.log(date2)//Sat Dec 03 2016 13:56:33 GMT+0800 (中国标准时间)
console.log(date2.getDate()) //3 返回这个月中的第几天，number
console.log(date2.getDay())  //6 返回这周中的第几天，0为周日，1为周一 ,number
console.log(date2.getFullYear())//2016 返回四位年份,number
console.log(date2.getMonth())  //11 返回月数，0为1月，11为12月
console.log(date2.getHours())  //13 时
console.log(date2.getMinutes())//56 分
console.log(date2.getSeconds())//33 秒
console.log(date2.getMilliseconds()) //22  毫秒
console.log(date2.getTime()) //1480744593000 得到时间戳，从1970年1月1日0：0：0到现在的毫秒数
```

### Math

```javascript
console.log(Math.PI) //3.141592653589793 圆周率
console.log(Math.abs(-3))//3  返回绝对值
console.log(Math.ceil(-2.33))//-2 向上取整
console.log(Math.floor(-2.33))//-3 向下取整
console.log(Math.round(3.4))//3  四舍五入
console.log(Math.max(1,2,3,4,5)) //5 最大值
console.log(Math.min(1,2,3,4,5))//1  最小值
console.log(Math.pow(3,4))//81  x的y次幂
console.log(Math.sqrt(64))//8  开根号
console.log(Math.random())//0.5161926002350568  生成0到1之间的随机数

function myrandom(x,y) { //生成x-y范围的随机数公式.包括x,y
    return Math.round(Math.random()*(y-x)+x)
}
console.log(myrandom(1000,9999))//四位验证码
```

### String

```javascript
var str1="abcdefg"
console.log(str1.length)//7 字符串的长度
console.log(str1[2])//c   同下
console.log(str1.charAt(2))//得到对应索引的字符
console.log(str1.charCodeAt(0))//97  得到对应位置字符的unicode编码
console.log(String.fromCharCode(20013))//中  从unicode编码转换为字符
console.log(str1.includes("bc",1))//true  searchstring,[position],从指定位置判断是否包含字符串
console.log(str1.indexOf("cd",1))//2  得到第一次出现该字符串的首字符索引,没有返回-1，可以指定起点
console.log(str1.lastIndexOf("e")) //4 从后面往前找
console.log(str1.slice(2,-1))//cdef 和substring一样，但是可以接受负数,-1为最后一字符,-2倒数第二
console.log(str1.substring(2,4))//cd  start,[end]截取，截头不截尾，不影响原字符串,没用end，截到尾
console.log(str1.substr(2,3))//cde  index,[length]从指定位置截取多少位字符
console.log(str1.split("d"))//[ 'abc', 'efg' ]  拆分,拿掉参数的字符串，剩下的部分作为数组,传"",则拆全部
console.log(str1.toUpperCase())//ABCDEFG  转大写
console.log(str1.toLowerCase())// 转小写
```

## 十三，正则表达式

### 创建

```javascript
var reg1=new RegExp("a","i")// 正则表达式,[匹配模式]  匹配模式i 为忽略大小写 g为全局匹配
console.log(reg1)// /a/i   表示包含a
console.log(reg1.test("adfs")) //true 使用test方法测试字符串,默认区分大小写，返回boolean
```

### 快速创建

直接创建方便，但是构造函数可以把变量作为参数，更灵活

```javascript
var reg1=/a/i  //快速创建  /正则表达式/匹配模式
console.log(reg1.test("A"))
```

### 或 

使用竖线`|`,表示或

```javascript
var reg1=/a|b/i  //使用竖线 ，表示或 
console.log(reg1.test("berw"))
```

使用中括号`[]`

```javascript
var reg1=/[ab]/i  //使用中括号[],里面的字符为或的关系，[ab]相当于a|b
console.log(reg1.test("berw"))//true
```

指定范围

```javascript
var reg1=/[d-g]/ //用-号指定范围 a-z为所有小写字母,A-Z为所有大写,A-z所有字母忽略大小写 0-9为任意数字
console.log(reg1.test("e"))//true
```

### 排除

```javascript
var reg1=/[^ab]/ //用^表示除了
console.log(reg1.test("ababaaa"))//false
console.log(reg1.test("abc"))//true  除了ab还有c
```

### 量词

```javascript
var reg1=/(ab){3}/  //相当于/ababab/
//使用{n}设置正好出现的次数，但是只对上一个字符有效，要想匹配多个字符使用()包起
console.log(reg1.test("abababfsad"))//true

var reg1=/ab{1,3}c/  //{m,n} 为出现m到n次
console.log(reg1.test("abbc"))//true

var reg1=/ab{3,}c/  //{m,} 为出现m次以上
console.log(reg1.test("abbbbbc"))//true

var reg1=/ab+c/  // 被+号修饰的字符，为至少出现一次，相当于{1,}
console.log(reg1.test("abbbbbc"))//true  至少由一个b

var reg1=/ab*c/  // 被*号修饰的字符，表示0个或多个，相当于{0,}
console.log(reg1.test("ac"))//true

var reg1=/ab?c/  // 被?号修饰的字符，表示0个或1个，相当于{0,1}
console.log(reg1.test("abbc"))//false

```

### 头尾

```javascript
var reg1=/^a/  // 由^开头后面跟的字符为，已这串字符开头
console.log(reg1.test("abcwer"))//true

var reg1=/a$/  // 由$开头后面跟的字符为，已这串字符结尾
console.log(reg1.test("werqta"))//true
```

### 转义

```javascript
var reg1=/\./
// 正则中.会匹配所有字符，使用反斜杠\转义
console.log(reg1.test("awe.f"))
```

### 元字符

```
\w	查找单词字符。
\W	查找非单词字符。
\d	查找数字。
\D	查找非数字字符。
\s	查找空白字符。
\S	查找非空白字符。
\b	匹配单词边界。
\B	匹配非单词边界。
```

### 常用正则

```javascript
var reg1=/^1[3-9][0-9]{9}$/
// 手机号正则 以1开头，第二位为3-9 ,总长度为11位
console.log(reg1.test("13656567878"))

var reg1=/^[\w\.-]+@[\w-]+(\.\w{2,6}){1,2}$/
//邮箱正则 
console.log(reg1.test("pl-111@qq.com.cn"))
```

### 字符串相关方法

```javascript
var str1="1b2c3d4e5f6"

console.log(str1.split(/[A-z]/))
//[ '1', '2', '3', '4', '5', '6' ] split 默认全局拆分,可以接受正则表达式
console.log(str1.search(/[cd]/))
//3  和indexof差不多，但是可以接受正则表达式
console.log(str1.match(/[A-z]/g))  
//[ 'b', 'c', 'd', 'e', 'f' ]  默认提取第一个符合的字符串,作为数组返回
console.log(str1.replace(/[a-z]/g,"@"))
//1@2@3@4@5@6  要被替换的字符串,[新的字符串],可以接受正则,默认替换第一个

```

## 十四,DOM

dom 为 文档对象模型 (document object model),用于操作页面元素，不同浏览器对于显示结果不同

```html
<button id="btn1">按钮1</button>
<script>
    console.log(document) //[object HTMLDocument] 在页面中已经有了document对象
    var btn1=document.getElementById("btn1") //通过id得到元素
    console.log(btn1) //<button id="btn1">按钮1</button>
    console.log(btn1.innerHTML)//按钮1  得到对象的属性
</script>
```

### 事件

指网页发生交互的瞬间

```html
<button id="btn1" onclick="alert('点击事件1')">按钮1</button>
```

上面通过`onclick`属性绑定了一个事件，事件会执行里面的js代码,但是这样会耦合,建议把结构和行为分开

```html
    <button id="btn1">按钮1</button>
    <script>
        var btn1=document.getElementById("btn1") //得到按钮对象
        btn1.onclick=function () {   //设置属性绑定为一个匿名函数
            alert("通过js绑定事件")
        }
    </script>
```

### 页面加载过程

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script>
        /*
        * 由于页面是由上而下逐行加载执行，此时页面body还没开始加载，get到的对象是null
        * 会报错Uncaught TypeError: Cannot set property 'onclick' of null
        * */
        window.onload=function () {
            var btn1=document.getElementById("btn1")
            btn1.onclick=function () {
                alert("通过js绑定事件")
            }
        }
        //此时需要把代码写在window的加载事件中，当页面加载完成时会调用里面的方法
        //不过先绑定事件多少还是会影响页面的加载速度，所以建议js写在body快要结束前
    </script>
</head>
<body>
    <button id="btn1">按钮1</button>
</body>
</html>

```

### 元素查询

元素element一般指标签，节点node包括文本节点，标签节点，属性节点

#### id

通过**id**得到节点 ,通过`document`对象调用`getElementById`方法，参数为id值,只能获取第一个id为此的对象，否则返回null

```javascript
var city1=document.getElementById("city1")
```

#### tagName

通过**标签名**得到节点集合 ,`getElementsByTagName`方法，注意是复数

```html
<ul id="city1">
    <li>北京</li>
    <li>上海</li>
    <li>深圳</li>
</ul>
<script>
    var lis=document.getElementsByTagName("li") //得到一个类数组对象
    console.log(lis) //HTMLCollection(3) [li, li, li]
    for (var i=0;i<lis.length;i++){  //遍历得到每个元素
        console.log(lis[i]) //<li>北京</li>
        console.log(lis[i].innerHTML) //北京
    }
</script>
```

#### name

通过**name**得到节点集合,`getElementsByName`方法,参数为name值

```html
男:<input type="radio" name="gender" value="man"/>
女:<input type="radio" name="gender" value="woman"/>
<script>
    var ins=document.getElementsByName("gender")
    console.log(ins) //NodeList(2) [input, input]
    for (var i=0;i<ins.length;i++){
        console.log(ins[i]) //<input name="gender" value="man" type="radio">
        console.log(ins[i].innerHTML) //innerHTML是拿到元素中的html代码,</>自结束标签该值为空字符串
        console.log(ins[i].value)//man 得到属性
        console.log(ins[i].type)//radio 需要什么属性直接点
    }
</script>
```

#### className

通过**class**得到节点集合,`getElementsByClassName`方法,参数为样式名,得到包含此样式的元素,ie8不支持

```html
<p class="s1">aaaa</p>
<p class="s1">bbbb</p>
<script>
    var ps=document.getElementsByClassName("s1")
    for (var i=0;i<ps.length;i++){
        console.log(ps[i]) //<p class="s1">aaaa</p>
        console.log(ps[i].innerHTML) //aaaa
        console.log(ps[i].className) //s1 由于class是js的关键字，所以通过className属性来调用
    }
</script>
```

#### 切换图片练习

```html
<body>
    <p id="p1"></p>
    <img id="img1" style="width: 200px;height: 355px" src=""/>
    <button id="prev">上一张</button>
    <button id="next">下一张</button>
    <script>
        window.onload=function () {
            var imgurls=[ //花瓣网上偷几张图
                "http://img.hb.aicdn.com/908820a96840aba52650fa65d2eeab4af341eece14cf37-IiORaH_fw658",
                "http://img.hb.aicdn.com/fcd51b6f6622a09c4634b743b559dd7dc30f06c626f05-T01TqV_fw658",
                "http://img.hb.aicdn.com/d4546113aee853619ca2486610deb8481503466822afa-fSafcz_fw658",
                "http://img.hb.aicdn.com/7f9f1a1fc2f96de116ca1f9ed2b0083f34e792be1a534c-rqr4St_fw658",
                "http://img.hb.aicdn.com/6905feaceedcc187a8c2d39488892983ed5ab3f0192680-mYgiBO_fw658"
            ];
            var img1=document.getElementById("img1") //得到img对象
            var index4img=0 //设置一个索引来标记第几张,初始值为0表示第一张
            img1.src=imgurls[index4img] //设置初始值图片
            var p1=document.getElementById("p1") //提示信息标签
            p1.innerHTML="共有"+imgurls.length+"张，当前为第"+(index4img+1)+"张"

            //得到prev元素直接绑定点击事件
            document.getElementById("prev").onclick=function () {
                //如果到了第一张
                if(index4img==0){
                    index4img=imgurls.length-1 //则设置为最后一张
                }else {
                    index4img--  //否则进入上一张
                }
                img1.src=imgurls[index4img]
                p1.innerHTML="共有"+imgurls.length+"张，当前为第"+(index4img+1)+"张"
            }
            //同上
            document.getElementById("next").onclick=function () {
                if(index4img==(imgurls.length-1)){
                    index4img=0
                }else {
                    index4img++
                }
                img1.src=imgurls[index4img]
                p1.innerHTML="共有"+imgurls.length+"张，当前为第"+(index4img+1)+"张"
            }
        }
    </script>
</body>
```

#### child

```html
    <ul id="ul1">
        <li>北京</li>
        <li>上海</li>
        <li>深圳</li>
    </ul>
    <script>
        var ul1=document.getElementById("ul1")
        var lis=ul1.getElementsByTagName("li")  
        //getElementsByTagName 还可以被元素对象调用 从子元素查询
        for (var i = 0; i < lis.length; i++) {
            console.log(lis[i].innerHTML)
        }
    </script>
```

通过`childNodes`属性,拿到所有子节点，由于会拿到空白元素，且与ie8以下兼容性不好，不建议使用

```html
<ul id="ul1">
    <li>北京</li>
    <li>上海</li>
    <li>深圳</li>
</ul>
<script>
    var ul1=document.getElementById("ul1")
    var lis=ul1.childNodes  
    //childNodes属性会得到所有的子节点，而且包括标签直接的空白
    console.log(lis.length) //7  ie8以下显示4, 有四处空白和三个元素
    for (var i = 0; i < lis.length; i++) {
        console.log(lis[i]) //#text  <li>北京</li>  
    }
</script>
```

通过`children`属性，拿到子元素，建议使用

```html
<ul id="ul1">
    <li>北京</li>
    <li>上海</li>
    <li>深圳</li>
</ul>
<script>
    var ul1=document.getElementById("ul1")
    var lis=ul1.children
    //children属性会得到所有的子元素
    console.log(lis.length) //3 有三个标签元素
    for (var i = 0; i < lis.length; i++) {
        console.log(lis[i]) //  <li>北京</li>
    }
</script>
```

first  last

```html
<ul id="ul1">
    <li>北京</li>
    <li>上海</li>
    <li>深圳</li>
</ul>
<script>
    var ul1=document.getElementById("ul1")
    console.log(ul1.firstChild)//#text 拿到第一个子节点，如果有空会拿到空白节点
    console.log(ul1.firstElementChild)//<li>北京</li> 得到第一个子元素 ie8显示undefined
    console.log(ul1.lastChild)//#text
    console.log(ul1.lastElementChild)//<li>深圳</li>
</script>
```

#### parent 

```html
<ul>
    <li >北京</li>
    <li id="snh">上海</li>
    <li>深圳</li>
</ul>
<script>
    var snh=document.getElementById("snh")
    console.log(snh.parentNode)//<ul>...</ul>  得到父节点
    console.log(snh.parentElement)//<ul>...</ul>  得到父元素
    console.log(snh.previousSibling)//#text  得到上一个兄弟节点，会拿到空白
    console.log(snh.previousElementSibling)//<li >北京</li>  得到上一个兄弟元素 没有返回null
    console.log(snh.nextSibling)//#text 下一个节点
    console.log(snh.nextElementSibling)//<li>深圳</li> 下一个元素
</script>
```

#### 全选练习

```html
    全选or全不选:<input type="checkbox"  id="allrdo">
        <div>
            <input type="checkbox" name="hobby" value="basketball">篮球
            <input type="checkbox" name="hobby" value="football">足球
            <input type="checkbox" name="hobby" value="run">跑步
        </div>
    <script>
        function myClick(idv,fun){ //封装一个方法，通过id绑定事件
            document.getElementById(idv).onclick=fun
        }

        myClick("allrdo",function () {
            console.log(this) //调用事件回调函数的对象就是元素本身
            //<input type="checkbox"  id="allrdo">
            var hobs=document.getElementsByName("hobby") //得到复选框集合
            for (let i = 0; i < hobs.length; i++) {
                hobs[i].checked=this.checked  //通过checked属性设置是否选中
            }
        })
    </script>
```

#### 选择器

通过`querySelector`和`querySelectorAll`两个方法

```html
<div id="ww"></div>
<p class="pp">wrewre</p>
<script>
    console.log(document) //#document  整个文档
    console.log(document.body)//body标签
    console.log(document.documentElement)//html标签
    //选择器的用法  .class  tag   #id
    console.log(document.querySelector("#ww"))//选择器 只会返回第一个匹配的元素
    console.log(document.querySelectorAll(".pp"))//选择器 返回一个NodeList
</script>
```

### 增删改

通过`appendChild`,`insetBefore`,`removeChild`,`replaceChild`,方法增删改节点

```html
<div>
    <ul id="city">
        <li>广州</li>
        <li id="sz">深圳</li>
        <li>上海</li>
    </ul>

    <button id="add">添加</button>
    <button id="addbf">添加到子节点前</button>
    <button id="rep">替换子节点</button>
    <button id="del">删除子节点</button>
</div>
<script>
    function myClick(idv,fun) { //封装绑定点击事件的方法
        document.getElementById(idv).onclick=fun
    }

    function getBJ(){ //返回组装好的北京节点
        var li1=document.createElement("li") //<li></li>  创建li元素
        li1.innerHTML="北京"
        return li1
    }

    myClick("add",function () {
        document.querySelector("#city").appendChild(getBJ())//添加到ul下,默认添加到最后
    })

    myClick("addbf",function () {
        var sz=document.querySelector("#sz")//被参考的子节点
        sz.parentNode.insertBefore(getBJ(),sz)//父节点.insertBefore(新节点，参考节点)
    })

    myClick("rep",function () {
        var sz=document.querySelector("#sz")//被替换的子节点
        sz.parentNode.replaceChild(getBJ(),sz)//父节点.replaceChild(新节点，旧节点)
    })

    myClick("del",function () {
        var sz=document.querySelector("#sz")//被移除
        sz.parentNode.removeChild(sz) //移除子节点
    })
</script>
```















