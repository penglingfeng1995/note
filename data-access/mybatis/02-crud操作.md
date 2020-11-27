# CRUD操作

## 几个小技巧

1，类型别名

在mybatis-config.xml中配置

```xml
<typeAliases>
    <typeAlias type="com.znsd.entity.StudentBean" alias="student"/>
</typeAliases>
```

配置mapper的时候就可以使用别名

```xml
<select id="selectStudent" resultType="student">
    SELECT * FROM STUDENT_INFO
</select>
```

配置多个实体类的时候可以直接配置一个包，包下的所以类都可以直接使用类名，不用加包名

```xml
<typeAliases>
    <package name="com.znsd.entity"/>
</typeAliases>
```

mapper中

```xml
<select id="selectStudent" resultType="StudentBean">
    SELECT * FROM STUDENT_INFO
</select>
```

2,扫描mapper的包

每次写一个mapper就要在核心中配置，为了方便，直接扫描一个包就可以自动配置下面所以的mapper

```xml
<mappers>
    <package name="com.znsd.mapper"/>
</mappers>
```

3,当数据库的字段和属性对应不上时有两种办法

在写sql语句的使用别名,别名和实体类属性对上

```xml
<select id="selectStudent" resultType="StudentBean">
    SELECT ID,STUDENT_ID STUDENTID,STUDENT_NAME STUDENTNAME FROM STUDENT_INFO
</select>
```

或者

使用resultMap,主键使用id子节点，普通属性使用result子节点，column属性为数据库的字段名，property为实体类的属性

```xml
<resultMap type="StudentBean" id="studentBeanMap">
    <id column="ID" property="id"/>
    <result column="STUDENT_ID" property="studentId"/>
    <result column="STUDENT_NAME" property="studentName"/>
</resultMap>
```

statement配置的时候将原来的resultType改为resultMap，值为对应的resultMap的id

```xml
<select id="selectStudent" resultMap="studentBeanMap">
    SELECT * FROM STUDENT_INFO
</select>
```

## CRUD

配置statement,使用#{属性名}，可以自动将该对象的属性注入,parameterType就是参数类型,这里使用了别名

```xml
<insert id="insertStudent" parameterType="studentBean">
    INSERT INTO STUDENT_INFO(STUDENT_ID,STUDENT_NAME) VALUES(#{studentId},#{studentName})
</insert>

<update id="updateStudent" parameterType="studentBean">
    UPDATE STUDENT_INFO SET STUDENT_ID=#{studentId},STUDENT_NAME=#{studentName} WHERE ID=#{id}
</update>

<delete id="deleteStudent" parameterType="int">
    DELETE FROM STUDENT_INFO WHERE ID=#{id}
</delete>
```

编写对应的方法,增删改的方法会返回对应的受影响的行

```java
public interface StudentMapper {

	List<StudentBean> selectStudent();
	
	Integer insertStudent(StudentBean student);
	
	Integer updateStudent(StudentBean student);
	
	Integer deleteStudent(Integer id);
}
```

使用

```java
SqlSession sqlSession= SqlSessionTools.getSqlSession();
//得到mapper
StudentMapper studentMapper= sqlSession.getMapper(StudentMapper.class);
//执行对应的方法
Integer rows= studentMapper.insertStudent(student);
//应为改变了数据库所以需要提交
sqlSession.commit();
//关闭链接
sqlSession.close();
```

### insert ID 回显

当执行insert操作，主键是自增的，插入后，主键可以自动赋值给原对象

```xml
<insert id="insert">
    <selectKey keyProperty="id" order="AFTER" resultType="java.lang.Long">
        SELECT LAST_INSERT_ID()
    </selectKey>
    insert into t_student(student_name) values (#{studentName});
</insert>
```

ex:

```java
TStudent tStudent = new TStudent();
// 主键自增不需设置
tStudent.setStudentName("测试id2");
studentMapper.insert(tStudent);
// 主键已赋值
log.info(tStudent.getId().toString());
```

## 多个参数传入

除了上面的把多个参数包装成一个对象，用#{属性}取值,还有三种办法可以传多个参数

### 1,注解

在参数前加@Param,设置属性value

```java
	List<StudentBean> selectStudentByLikeAndPage(
        @Param(value = "start") int start,
        @Param(value = "record")int record);

```

statement中可以直接用#{value}取值，值填写刚设置的value,不用写paramterType

```xml
<select id="selectStudentByLikeAndPage" resultMap="studentBeanMap">
    SELECT * FROM STUDENT_INFO LIMIT #{start},#{record}
</select>
```

### 2,下标

接口中的方法

```java
List<StudentBean> selectStudentByLikeAndPage(int start,int record);
```

xml

使用arg,从0开始算下标

```xml
<select id="selectStudentByLikeAndPage" resultMap="studentBeanMap">
    SELECT * FROM STUDENT_INFO LIMIT #{arg0},#{arg1}
</select>
```

或者param，从1开始算下标

```xml
<select id="selectStudentByLikeAndPage" resultMap="studentBeanMap">
    SELECT * FROM STUDENT_INFO LIMIT #{param1},#{param2}
</select>
```

### 3,使用Hashmap

接口中的方法

```java
List<StudentBean> selectStudentByLikeAndPage(Map<String,Object> map);
```

xml,使用#{key}取值，填map中的键

```xml
<select id="selectStudentByLikeAndPage" resultMap="studentBeanMap">
    SELECT * FROM STUDENT_INFO LIMIT #{start},#{record}
</select>
```

使用map,会通过键取值

```java
//设置map
Map<String,Object> map=new HashMap<String,Object>();
map.put("start",2);
map.put("record", 4);
//传map
List<StudentBean> list= studentMapper.selectStudentByLikeAndPage(map);
```

使用hashmap还可以带入多个对象类型

```java
Map<String,Object> map=new HashMap<String,Object>();
//new一个分页对象
Page page=new Page();
page.setStart(2);
page.setRecord(4);
//存入map
map.put("page", page);
List<StudentBean> list= studentMapper.selectStudentByLikeAndPage(map);
```

xml中可以用#{对象.属性}的方式取值

```xml
<select id="selectStudentByLikeAndPage" resultMap="studentBeanMap">
    SELECT * FROM STUDENT_INFO LIMIT #{page.start},#{page.record}
</select>
```

## # 与  $ 区别

#{} ： 会使用 PreparedStatement 进行预编译，该占位符替换为 `?` ，使用 PreparedStatement 进行赋值，更加安全，但是只能设置参数，比如有的需要传表名，或者 `DESC` 等关键字无法使用。也不能在`"#{}"` 字符串中使用

${} ： 会使用字符串替换，灵活度高，但是存在sql注入风险

## 指定type

使用mybatis占位符进行传值的时候，如果一个值允许为null，此时mybatis是不知道这个null值对应的jdbc类型，需要指定jdbc类型，详见`PreparedStatement.setNull(paramIndex,sqlType)`

```
#{userId,jdbcType=VARCHAR}
```

