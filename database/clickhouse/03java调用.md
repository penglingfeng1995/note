# 使用

使用起来通过jdbc的驱动来调用

引入依赖

```xml
<dependency>
    <groupId>ru.yandex.clickhouse</groupId>
    <artifactId>clickhouse-jdbc</artifactId>
    <version>0.3.2</version>
</dependency>
```

同时引入 mybatis plus 来整合

配置

```yml
spring:
  datasource:
    url: jdbc:clickhouse://localhost:8123/student
    driver-class-name: ru.yandex.clickhouse.ClickHouseDriver
    username: xxx
    password: xxx
```

对应的表

```sql
create table t_student(
 	id UInt32,
 	name String,
 	age UInt32,
 	weight_kg Decimal(4,1),
 	birthday Date,
 	create_time Datetime
) engine = MergeTree
  partition by toYYYYMMDD(create_time)
  primary key(id)
  order by (id,birthday);
```

实体类

```java
@Data
@TableName("t_student")
public class TStudent {

    @TableField("id")
    private Integer id;

    @TableField("name")
    private String name;
    
    @TableField("age")
    private Integer age;
    
    @TableField("weight_kg")
    private BigDecimal weightKg;
    
    @TableField("birthday")
    private Date birthday;
    
    @TableField("create_time")
    private Date createTime;
}
```

通过mapper调用即可