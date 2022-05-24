# Apache Commons CSV



# Open CSV

解析csv的开源库，官网 `http://opencsv.sourceforge.net/`

```xml
<dependency>
    <groupId>com.opencsv</groupId>
    <artifactId>opencsv</artifactId>
    <version>5.6</version>
</dependency>
```

## 写

写入 bean 或者 beanList 

```java
import com.opencsv.bean.StatefulBeanToCsv;
import com.opencsv.bean.StatefulBeanToCsvBuilder;

//设置为true表示增量添加，否则为覆盖
try (Writer writer = new FileWriter("test.csv",true)){
    StatefulBeanToCsv<Student> beanToCsv = new StatefulBeanToCsvBuilder<Student>(writer).build();
    beanToCsv.write(student);
} catch (Exception e) {
    log.error("写入csv失败",e);
}
```

可以通过注解`@CsvBindByPosition`来排序属性，不需要的属性可以用 `@CsvIgnore` 忽略

```java
@Data
public class Student {

    @CsvBindByPosition(position = 0)
    private String name;

    //@CsvIgnore
    @CsvBindByPosition(position = 1)
    private Integer age;
```

