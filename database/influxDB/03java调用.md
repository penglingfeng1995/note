# 配置

我们使用springboot来自动配置，引入依赖即可

```xml
<dependency>
    <groupId>org.influxdb</groupId>
    <artifactId>influxdb-java</artifactId>
</dependency>
```

配置

```yaml
spring:
  influx:
    url: http://192.169.x.x:8086
```

然后我们注入 InfluxDB 实例即可使用了。

```java
import org.influxdb.InfluxDB;

@Resource
private InfluxDB influxDB;
```

# 添加

直接添加

```java
influxDB.setDatabase("monitor6");

// 创建一条记录，指定表名measurement，设置tag 和 field ,以及时间time
Point point = Point.measurement("test_metric")
    .tag("code", "sms")
    .tag("id", "123")
    .addField("value", 123123)
    .time(Instant.now().toEpochMilli(), TimeUnit.MILLISECONDS)
    .build();
// 写入数据
influxDB.write(point);
```

使用 Bean

```java
import org.influxdb.annotation.Column;
import org.influxdb.annotation.Measurement;
import org.influxdb.annotation.TimeColumn;

@Data
// 必须指定表名
@Measurement(name = "test_metric")
public class TestMetric {
	// 指定tag
    @Column(name = "code",tag = true)
    private String code;

    @Column(name = "id",tag = true)
    private String id;
	// 指定field
    @Column(name = "value")
    private Integer value;
	// 指定 time，默认毫秒
    @TimeColumn
    private Long time;
}
```

```java
TestMetric testMetric = getData();

Point point = Point.measurementByPOJO(TestMetric.class)
    .addFieldsFromPOJO(testMetric)
    .time(Instant.now().toEpochMilli(), TimeUnit.MILLISECONDS)
    .build();

influxDB.write(point);
```

指定保存策略，influxdb一边不进行修改和删除，通过指定策略留存数据

创建表的时候，创建保存策略，以下是在 test1 库中创建了名为 ONE_DAY 的策略，数据保留一天，一个副本，并设置为默认。

```sql
create retention policy "ONE_DAY" on test1 duration 1d replication 1 default;
```

插入时可以指定数据库和策略

```java
influxDB.write("test1","ONE_DAY",point);
```

# 查询



