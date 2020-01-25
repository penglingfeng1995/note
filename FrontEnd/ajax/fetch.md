# 概述

fetch 是一个接口，用于异步访问 http ，用来替换原本的 xhr

所有版本的 ie 都不支持 fetch

# 使用

使用 fetch 函数传入一个 地址，会返回一个 promise 对象，用于处理响应，使用 响应的 json 函数又会返回一个 promise对象，继续调用 then 函数，处理 json 数据

```js
fetch("/student/student/selectStudent").then(res => res.json()).then(msg => {
	console.log(msg)
})
```

# post传参

## FormData

fetch 的第二个参数可以传入一个对象，可以包含参数和请求信息

指定 method，请求头，body指定参数

```js
fetch("/student/student/addStudent",{
    method:"post",
    headers:{"Content-Type":"application/x-www-form-urlencoded"},
    body:`studentName=${studentName}&studentAge=${studentAge}`,
}).then(res=>res.json()).then(msg=>{
    console.log(msg)
})
```

如果参数比较多，可以使用 `URLSearchParams` 对象携带参数，无需指定请求头

```js
let params = new URLSearchParams()
params.append("studentName",studentName)
params.append("studentAge",studentAge)
fetch("/student/student/addStudent",{
    method:"post",
    body:params,
}).then(res=>res.json()).then(msg=>{
    console.log(msg)
})
```

## JSON

传 json 数据时，需指定 content-type ，

```js
fetch("/student/student/addStudent",{
    method:"post",
    headers:{"Content-Type":"application/json"},
    body:JSON.stringify({studentName:"张三",studentAge:12})
}).then(res=>res.json()).then(msg=>{
    console.log(msg);
})
```

后端接收需要使用 `@RequestBody` 注解

```java
@ResponseBody
@RequestMapping("addStudent")
public Result addStudent(@RequestBody Student student){
```

## File



