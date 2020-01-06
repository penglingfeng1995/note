# 引入依赖

```xml
<dependency>
    <groupId>org.flywaydb</groupId>
    <artifactId>flyway-core</artifactId>
    <version>6.1.3</version>
</dependency>
```

# 配置类

Flyway需要使用自己的方式，得到 Flyway 的实例，指定一个数据源，指定 flyway 文件的路径，并在初始化结束时，调用`migrate`方法。该方法将会执行sql脚本

```java
@Bean(initMethod = "migrate")
public Flyway flyway(DataSource dataSource){
    return Flyway.configure().dataSource(dataSource).locations("flyway").load();
}
```



# 配置flyway脚本

在 刚才指定的 类路径下，编写sql标本，命名`V版本号__描述.sql`，中间是双下划线，会按照版本号依次执行

如 `V20201061343__CREATE_STUDENT.sql`

```sql
create table t_student
(
	studentId bigint auto_increment,
	studentName varchar(255) null,
	studentAge int null,
	constraint t_student_pk
		primary key (studentId)
);
```

