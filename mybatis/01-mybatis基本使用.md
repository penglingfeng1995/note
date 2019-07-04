# mybatis基本用法

### 一，纯XML模式

1,导入mybatis的依赖和对应数据库的依赖

```xml
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.4.6</version>
</dependency>

<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.43</version>
</dependency>
```

2，编写db.properties配置数据库的信息

```properties
username=root
password=root
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://192.168.1.111:3306/test
```

3,在src下配置核心配置文件mybatis-config.xml文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

	<!-- 导入数据库配置文件，之后可以用表达式来取值 -->
	<properties resource="db.properties"></properties>


	<!-- 直接去官方的说明书拷贝，也不用记，因为到了后期整合spring就不用配置这个东西了 -->
	<environments default="development">
		<environment id="development">
			<transactionManager type="JDBC" />
			<dataSource type="POOLED">
				<!-- 这里采用${key}可以取到导入的properties文件的值 -->
				<property name="driver" value="${driver}" />
				<property name="url" value="${url}" />
				<property name="username" value="${username}" />
				<property name="password" value="${password}" />
			</dataSource>
		</environment>
	</environments>
</configuration>

```

4，配置sql语句的配置文件，在com.znsd.mapper包下配置StudentMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!-- namespace类似于类的全名 -->
<mapper namespace="com.znsd.mapper.StudentMapper">

	<!-- id要是唯一的         resultType是返回值类型 -->
	<select id="selectStudent" resultType="com.znsd.entity.StudentBean">
		SELECT * FROM STUDENT_INFO
	</select>
</mapper>
```

并且在mybatis-config.xml注册

```xml 
<mappers>
  <mapper resource="com/znsd/mapper/StudentMapper.xml"/>
</mappers>
```

5.读取配置文件,这个不用记，整合spring后这个就不用自己去读取了

```java
//配置文件的名字，会从src下找
String config="mybatis-config.xml";
//转换成流
InputStream is=SqlSessionTools.class.getClassLoader().getResourceAsStream(config);
//构造工厂
SqlSessionFactory fac= new SqlSessionFactoryBuilder().build(is);
//得到执行sql的会话对象
SqlSession sqlSession= fac.openSession();
```

6,编写实体类StudentBean(属性暂时和数据库一样，后期会讲不一样的时候怎么处理),和测试类

```java
public class StudentBean { 

	private Integer id;
	private String student_id;
	private String student_name;
```

```java
@Test
public void test() {
  List<StudentBean> list= new StudentDao().selectStudent();
  for (StudentBean studentBean : list) {
    System.out.println(studentBean);
  }
}

public List<StudentBean> selectStudent(){
  //选择要执行的sql，
  String statement ="com.znsd.mapper.StudentMapper.selectStudent";
  //得到sqlSession
  SqlSession sqlSession= SqlSessionTools.getSqlSession();
  //执行sql
  List<StudentBean> list= sqlSession.selectList(statement);
  //返回结果
  return list;
}
```

### 二，XML加接口

1,编写对应的接口

```java
public interface StudentMapper {

	List<StudentBean> selectStudent();
}
```

2编写对应的xml，并注册接口

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!-- namespace对应一个接口，也就是接口的全名 -->
<mapper namespace="com.znsd.mapper.StudentMapper">

	<!-- id对应接口的方法名       resultType是返回值类型对应该方法的返回值类型 -->
	<select id="selectStudent" resultType="com.znsd.entity.StudentBean">
		SELECT * FROM STUDENT_INFO
	</select>
</mapper>
```

```xml
<mappers>
    <mapper class="com.znsd.mapper.StudentMapper"/>
</mappers>
```

3使用

```java
SqlSession sqlSession= SqlSessionTools.getSqlSession();
//得到接口 的实例
StudentMapper studentMapper= sqlSession.getMapper(StudentMapper.class);
//执行接口 的方法
List<StudentBean> list= studentMapper.selectStudent();
```

### 三，纯注解

编写接口

```java
public interface UserMapper {

	@Select(value = { "SELECT * FROM STUDENT_INFO" })
	List<StudentBean> selectUser();
}

```

注册

```xml
<mappers>
    <mapper class="com.znsd.mapper.StudentMapper"/>
    <mapper class="com.znsd.mapper.UserMapper"/>
</mappers>
```

使用方法和接口一样

## 总结

xml的方式配置简单但是使用繁琐，使用注解配置和使用都很方便但是在复杂情况下如多表嵌套查询会不够用，

现实中用的最多的就是xml加接口，在整合spring后，这种方式会更加方便