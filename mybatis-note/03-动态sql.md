# 动态sql

1，为了方便测试，能够实时看见我们执行了什么sql，做一个日志输出，在src下创建`log4j.properties`文件做配置,并导入`log4j-1.2.17.jar`包

```properties
log4j.rootLogger=DEBUG,Console
log4j.appender.Console=org.apache.log4j.ConsoleAppender
log4j.appender.Console.layout=org.apache.log4j.PatternLayout
log4j.appender.Console.layout.ConversionPattern=%d [%t] %-5p [%c] - %m%n
log4j.logger.org.apache=INFO
```

## if

1,之前用的分页，在map中传入了一个分页对象

```java
Map<String,Object> map=new HashMap<String,Object>();
Page page=new Page();
page.setStart(2);
page.setRecord(4);
map.put("page", page);
List<StudentBean> list= studentMapper.selectStudentByLikeAndPage(map);
```

在xml中取值,用#{对象.属性}

```xml
<select id="selectStudentByLikeAndPage" resultMap="studentBeanMap">
    SELECT * FROM STUDENT_INFO LIMIT #{page.start},#{page.record}
</select>
```

假如传的是一个null

```java
//		map.put("page", page);
		map.put("page", null);
```

就会抛出`MySQLSyntaxErrorException`异常

```java
Caused by: com.mysql.jdbc.exceptions.jdbc4.MySQLSyntaxErrorException: 
You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'null,null' at line 1
```

2,现在改为用if条件,test填写表达式,表达式内的结果为true则执行的sql语句会自动拼接if节点的内容

```xml
<select id="selectStudentByLikeAndPage" resultMap="studentBeanMap">
    SELECT * FROM STUDENT_INFO 

    <if test="page!=null">
        LIMIT #{page.start},#{page.record}
    </if>
</select>
```

日志执行结果,可以看到执行的sql为SELECT * FROM STUDENT_INFO

```java
2018-02-17 13:51:51,764 [main] DEBUG [com.znsd.mapper.StudentMapper.selectStudentByLikeAndPage] - ==>  Preparing: SELECT * FROM STUDENT_INFO 
2018-02-17 13:51:51,792 [main] DEBUG [com.znsd.mapper.StudentMapper.selectStudentByLikeAndPage] - ==> Parameters: 
2018-02-17 13:51:51,812 [main] DEBUG [com.znsd.mapper.StudentMapper.selectStudentByLikeAndPage] - <==      Total: 15
```

再测试传入有值的page对象,执行的sql变成了有limit的sql语句 SELECT * FROM STUDENT_INFO LIMIT ?,? 

```java
2018-02-17 13:55:03,610 [main] DEBUG [com.znsd.mapper.StudentMapper.selectStudentByLikeAndPage] - ==>  Preparing: SELECT * FROM STUDENT_INFO LIMIT ?,? 
2018-02-17 13:55:03,639 [main] DEBUG [com.znsd.mapper.StudentMapper.selectStudentByLikeAndPage] - ==> Parameters: 2(Integer), 4(Integer)
2018-02-17 13:55:03,660 [main] DEBUG [com.znsd.mapper.StudentMapper.selectStudentByLikeAndPage] - <==      Total: 4
```

## choose

当我们有多个条件的时候，只想选则其一，可以用到choose，和jstl很想

```xml
<select id="selectStudentByLikeAndPage" resultMap="studentBeanMap">
		SELECT * FROM STUDENT_INFO
    <!--这里的1=1代表其他条件-->
		WHERE 1=1
		<choose>
            <!--如果要用 对象.属性 的方式判断，一定要先判断改对象不为null-->
			<when test="student!=null and student.studentSex!=null and student.studentSex!=''">
				AND STUDENT_SEX=#{student.studentSex}
			</when>
			<when test="student!=null and student.studentAge!=null and student.studentAge!=''">
				AND STUDENT_AGE=#{student.studentAge}
			</when>
			<otherwise>
				LIMIT 0,5
			</otherwise>
		</choose>
	</select>
```

当我们在map中为student放一个null

```java
map.put("student", null);
```

执行结果 ，当前两个条件不满足时，会执行otherwise的SELECT * FROM STUDENT_INFO WHERE 1=1 LIMIT 0,5 

```java
2018-02-17 16:10:46,387 [main] DEBUG [com.znsd.mapper.StudentMapper.selectStudentByLikeAndPage] - ==>  Preparing: SELECT * FROM STUDENT_INFO WHERE 1=1 LIMIT 0,5 
2018-02-17 16:10:46,416 [main] DEBUG [com.znsd.mapper.StudentMapper.selectStudentByLikeAndPage] - ==> Parameters: 
2018-02-17 16:10:46,434 [main] DEBUG [com.znsd.mapper.StudentMapper.selectStudentByLikeAndPage] - <==      Total: 5
```

当我们设置student,只设置sex

```java
student.setStudentSex("男");
map.put("student", student);
```

执行的结果,sql语句为SELECT * FROM STUDENT_INFO WHERE 1=1 AND STUDENT_SEX=? ,

