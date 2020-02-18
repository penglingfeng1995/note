# 概述

阿里巴巴开发的高性能 json 库

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.62</version>
</dependency>
```

# 使用

对象转 json ,调用 `JSON.toJSONString` 方法

```java
Student student = new Student();
student.setStuName("张三");
student.setStuAge(12);

String studentJson = JSON.toJSONString(student);
log.info(studentJson);//{"stuAge":12,"stuName":"张三"}
```

json 转对象，调用 `JSON.parseObject` 得到`JSONObject` 对象，这是一种类似于 map 的数据结构。

```java
JSONObject jsonObject = JSON.parseObject(studentJson);
log.info(jsonObject.getString("stuName"));
```

若想指定类型，则传入指定类型。

```java
Student student1 = JSON.parseObject(studentJson, Student.class);
log.info(student1.toString());
```

