# RequestMapping

### 修饰类

除了修饰方法还可以修饰类

```java
@Controller
@RequestMapping("student")
public class StudentController {
```

对于的url也要加上类的映射路径

```html
<a href="${pageContext.request.contextPath }/student/test">to success</a>
```

### 参数method

method的值是枚举类型RequestMethod的值的数组,参数method可以限制请求的方式,限定只有post请求可以访问

```java
@RequestMapping(value="test",method= {RequestMethod.POST})
public String test() {
    return "success";
}
```

否则会抛出

```html
HTTP Status 405 - Request method 'GET' not supported
```

多个请求方式,一般不用

```java
@RequestMapping(value="test",method= {RequestMethod.POST,RequestMethod.POST})
```

### 参数params和headers

params的值为string型的数组，可以限制请求的参数条件，

```java
@RequestMapping(value="test",params= {"username"})
public String test() {
    return "success";
}
```

直接加个普通的字符串表示必须要有这个参数,否则抛出异常

```html
HTTP Status 400 - Parameter conditions "username" not met for actual request parameters:
```

加上参数?username=aaa可以访问

```html
<a href="${pageContext.request.contextPath }/student/test?username=aaa">to success</a>
```

支持简单的表达式，比如username!=aaa,

```java
@RequestMapping(value="test",params= {"username!=aaa"})
public String test() {
    return "success";
}
```

每个请求都会有请求头,headers用法和params类似

```java
@RequestMapping(value="test",headers= {"Accept-Language=zh-CN,zh;q=0.9,en;q=0.8"})
public String test() {
    return "success";
}
```

### 通配符url

支持ant风格的url，?代表一个字符,*代表多个字符,**代表多层路径

```java
@RequestMapping(value="**/test")
public String test() {
    return "success";
}
```

url可以为

```html
<a href="${pageContext.request.contextPath }/student/a/b/c/test">to success</a>
```

### 占位符url

可以使用{占位符}作为url的一部分,可以使用@PathVariable加在参数上，可以绑定对应的参数

```java
@RequestMapping(value="test/{id}")
public String test(@PathVariable String id) {
    System.out.println("id:"+id);
    return "success";
}
```

可以在url上带参数

```html
<a href="${pageContext.request.contextPath }/student/test/001">to success</a>
```

## @ResponseBody

@ResponseBody用来修饰方法，被@ResponseBody修饰的方法会将返回值不做视图解析，直接作为资源内容返回到页面，如果到了jackson的jar包，直接返回对象可以把对象作为json数据直接返回