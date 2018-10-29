# ioc

1,引入`jar`包 `core`,`expression`,`context`,`bean`,加上`log4j`和`commons-logging`作为日志

2,再`src`下创建`applicationContext.xml`并导入约束

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans 
        http://www.springframework.org/schema/beans/spring-beans.xsd">
</beans>
```

3,创建实体类Student并在`applicationContext.xml`文件中配置 

```xml
<bean id="student" class="com.znsd.entity.Student"></bean>
```

4,在`java`中加载配置文件`applicationContext.xml` 

```java
 
ApplicationContext context=new ClassPathXmlApplicationContext("applicationContext.xml");
```

5,用`getBean()`方法得到Student对象,参数为在`xml`中配置的bean的id 

```java
Student student=(Student)context.getBean("student");
```

# bean

**scope属性用来配置作用域**

1,`singleton` 单例的，且是默认值

2,`prototype` 多例的

3,`request` 存放到request的作用域中

4,`session` 存放到session 的作用域中

5,`global-session` 存放到多个站点

```xml
<bean id="student" class="com.znsd.entity.Student"  scope="prototype"></bean>
```

**属性**

**id**:用作唯一标示

**name**:一般情况和id没区别，但是可以有多个值，可以有特殊符号(如果用了`Struts1`可能会用到)

```xml
 
<bean id="student" name="student1,student2" class="com.znsd.entity.Student"  scope="prototype"></bean>
```

**class**:类的全限定名

**scope**:bean的作用域

# di

1通过xml文件配置注入属性

**构造注入**:在实体类中添加属性和构造方法

```java
 public class User {

    private String username;
    private String password;

    public User(String username, String password) {
        super();
        this.username = username;
        this.password = password;
    }
  
    @Override
    public String toString() {
        return "User [username=" + username + ", password=" + password + "]";
    }
}
```

配置

```xml
 <bean name="user" class="com.znsd.bean.User">
         <constructor-arg name="username" value="张三"></constructor-arg>
         <constructor-arg name="password" value="123"></constructor-arg>
</bean>
```

**setter注入**:添加属性和set，get方法 

```java
 public class User {

    private String username;
    private String password;
  
    @Override
    public String toString() {
        return "User [username=" + username + ", password=" + password + "]";
    }

    /**setter  and  getter**/
}
```

配置

```xml
<bean name="user" class="com.znsd.bean.User">
    <property name="username" value="gg"></property>
    <property name="password" value="123"></property>
</bean>
```

**注入对象**: 属性用ref来引用要注入的对象的bean的id 

```xml
 <bean id="userService" class="com.znsd.service.impl.UserServiceImpl">
        <property name="userDao" ref="userDao"></property>
</bean>
    
<bean id="userDao" class="com.znsd.dao.impl.UserDaoImpl"></bean>

```

**注入复杂类型**: 

数组: array 

```xml
 <property name="arr">
  <array>
    <value>1</value>
    <value>2</value>
    <value>3</value>
  </array>
</property>
```

List: list 

```xml
<property name="list">
    <list>
        <value>张三</value>
        <value>李四</value>
        <value>王五</value>
    </list>
</property>
```

Map:map 

```xml
<property name="map">
    <map>
        <entry key="1" value="y"></entry>
        <entry key="2" value="e"></entry>
        <entry key="3" value="s"></entry>
    </map>
</property>
```

Properties: props 

```xml
 <property name="properties">
    <props>
        <prop key="r">R</prop>
        <prop key="z">Z</prop>
        <prop key="e">E</prop>
    </props>
</property>
```