```java
2018-02-17 16:15:32,233 [main] DEBUG [com.znsd.mapper.StudentMapper.selectStudentByLikeAndPage] - ==>  Preparing: SELECT * FROM STUDENT_INFO WHERE 1=1 AND STUDENT_SEX=? 
2018-02-17 16:15:32,263 [main] DEBUG [com.znsd.mapper.StudentMapper.selectStudentByLikeAndPage] - ==> Parameters: 男(String)
2018-02-17 16:15:32,283 [main] DEBUG [com.znsd.mapper.StudentMapper.selectStudentByLikeAndPage] - <==      Total: 7
```

注释掉设置的sex，改为设置age

```java
		student.setStudentAge("10");
//		student.setStudentSex("男");
		map.put("student", student);
```

执行的结果，sql为SELECT * FROM STUDENT_INFO WHERE 1=1 AND STUDENT_AGE=? 

```java
2018-02-17 16:19:05,325 [main] DEBUG [com.znsd.mapper.StudentMapper.selectStudentByLikeAndPage] - ==>  Preparing: SELECT * FROM STUDENT_INFO WHERE 1=1 AND STUDENT_AGE=? 
2018-02-17 16:19:05,357 [main] DEBUG [com.znsd.mapper.StudentMapper.selectStudentByLikeAndPage] - ==> Parameters: 10(String)
2018-02-17 16:19:05,378 [main] DEBUG [com.znsd.mapper.StudentMapper.selectStudentByLikeAndPage] - <==      Total: 9
```

当两个都设置

```java
student.setStudentAge("10");
student.setStudentSex("男");
map.put("student", student);
```

执行的结果，sql为SELECT * FROM STUDENT_INFO WHERE 1=1 AND STUDENT_SEX=? 

```java
2018-02-17 16:22:10,535 [main] DEBUG [com.znsd.mapper.StudentMapper.selectStudentByLikeAndPage] - ==>  Preparing: SELECT * FROM STUDENT_INFO WHERE 1=1 AND STUDENT_SEX=? 
2018-02-17 16:22:10,565 [main] DEBUG [com.znsd.mapper.StudentMapper.selectStudentByLikeAndPage] - ==> Parameters: 男(String)
2018-02-17 16:22:10,584 [main] DEBUG [com.znsd.mapper.StudentMapper.selectStudentByLikeAndPage] - <==      Total: 7
```

choose相当于java中的if else，只会执行满足条件的第一个

## where

如果我们想用多个条件，让sex和age都能假如，就要用多个if

```xml
<select id="selectStudentByLikeAndPage" resultMap="studentBeanMap">
    SELECT * FROM STUDENT_INFO
    WHERE 1=1
    <if test="student!=null and student.studentSex!=null and student.studentSex!=''">
        AND STUDENT_SEX=#{student.studentSex}
    </if>
    <if test="student!=null and student.studentAge!=null and student.studentAge!=''">
        AND STUDENT_AGE=#{student.studentAge}
    </if>
</select>
```

执行的结果,sql为SELECT * FROM STUDENT_INFO WHERE 1=1 AND STUDENT_SEX=? AND STUDENT_AGE=? 

```java
2018-02-17 16:36:05,857 [main] DEBUG [com.znsd.mapper.StudentMapper.selectStudentByLikeAndPage] - ==>  Preparing: SELECT * FROM STUDENT_INFO WHERE 1=1 AND STUDENT_SEX=? AND STUDENT_AGE=? 
2018-02-17 16:36:05,887 [main] DEBUG [com.znsd.mapper.StudentMapper.selectStudentByLikeAndPage] - ==> Parameters: 男(String), 10(String)
2018-02-17 16:36:05,909 [main] DEBUG [com.znsd.mapper.StudentMapper.selectStudentByLikeAndPage] - <==      Total: 5
```

加如我们不写1=1，如果都不匹配，那么sql会变为SELECT * FROM STUDENT_INFO WHERE ，或者只匹配第二条

sql就为SELECT * FROM STUDENT_INFO WHERE AND STUDENT_SEX=? AND STUDENT_AGE=? ，肯定是会报错

只需要在前面加上where节点就可以的了,where节点会在条件有满足的情况下加入WHERE子句,并且会自动干掉多出来的and 或者 or

```xml
<select id="selectStudentByLikeAndPage" resultMap="studentBeanMap">
    SELECT * FROM STUDENT_INFO
    <where>
        <if test="student!=null and student.studentSex!=null and student.studentSex!=''">
            AND STUDENT_SEX=#{student.studentSex}
        </if>
        <if test="student!=null and student.studentAge!=null and student.studentAge!=''">
            AND STUDENT_AGE=#{student.studentAge}
        </if>
    </where>
</select>
```

## set

我们通常需要写这样的修改语句

```xml
<update id="updateStudent" parameterType="studentBean">
    UPDATE STUDENT_INFO SET
    STUDENT_ID=#{studentId},STUDENT_NAME=#{studentName},
    STUDENT_AGE=#{studentAge},STUDENT_SEX=#{studentSex}
    WHERE
    ID=#{id}
</update>
```

