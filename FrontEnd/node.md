# node

node是运行在服务器端的js,不包含dom和bom

## 一,node模块化

**commonjs规范**

由于以前javascript没有模块化的相关操作，js代码会加载的顺序依次执行，需要用到那个模块，则需要导入对应的js链接，模块之间的依赖不清楚,需要手动去判断，位置错了，会导致全部不能用

```html
<script src="../jquery.js"></script>
<script src="../bootstarp.js"></script> <!--bootstrap依赖于jquery-->
<script src="../bootstaptable.js"></script> <!--表格插件依赖于bootstrap-->
```

到了node中一个js文件就是一个模块

### 引入

通过require方法引入其他模块，相对路径必须以`.`或者`..`开头

```javascript
var md1=require("./module1") //代表导入的模块对象
```

执行一个js文件时，会先依次执行外部模块中的代码

### 暴露

模块是运行在一个独立的函数中，其他模块不能直接访问模块中的属性和方法

如果想让外部访问，需要赋值给`exports`对象的属性

编写`module1.js`文件

```javascript
console.log("its module1")

exports.arr=[1,2,3]
exports.fun1=function () {
    console.log("md1 中的方法")
}
```

同一目录下的`module2.js`

```javascript
var md1=require("./module1") //代表导入的模块对象,可以不写后缀

console.log("its module2")
console.log(md1) //{ arr: [ 1, 2, 3 ], fun1: [Function] }
console.log(md1.arr)//[ 1, 2, 3 ]
md1.fun1()//md1 中的方法
```

执行module2，会先执行md1的代码，再执行md2的代码

### 系统模块

导入自己定义的模块，使用路径，使用系统模块或者下载到环境中的模块直接指定模块名

```javascript
var fs=require("fs") //文件系统模块，可以操作系统文件

console.log(fs)
```

### 原理

当执行`console.log(arguments.callee+"")`时，可以看到以下代码

```javascript
function (exports, require, module, __filename, __dirname) {
console.log(arguments.callee+"")
}
```

node会在js的开头和结尾加两行代码，包装成一个方法来交给global对象来执行

传入exports,require等对象，

### 批量暴露

```javascript
console.log(exports===module.exports)//true
//exports本身是module的一个属性,也可module.exports.属性=值
module.exports={    //但是不能直接用exports={}来批量,这样会指向新的对象与module断开联系
    name:"zs",
    fun1:function () {
        console.log("module exports")
    }
}

```

## 二，npm包

一个包里面必须要拥有`package.json`来描述这个包

通过 npm init 来创建这个文件

```json
{
  "name": "nodetest",
  "version": "1.0.0",
  "dependencies": {
    
  }
}

```

node通过npm来管理包，相当于一个软件管家，自动安装，管理依赖，包发布

### 搜索包

通过npm s/search 包名

```bash
npm s math
```

### 安装包

`npm i/install 包名@版本号 --save  -g `  

   -g 会全局安装        --save 会安装并添加依赖到pkg.json

```bash
npm i math --save
```

此时`package.json`已经有依赖了,并且在当前文件下会有node_modules文件夹放其他包

```json
  "dependencies": {
    "math": "0.0.3"
  }
```

导入使用

```js
var math=require("math")

console.log(math.add(1,2))
```

### 移除包

`npm r/remove 包名`

```bash
npm r math
```

### 安装依赖

//只要有pkg.json,就可以通过下面的命令，安装所有依赖的包

```bash
npm install 
```

### 使用阿里镜像

```bash
npm install --registry=https://registry.npm.taobao.org --loglevel=silly
npm cache clean --force
```

## 三,fs模块

### 同步文件写入

```js
var fs=require("fs") //导入fs模块

var fd=fs.openSync("index.txt","w") //打开一个文件，没有则创建，返回一个标识

console.log(fd) //3  一个标识,用于标记打开的这个文件

fs.writeSync(fd,"how are you") //对指定标识的文件，写入内容

fs.closeSync(fd)   //关闭文件
```

