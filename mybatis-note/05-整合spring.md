# 整合spring

1，导入spring-mybatis的整合包,

2,配置spring-mvc.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"  
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:mvc="http://www.springframework.org/schema/mvc"  
    xmlns:context="http://www.springframework.org/schema/context"  
    xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"  
    xsi:schemaLocation="http://www.springframework.org/schema/beans   
        http://www.springframework.org/schema/beans/spring-beans-4.2.xsd   
        http://www.springframework.org/schema/mvc   
        http://www.springframework.org/schema/mvc/spring-mvc-4.2.xsd   
        http://www.springframework.org/schema/context   
        http://www.springframework.org/schema/context/spring-context-4.2.xsd   
        http://www.springframework.org/schema/aop   
        http://www.springframework.org/schema/aop/spring-aop-4.2.xsd   
        http://www.springframework.org/schema/tx   
        http://www.springframework.org/schema/tx/spring-tx-4.2.xsd ">
        
     <!--开启注解驱动-->   
	<mvc:annotation-driven></mvc:annotation-driven>        
     <!--扫描整个项目-->  
    <context:component-scan base-package="com.znsd"></context:component-scan> 
</beans>
```

3,配置db.properties,**这里的用户名千万别取username**,因为spring的表达式${username}会拿到电脑主机的用户名,到时候会报数据库连不上的异常

```properties
jdbc.username=root
password=root
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://192.168.1.111:3306/test
```

4,配置spring-dao.xml,,(***重点***)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns:aop="http://www.springframework.org/schema/aop"     xmlns:context="http://www.springframework.org/schema/context"
xmlns:tx="http://www.springframework.org/schema/tx"
xsi:schemaLocation="
        http://www.springframework.org/schema/beans 
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context 
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/aop 
        http://www.springframework.org/schema/aop/spring-aop.xsd
        http://www.springframework.org/schema/tx 
        http://www.springframework.org/schema/tx/spring-tx.xsd">
        
        <!-- 加载刚刚的db.properties -->
	    <context:property-placeholder location="classpath:db.properties"/>
	     <!-- 用spring实例化数据源,可以使用c3p0,dbcp等，这里使用了阿里巴巴的druid数据源 -->
	     <bean  id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
	     	<!-- 千万别用${username}一定要换个名字 -->
        	<property name="username" value="${jdbc.username}"></property>
        	<property name="password" value="${password}"></property>
        	<property name="url" value="${url}"></property>
        	<property name="driverClassName" value="${driver}"></property>
        </bean>
        <!-- 实例化sqlsession工厂 -->
        <bean id="sqlSessionFactoryBean" class="org.mybatis.spring.SqlSessionFactoryBean">
        	<!-- 加载数据源 -->
        	<property name="dataSource" ref="dataSource"></property>
        	<!-- 加载mybatis-config.xml -->
        	<property name="configLocation" value="classpath:mybatis-config.xml"></property>
        </bean>
        <!-- 开启mapper扫描配置 -->
        <bean  class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        	<!-- 指定要扫描的包 -->
        	<property name="basePackage" value="com.znsd.mapper"></property>
        	<!-- 指定sqlsession工厂 -->
        	<property name="sqlSessionFactoryBeanName" value="sqlSessionFactoryBean"></property>
        	<!-- 指定@Repository为注解 -->
        	<property name="annotationClass" value="org.springframework.stereotype.Repository"></property>
        </bean>
        <!-- 实例化spring的事务管理器 -->
        <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        	<property name="dataSource" ref="dataSource"></property>
        </bean>
        <!-- 开启注解的方式使用事物 -->
        <tx:annotation-driven transaction-manager="transactionManager"/>
</beans>
```

5,配置mybatis-config.xml,不用再配置mapper和其他东西

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
	<typeAliases>
		<package name="com.znsd.entity"/>
	</typeAliases>
</configuration>
```

6,配置web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd" id="WebApp_ID" version="2.5">
 	<!--配置核心servlet-->
   <servlet>
  	<servlet-name>springmvc</servlet-name>
  	<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
  		<!--设置初始化参数-->
       <init-param>
  		<param-name>contextConfigLocation</param-name>
         <!--spring-*.xml,可以扫描到所有符合格式的xml-->
  		<param-value>classpath:spring-*.xml</param-value>
  	</init-param>
  </servlet>
  
  <servlet-mapping>
  	<servlet-name>springmvc</servlet-name>
  	<url-pattern>*.do</url-pattern>
  </servlet-mapping>
  
  
</web-app>
```

### 测试

1.编写controller,加上@Controller，并且用Service的接口作为属性自动注入

```java
@Controller
public class TestController {
	
	@Autowired
	private ITestService testService;
	
	@RequestMapping(value="test")
	public void test(HttpServletResponse response) throws IOException {
		List<TestBean> list= testService.select();
		response.setCharacterEncoding("UTF-8");
		response.setContentType("text/json;charset=UTF-8");
		response.getWriter().println(JSON.toJSON(list));
	}
}
```

2,service,加上@Service用于实例化,@Transactional可以让所有的方法都加上事物,直接使用Mapper接口作为属性

```java
@Service
@Transactional
public class TestServiceImpl implements ITestService {

	@Autowired
	private TestMapper testMapper;
	
	@Override
	public List<TestBean> select() {
		return testMapper.selectTest();
	}
}
```

3,mapper,使用@Repository

```java
@Repository
public interface TestMapper {
	public List<TestBean> selectTest();
}

```

