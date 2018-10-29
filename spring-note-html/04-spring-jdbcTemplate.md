# jdbc  template

导入`spring-jdbc-4.3.12.RELEASE.jar`使用spring的数据源 

```java
DriverManagerDataSource source=new DriverManagerDataSource();
source.setDriverClassName("com.mysql.jdbc.Driver");
source.setUrl("jdbc:mysql://192.168.2.135:3306/test");
source.setUsername("root");
source.setPassword("root");
```

执行`sql`语句 

```java
JdbcTemplate jdbcTemplate=new JdbcTemplate(source);
String sql="insert into user_info values(?,?)";
//执行sql语句
jdbcTemplate.update(sql, "zs","ls");
```

**c3p0**

导入 `c3p0-0.9.1.2.jar`在`xml`中配置`c3p0` 

```xml
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
    <property name="driverClass" value="com.mysql.jdbc.Driver"></property>
    <property name="password" value="root"></property>
    <property name="user" value="root"></property>
    <property name="jdbcUrl" value="jdbc:mysql://192.168.2.135:3306/test"></property>
</bean>
```

把`JdbcTemplate`作为`dao`的实现类的属性并注入 

```xml
//加载数据源到jdbcTemplate
<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
    <property name="dataSource" ref="dataSource"></property>
</bean>
//加载到dao中
<bean id="userDao" class="com.znsd.dao.impl.UserDaoImpl">
    <property name="jdbcTemplate" ref="jdbcTemplate"></property>
</bean>
//加载到service中
<bean id="userService" class="com.znsd.service.impl.UserServiceImpl">
    <property name="userDao" ref="userDao"></property>
</bean>
```

在`dao`中使用`jdbcTemplate` 

# tx

## 基于xml

1，导入`tx`的约束和`spring-tx-4.3.12.RELEASE.jar`

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
</beans>
```

2,注册spring的事务管理器`DataSourceTransactionManager`,注入数据源 

```xml
<bean id="transactionManager"
 class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"></property>
</bean>
```

3,配置事务增强的切面

```xml
<tx:advice id="txadvice" transaction-manager="transactionManager">
    <tx:attributes >
        <tx:method  name="add"/>
    </tx:attributes>
</tx:advice>
```

4,为一个切点配置事务增强 

```xml
<aop:config>
    <aop:pointcut 
                  expression="execution(* com.znsd.service.impl.UserServiceImpl.add())" id="cut1"/>
    <aop:advisor advice-ref="txadvice" pointcut-ref="cut1"/>
</aop:config>
```

5，配置好后当这个切点下的方法，报错会回滚 

## 基于注解

1，注册spring的事务管理器`DataSourceTransactionManager`,注入数据源 

```xml
<bean id="transactionManager"     class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource" ref="dataSource"></property>
</bean>
```

2,开启事务的注解模式 

```xml
<tx:annotation-driven transaction-manager="transactionManager"/>
```

3,在类上加上`@Transactional`,类中的所有方法都支持了事务 

```java
@Transactional
public class UserServiceImpl implements IUserService {
```

