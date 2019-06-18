# log4j的基本使用

1,引入log4j的依赖

```xml
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
```

2,在`src/main/resources`下编写`log4j.properties`作配置信息

```properties
# 设置根logger的级别为info，使用STDOUT这个appender
log4j.rootLogger=info,STDOUT

# 定义了一个名为STDOUT的appender,及属性
log4j.appender.STDOUT=org.apache.log4j.ConsoleAppender
log4j.appender.STDOUT.layout=org.apache.log4j.PatternLayout
log4j.appender.STDOUT.layout.conversionPattern=%d{yyyy-MM-dd HH:mm:ss:SSS} %m%n
```

3,编写测试类

log4j的核心api为`org.apache.log4j.Logger`,通过静态方法得到实例

```java
import org.apache.log4j.Logger;
import org.junit.Test;

public class Log4jTest {

    private static final Logger LOG = Logger.getLogger(Log4jTest.class);

    @Test
    public void test1(){
        LOG.info("log4j测试");
        LOG.error("异常",new Exception("文件不存在"));
    }
}
```

### 日志的等级

log4j会把日志分为七个等级，常用其中的四个等级，DEBUG<INFO<WARN<ERROR,会参照配置文件中的等级显示对应的等级日志，大于等于当前等级的日志

### 常用目的地

```java
org.apache.log4j.ConsoleAppender （控制台）
org.apache.log4j.FileAppender （ 文件）
```

配置一个文件目的地

```properties
log4j.appender.myAppender=org.apache.log4j.FileAppender
log4j.appender.myAppender.File=D:\\mylog.txt
```

日志就会输出到指定文件

### 常用日志格式

```java
org.apache.log4j.PatternLayout （自定义格式），
org.apache.log4j.SimpleLayout （简单格式，等级+消息），
org.apache.log4j.TTCCLayout（线程+时间+类名+消息）
```

使用自定义格式

```properties
log4j.appender.myAppender.layout=org.apache.log4j.PatternLayout
#格式转换器
log4j.appender.myAppender.layout.ConversionPattern=%-20d{yyyy-MM-dd hh:mm:ss}%c using %r ms %n%-5p:%m  %n
```

| 参数 |                   含义                    |
| :--: | :---------------------------------------: |
|  %p  |              输出日志的等级               |
|  %d  | 输出日志的时间，可以自定义%d{yyyy-MM-dd } |
|  %r  |                花费的时间                 |
|  %c  |               所在类的全名                |
|  %t  |                  线程名                   |
|  %l  |                日志的位置                 |
|  %f  |                  文件名                   |
|  %m  |                  消息体                   |
|  %n  |                   换行                    |
|  %%  |                  输出%号                  |

