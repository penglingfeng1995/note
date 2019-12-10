# 原生ajax

Ajax 即Asynchronous Javascript And XML ,异步的js和xml

可以在不刷新网页就可以与服务器交互，获取数据

## 1，创建xhr对象

```js
var xhr = new XMLHttpRequest()
```

直接new，在ie下需使用`ActiveXObject`

## 2，设置参数

```js
xhr.open("post","/student/select.do")
```

设置请求方式get或post，和请求的url地址

还有第三个参数可以指定同步还是异步，默认为true，一般不传

## 3，发送请求

```js
xhr.send()
```

调用send发生，参数需要通过这个方法携带

## 4，监听状态

```js
xhr.onreadystatechange = function (event) {
    if (xhr.readyState === 4) {
        if ((xhr.status >= 200 && xhr.status < 300) || xhr.status === 304) {
            console.log('success')
        } else {
            console.log('fail')
        }
    }
}
```

`onreadystatechange` 事件是xhr的状态`readyState`发生改变时则会调用，xhr有五个状态

0 请求未初始化，1 连接已建立 ，2 请求已接受 ，3 请求在处理 ，4 请求处理完成准备响应

我们想要获取结果，只需当状态为4的时候即可

但是请求完成，不一定请求成功了，要根据状态码判断，200~300和304的时候为成功，其余为失败

## 5，处理结果

```js
var result=xhr.responseText
console.log(result);
```

请求成功后，通过xhr得到`responseText` 即可得到请求的结果

# 封装

```js
function ajax(object) {
    var xhr = new XMLHttpRequest()
    xhr.open(object.type, object.url)
    xhr.send()
    xhr.onreadystatechange = function () {
        if (xhr.readyState === 4) {
            if ((xhr.status >= 200 && xhr.status < 300) || xhr.status === 304) {
                object.success(xhr)
            } else {
                object.error(xhr)
            }
        }
    }
}
```

封装一个方法，参数由一个对象全部带进来，包括请求地址，请求类型，成功函数，失败函数

调用的时候，指定好该对象的属性

```js
ajax({
    type:'post',
    url:'/student/select.do',
    success:function (xhr) {
        console.log(xhr.responseText)
    },
    error:function (xhr) {
        console.log(xhr.status)
    }
})
```

## 超时时间

```js
if (object.timeout) {
    setTimeout(function () {
        xhr.abort()
    }, object.timeout)
}
```

如果需要，我们可以设置一个超时时间，超过时间后调用`abort` 方法，终止请求

## 中文参数

get请求中需要将参数通过 `?key=value&k2=v2 `的形式携带，如果有中文需要转为uri编码

```js
var uname = encodeURI('张三')
```

## 参数携带

## post

```js
xhr.open(object.type, object.url)
xhr.setRequestHeader("Content-type", "application/x-www-form-urlencoded");
xhr.send("stuName=zz&stuId=2");
```

post请求需要在open之后，send之前，设置一个请求头

send的时候携带参数`k1=v1&k2=v2`

## get 

get请求直接在url后面携带参数`url?k1=v1&k2=v2`