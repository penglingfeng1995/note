# 概述

一款可以简化开发的插件，在编译期间，通过注解增加特定的类成员。

参与编译，不参与运行，设置为 provided

```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.8</version>
    <scope>provided</scope>
</dependency>
```

# 使用

## @Data

```java
@Data
public class Student {

    private String stuName;
    private Integer stuAge;
}
```

使用该注解标注的类将自动增加，`getter,setter,equals,hashCode,toString`方法

```java
Student student = new Student();
String stuName = student.getStuName();
```

## @Slf4j

自动生成 slf4j 的对象

```java
private static final org.slf4j.Logger log = org.slf4j.LoggerFactory.getLogger(LogExample.class);
```

可以直接使用 log 对象

```java
@Slf4j
public class LomApp {

    public static void main(String[] args) {
        log.info("hello lombok");
    }
}
```

