# 概述

一个高性能的 rpc 框架

# 基本使用

一，首先安装 zookeeper

解压后，修改 `conf/zoo.cfg` ,然后运行`./zkServer.sh start`，并暴露 2181 端口

二，编写接口类

接口类最好使用一个独立的 jar 包，方便被生产者和消费者同时依赖

```java
public interface StudentService {

    String hello(String name);
}
```

三，引入依赖

版本这块坑比较多。2.7以下的版本为 alibaba，以上为 apache。

```xml
<dependency>
    <groupId>org.apache.dubbo</groupId>
    <artifactId>dubbo</artifactId>
    <version>2.7.0</version>
</dependency>
<dependency>
    <groupId>org.apache.curator</groupId>
    <artifactId>curator-framework</artifactId>
    <version>2.8.0</version>
</dependency>
<dependency>
    <groupId>org.apache.curator</groupId>
    <artifactId>curator-recipes</artifactId>
    <version>2.8.0</version>
</dependency>
```

四，消费者端，实现接口

```java
public class StudentServiceImpl implements StudentService {
    @Override
    public String hello(String name) {
        return "hello,my name is " + name;
    }
}
```

配置 dubbo , 使用 `dubbo:service` 暴露服务接口

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:dubbo="http://dubbo.apache.org/schema/dubbo"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://dubbo.apache.org/schema/dubbo http://dubbo.apache.org/schema/dubbo/dubbo.xsd">

    <dubbo:application name="student-prov"/>

    <dubbo:registry address="zookeeper://192.168.80.128:2181"/>

    <dubbo:protocol name="dubbo" port="20880"/>

    <bean id="studentService" class="com.plf.dup.StudentServiceImpl"/>

    <dubbo:service interface="com.plf.du.StudentService" ref="studentService"/>
</beans>
```

启动项目

```java
public static void main(String[] args) throws IOException {
    ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("spring.xml");
    context.start();
    System.in.read();
}
```

五，生产者调用接口

使用 `dubbo:reference` 调用接口，注意appName需要唯一

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:dubbo="http://dubbo.apache.org/schema/dubbo"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd   http://dubbo.apache.org/schema/dubbo http://dubbo.apache.org/schema/dubbo/dubbo.xsd http://code.alibabatech.com/schema/dubbo http://code.alibabatech.com/schema/dubbo/dubbo.xsd">

    <dubbo:application name="student-cons"/>

    <dubbo:registry address="zookeeper://192.168.80.128:2181"/>

    <dubbo:protocol name="dubbo" port="20880"/>

    <dubbo:reference id="studentService" interface="com.plf.du.StudentService"/>
</beans>
```

调用接口

```java
public static void main(String[] args) {
    ApplicationContext context = new ClassPathXmlApplicationContext("spring.xml");
    StudentService studentService = context.getBean(StudentService.class);
    System.out.println(studentService.hello("张三"));
}
```



# 整合 springboot

## 定义接口

创建公共接口的模块,dubbo-api

定义接口

```java
public interface StudentService {

    String hello(String name);

    Student getStudent(Integer studentId);
}
```

如果涉及到实体类，需要序列化。

```java
public class Student implements Serializable {

    private Integer studentId;
    private String studentName;
```

## 生产者

引入依赖 `org.apache.dubbo:dubbo-spring-boot-starter` 以及 `org.apache.dubbo:dubbo` 及相关依赖。

消费者引入同样的依赖。

```xml
<properties>
    <spring-boot.version>2.2.2.RELEASE</spring-boot.version>
</properties>

<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <dependency>
        <groupId>org.apache.dubbo</groupId>
        <artifactId>dubbo-spring-boot-starter</artifactId>
        <version>2.7.0</version>
    </dependency>

    <dependency>
        <groupId>org.apache.dubbo</groupId>
        <artifactId>dubbo</artifactId>
        <version>2.7.0</version>
    </dependency>
    <dependency>
        <groupId>org.apache.curator</groupId>
        <artifactId>curator-framework</artifactId>
        <version>2.8.0</version>
    </dependency>
    <dependency>
        <groupId>org.apache.curator</groupId>
        <artifactId>curator-recipes</artifactId>
        <version>2.8.0</version>
    </dependency>
    <!--公共接口-->
    <dependency>
        <groupId>org.example</groupId>
        <artifactId>dubbo-api</artifactId>
        <version>1.0-SNAPSHOT</version>
    </dependency>
</dependencies>

<dependencyManagement>
    <dependencies>
        <!-- Spring Boot -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-dependencies</artifactId>
            <version>${spring-boot.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

实现接口,并使用`org.apache.dubbo.config.annotation.Service` 注解标注，使其注册为一个dubbo组件。

```java
@Service
public class StudentServiceImpl implements StudentService {
    @Override
    public String hello(String name) {
        return "my name is " + name;
    }

    @Override
    public Student getStudent(Integer studentId) {
        Student student = new Student();
        student.setStudentId(studentId);
        student.setStudentName("亚索");
        return student;
    }
}
```

启动类上使用 `org.apache.dubbo.config.spring.context.annotation.DubboComponentScan` 扫描dubbo组件。

```java
@SpringBootApplication
@DubboComponentScan
public class DuApplication {
```

配置 application.yml

```yaml
dubbo:
  application:
    name: dubbo-prov-b
  registry:
    address: zookeeper://192.168.80.128:2181
  protocol:
    name: dubbo
    port: 20880
```

启动项目即可。

## 消费者

在某个bean中，注入接口，使用`org.apache.dubbo.config.annotation.Reference` 注入。

```java
@RestController
public class StudentController {

    @Reference
    private StudentService studentService;

    @RequestMapping("/getStudent/{id}")
    public Student getStudent(@PathVariable Integer id){
        return studentService.getStudent(id);
    }
}
```

启动类无需多的配置。

```java
@SpringBootApplication
public class DucApplication {
```

同样需要配置 zk 的端口等配置。

```yaml
server:
  port: 8081
dubbo:
  application:
    name: dubbo-cons-b
  registry:
    address: zookeeper://192.168.80.128:2181
  protocol:
    name: dubbo
    port: 20880
```

启动项目，调用接口测试。