如果有的参数没有设置，只设置了要修改的一条属性，那么其他属性都会变成null,这时候就需要用set

```xml
<update id="updateStudent" parameterType="studentBean">
    UPDATE STUDENT_INFO 
    <set>
        <if test="studentId!=null">STUDENT_ID=#{studentId}</if>
        <if test="studentName!=null">student_name=#{studentName}</if>
        <if test="studentAge!=null">student_age=#{studentAge}</if>
        <if test="studentSex!=null">student_sex=#{studentSex}</if>
    </set>
    WHERE
    ID=#{id}
</update>
```

只设置年龄和主键，不设置其他属性

```java
StudentBean student =new StudentBean();
student.setId(1);
student.setStudentAge("15");
Integer rows=new StudentDao().updateStudent(student);
```

那么执行的sql为 UPDATE STUDENT_INFO SET STUDENT_AGE=? WHERE ID=? 就只有student_age不会出现把其他属性设置为null的情况

```java
2018-02-17 17:19:54,524 [main] DEBUG [com.znsd.mapper.StudentMapper.updateStudent] - ==>  Preparing: UPDATE STUDENT_INFO SET STUDENT_AGE=? WHERE ID=? 
2018-02-17 17:19:54,553 [main] DEBUG [com.znsd.mapper.StudentMapper.updateStudent] - ==> Parameters: 15(String), 1(Integer)
2018-02-17 17:19:54,609 [main] DEBUG [com.znsd.mapper.StudentMapper.updateStudent] - <==    Updates: 1
```

## trim

以上的where和set其实都可以用trim来写，当where和set满足不了需求时可以自定义格式,

where的等价trim写法

```xml
<trim prefix="WHERE" prefixOverrides="AND | OR">
    <if test="student!=null and student.studentSex!=null and student.studentSex!=''">
        AND STUDENT_SEX=#{student.studentSex}
    </if>
    <if test="student!=null and student.studentAge!=null and student.studentAge!=''">
        AND STUDENT_AGE=#{student.studentAge}
    </if>
</trim>
```

set的等价写法

```xml
<trim prefix="SET" prefixOverrides=",">
    <if test="studentId!=null">STUDENT_ID=#{studentId}</if>
    <if test="studentName!=null">STUDENT_NAME=#{studentName}</if>
    <if test="studentAge!=null">STUDENT_AGE=#{studentAge}</if>
    <if test="studentSex!=null">STUDENT_SEX=#{studentSex}</if>
</trim>
```

prefix代表前缀，当存在条件满足时加入前缀,prefixOverrides的属性代表分割符号，多的干掉，不写会补.

## foreach

当你需要一次添加多个，或者删除多个，或查询处于某个集合中的对象时，用foreach

之前删除多个或查询集合我们可能会加这样的条件  where id in (66,67,68)

现在可以这样写,collection的属性如果传的是List必须填list,数组必须填array，item就是给取出来的对象命名，随便起

open是前缀，close是后缀，separator是分隔符

```xml
<delete id="deleteStudent">
    DELETE FROM
    STUDENT_INFO WHERE ID IN
    <foreach collection="list" item="item" open="(" separator="," close=")">
        #{item}
    </foreach>
</delete>
```

测试

```java
List<Integer> list=new ArrayList<Integer>();
list.add(8);
list.add(9);
list.add(10);
Integer rows=new StudentDao().deleteStudent(list);
```

结果,执行的sql为DELETE FROM STUDENT_INFO WHERE ID IN ( ? , ? , ? ) 

```java
2018-02-17 17:54:47,141 [main] DEBUG [com.znsd.mapper.StudentMapper.deleteStudent] - ==>  Preparing: DELETE FROM STUDENT_INFO WHERE ID IN ( ? , ? , ? ) 
2018-02-17 17:54:47,171 [main] DEBUG [com.znsd.mapper.StudentMapper.deleteStudent] - ==> Parameters: 8(Integer), 9(Integer), 10(Integer)
2018-02-17 17:54:47,201 [main] DEBUG [com.znsd.mapper.StudentMapper.deleteStudent] - <==    Updates: 1
```

批量添加,同理

```xml
<insert id="insertStudents">
    INSERT INTO
    STUDENT_INFO(STUDENT_ID,STUDENT_NAME)
    VALUES
    <foreach collection="list" item="student" separator=",">
        (#{student.studentId},#{student.studentName})
    </foreach>
</insert>
```

## sql片段

把之前的where节点的if全部抽出来,定义一个sql节点

```xml
	<sql id="select_condition">
		<if test="student!=null and student.studentSex!=null and student.studentSex!=''">
			AND STUDENT_SEX=#{student.studentSex}
		</if>
		<if test="student!=null and student.studentAge!=null and student.studentAge!=''">
			AND STUDENT_AGE=#{student.studentAge}
		</if>
	</sql>
```

其他的statement就可用includ引用这个sql片段,引用别的文件需要加namespace

```xml
	<select id="selectStudentByLikeAndPage" resultMap="studentBeanMap">
		SELECT * FROM STUDENT_INFO
		<where>
			<include refid="select_condition"/>
		</where>
	</select>
```

