# spring注解

配置xml

```xml
 <beans xmlns="http://www.springframework.org/schema/beans"                  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans 
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context 
        http://www.springframework.org/schema/context/spring-context.xsd">

</beans>
```

**开启注解扫描**:`base-package`属性为要扫描的包 

```xml
<context:component-scan base-package="com.znsd"></context:component-scan>
```

在类上加`@Component`注解,可以指定value，相当于id，如果不指定，则默认把类的第一个字母小写作为value

有三个衍生的注解功能目前和`@Component`一样未来会加强,分别是

Web层`@Controller`,业务层`@Service`,持久层`@Repository`

```java
@Component(value="user") 
public class User {
```

**注入属性**

使用`@Autowired`注解会自动寻找对应的bean对象来注入 

```java
@Autowired
private UserDao userDao;
```

如果想要指定对应的bean可以使用`@Resource`,并添加`value`属性来指定 

```java
@Resource(value="user")
private UserDao userDao;
```

**读取properties**

**单个文件**:默认从`classpath`(`src`)下作为根目录,使用`<context:property-placeholder />`标签为`location`属性赋值 

```xml
 <context:property-placeholder location="db.properties"/> 
```

or 使用`PropertyPlaceholderConfigurer`类加载,并为`location`属性赋值,值为文件路径 

```xml
<bean id="propertyConfig"     class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer" >
    <property name="location" value="classpath:db.properties"></property> 
</bean>
```

赋值   使用`@Value(value="${键}")`来设置值

```java
@Value(value = "${driver}")
private String driver;
```



**多个文件**:会读取全部的键值对,后面的会覆盖先读取的键值对,属性为`locations`

```xml
<bean class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer"> 
        <property name="locations">
              <array> 
                  <value>classpath:db.properties</value>
                  <value>classpath:db2.properties</value> 
              </array>
       </property> 
</bean>
```

spel 表达式

单个文件:需要导入`util`约束 

```xml
<util:properties id="configProperties" location="classpath:db.properties"/>
```

多个文件:使用`PropertiesFactoryBean`类对其`locations`属性赋值 

```xml
<bean id="configProperties" class="org.springframework.beans.factory.config.PropertiesFactoryBean"> 
    <property name="locations">
         <array> 
             <value>classpath:db.properties</value>
             <value>classpath:db2.properties</value> 
         </array> 
    </property> 
</bean>
```

使用`@Value(value=" # { id [ ' 键' ] }")` 

```java
@Value(value="#{configProperties['username']}")
private String username;
```

