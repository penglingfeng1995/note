# log4j的基本使用

1,导入jar包,`log4j-1.2.17.jar`,

2,在src下编写`log4j.properties`作配置信息

```properties
#注册目的地，并设置消息等级
log4j.rootLogger=debug,myAppender
#myAppender是自定义的日志目的地的名称，选择在Console控制台输出
log4j.appender.myAppender=org.apache.log4j.ConsoleAppender
#输出的格式为SimpleLayout简单格式，值包含日志等级和日志主体
log4j.appender.myAppender.layout=org.apache.log4j.SimpleLayout
```

3,编写测试类

```java
public class MyTest {
	//得到logger对象，用来输出日志,通常加载当前类
	private static Logger logger=Logger.getLogger(MyTest.class);
	
	public static void main(String[] args) {
        //输出日志
		logger.debug("logger.debug这是调试");
		logger.info("logger.info这是消息");
		logger.warn("logger.warn这是警告");
		logger.error("logger.error这是错误");
	}
}
```

4,控制台上显示

```java
DEBUG - logger.debug这是调试
INFO - logger.info这是消息
WARN - logger.warn这是警告
ERROR - logger.error这是错误
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

