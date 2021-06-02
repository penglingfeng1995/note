## 引入依赖

```xml
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>1.7.25</version>
</dependency>
```

## 使用接口

```java
import org.junit.Test;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class Slf4jTest {

    private static final Logger log = LoggerFactory.getLogger(Slf4jTest.class);

    @Test
    public void test1(){
        log.info("slf4j测试");
        // 带参数打印，使用占位符{}，按下标填充，可以减少字符串提前拼接，提高性能
        log.info("需要添加的数量={},需要减少的数量={}", 10, 20);
        // 打印异常信息
        log.error("执行异常",new Exception("slf4j---"));
        // 打印参数及异常，需要把异常对象作为最后一个参数
        log.error("密码错误，userId={},pwd={}", 123, "abc", new Exception("密码不匹配"));
    }
}
```

可以使用` lombok` 的注解，可以直接在类中使用 log 实例。

```java
import lombok.extern.slf4j.Slf4j;

@Slf4j
public class LogTest {

    @Test
    void testLog(){
      log.info("节日快乐");
    }
}
```

## 执行过程

`slf4j-api`会去动态查找`org.slf4j.impl.StaticLoggerBinder`这个类，通常这个类会在各种绑定包中，如果没有这个类，则会使用默认的`org.slf4j.helpers.NOPLogger`,这个类中都是空方法,将不做任何操作,并打印三条警告

![](img/2.png)

如果存在`org.slf4j.impl.StaticLoggerBinder`,则会通过这个绑定对应的日志实现

通过需要 `slf4j-api`，绑定包，实现包，三个类型的包完成一组调用

## 调用其他实现

### slf4j-nop

```xml
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-nop</artifactId>
    <version>1.7.25</version>
</dependency>
```

这个包中的`org.slf4j.impl.StaticLoggerBinder`就是绑定的`slf4j-api`中的NOPLogger，这个包的作用就是不再打印那三行警告

### slf4j-simple

```xml
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-simple</artifactId>
    <version>1.7.25</version>
</dependency>
```

这个包会调用`System.err`方法，把日志打印在控制台，一个简单实现

### log4j

```xml
<!--绑定包-->
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-log4j12</artifactId>
    <version>1.7.25</version>
</dependency>

<!--log4j实现包-->
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
```

通过绑定包会绑定log4j-1.2.x的版本

### jul

```xml
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-jdk14</artifactId>
    <version>1.7.25</version>
</dependency>
```

绑定jul，由于jul是jdk自带的，不需要其他依赖

### jcl

```xml
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-jcl</artifactId>
    <version>1.7.25</version>
</dependency>

<dependency>
    <groupId>commons-logging</groupId>
    <artifactId>commons-logging</artifactId>
    <version>1.2</version>
</dependency>
```

先绑定jcl，再通过jcl调用对应的日志实现

这种用法很少，因为jcl本身就是日志门面，多个日志门面间转发，性能很差

### logback

```xml
<!--绑定包-->
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>

<!--logback实现-->
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-core</artifactId>
    <version>1.2.3</version>
</dependency>
```

logback是slf4j的经典实现，是slf4j的最优搭配

### log4j2

```xml
<!--绑定包-->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-slf4j-impl</artifactId>
    <version>2.11.2</version>
</dependency>


<!--log4j2实现，会自动依赖log4j-api-->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-core</artifactId>
    <version>2.11.2</version>
</dependency>
```

由于log4j2发布时间在slf4j之后，绑定包由log4j2提供

## 总结

| 实现类型 | 绑定包                                    | 实现包                              |
| -------- | ----------------------------------------- | ----------------------------------- |
| nop      | org.slf4j:slf4j-nop                       | 无实现                              |
| simple   | org.slf4j:slf4j-simple                    | System.err                          |
| log4j    | org.slf4j:slf4j-log4j12                   | log4j:log4j                         |
| jul      | org.slf4j:slf4j-jdk14                     | jdk自带java.util.logging            |
| jcl      | org.slf4j:slf4j-jcl                       | commons-logging:commons-logging     |
| logback  | ch.qos.logback:logback-classic            | ch.qos.logback:logback-core         |
| log4j2   | org.apache.logging.log4j:log4j-slf4j-impl | org.apache.logging.log4j:log4j-core |

slf4j可以通过各种绑定包，绑定到对应的日志实现，其中logback最佳。

版本选择上，尽可能同一个groupid的版本一致