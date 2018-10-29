# spring-mybatis

1,导入`mybatis-spring-1.3.1.jar`,`mybatis-3.4.5.jar`,和`mysql-connector-java-5.1.26-bin.jar` 

2,配置`db.properties` 

```properties
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://192.168.2.135:3306/test
username=root
password=root
```

3,加载`db.properties`文件 

```xml
<bean class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer" >
    <property name="location" value="classpath:db.properties"></property>
</bean>
```

4,配置`c3p0`的数据源,通过`${键}`设置值 

```xml
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
    <property name="driverClass" value="${driver}"></property>
    <property name="password" value="${password}"></property>
    <property name="user" value="${username}"></property>
    <property name="jdbcUrl" value="${url}"></property>
</bean>
```

5.配置mybatis核心文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <typeAliases>
        <package name="com.znsd.bean" />
    </typeAliases>
    <mappers>
        <mapper resource="con/znsd/mapper/UserMapper.xml"/>
    </mappers>
</configuration>
```

6,创建factory

```xml
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
    <property name="dataSource" ref="dataSource"></property>
    <property name="configLocation" value="classpath:mybatis-config.xml"></property>
</bean>
```

7,扫码接口mapper

```xml
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
    //指定扫描的包
    <property name="basePackage" value="com.znsd.mapper"></property>
    //配置一个template或factory  注意这里是value不是ref
    <property name="sqlSessionFactoryBeanName" value="SqlSessionFactory"></property>
    //表示指定repository这个注解来配置
    <property name="annotationClass" value="org.springframework.stereotype.Repository"></property>
</bean>
```

8,配置mapper,

在`UserMapper.xml`同包下创建`UserMapper.class`接口类,`xml`的`namespace`要和类的全名一致 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.znsd.mapper.StudentMapper">
    <insert id="insertStudent" parameterType="StudentBean">
        insert into stu_info(student_id,student_name) values(#{studentId},#{studentName})
    </insert>
</mapper>
```

同时在代理对象接口中创建和`sql`的id一样的方法 

```java
package com.znsd.mapper;

@Repository
public interface StudentMapper {
	
    int insertStudent(StudentBean student);
}
```

注入mapper

```java
public class StudentServiceImpl implements StudentService {

    @Autowired
    private StudentMapper studentMapper;
```

直接调用接口类的方法 

```java
int rows= studentMapper.insertStudent(student);
```

