# 引入依赖

6的版本不支持过低版本的mysql，需要换成5的版本。

```xml
<dependency>
    <groupId>org.flywaydb</groupId>
    <artifactId>flyway-core</artifactId>
    <version>6.1.3</version>
</dependency>
```

# 使用

构建一个 Flyway 的实例，至少需要一个数据源。

调用 migrate 方法即可执行迁移。

```java
public static void main(String[] args) {
    Flyway flyway = Flyway.configure().dataSource(getDataSource()).load();
    flyway.migrate();
}
```

# 整合 spring

spring 配置类创建 Flyway 的实例，指定一个数据源，指定 flyway 文件的路径，并在初始化结束时，调用`migrate`方法。

```java
@Bean(initMethod = "migrate")
public Flyway flyway(DataSource dataSource){
    return Flyway.configure().dataSource(dataSource).locations("flyway").load();
}
```

# 整合springboot

整合springboot，只需要把依赖添加即可使用。当然还有数据库的驱动。

```xml
<dependency>
    <groupId>org.flywaydb</groupId>
    <artifactId>flyway-core</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jdbc</artifactId>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>
```

配置一个数据源即可使用

```yaml
spring:
  datasource:
    driver-class-name: com.mysql.jdbc.Driver
    url: jdbc:mysql://192.168.80.128:3306/flyway
    username: root
    password: 123456
```

编写flyway脚本放在 `classpath:db/migration`下

如 `V1__CREATE_STUDENT.sql`

```sql
create table t_student
(
    id bigint auto_increment,
    name varchar(255) null,
    constraint table_name_pk
        primary key (id)
);
```



# 配置项

|          属性名           |                             含义                             |     可选值     |
| :-----------------------: | :----------------------------------------------------------: | :------------: |
|         locations         |   指定flyway脚本所在的目录，默认为`classpath:db/migration`   |                |
|    baseline-on-migrate    | 是否基于已有的数据库进行迁移，默认为false。flyway希望从一个空的数据库开始接管，如果是已经使用过的数据库，则需要开启，否则会被拒绝。 |   true,false   |
| ignore-missing-migrations | 是否忽略已经执行，但是版本低于本地文件最高版本，而本地缺失的脚本。默认为false。例如，历史表中执行了1,2,3三个版本，而本地文件只有1，3两个版本，缺失了2，如果不开启此选项，会被拒绝执行。 |   true,false   |
| ignore-future-migrations  | 是否忽略已经执行，但是版本高于本地文件最高版本，而本地缺失的脚本。默认为true。例如，历史表中执行了1,2,3三个版本，而本地文件只有1，2两个版本，缺失了3，如果不开启此选项，会被拒绝执行。 |   true,false   |
|       out-of-order        | 是否允许乱序执行。默认为false。例如，已经执行了1,2两个版本，但是想添加一个1.1的版本，而该版本是介于两个已经执行的版本之间，顺序乱了，会被拒绝。 |   true,false   |
|          target           | 执行到指定的版本。字符串类型，指定后可以执行到小于等于指定版本号的所有脚本。有两个特殊的值，current,执行到历史记录表中的最新版本，通常配合乱序，添加一些过去版本的脚本。latest，执行到当前本地脚本的最新版本。 | current,latest |
|    validate-on-migrate    | 是否每次都校验 checksum值。默认为true。同样的文件会生成一个checksum值，历史表中也会记录，当文件发生改变时，checksum值会不一样，则会拒绝执行。建议保持开启 |   true,false   |

