# 概述

SnakeYaml 是springboot中用来解析yml文件的工具包，官方文档 https://bitbucket.org/snakeyaml/snakeyaml-engine/wiki/Documentation

如果是springboot项目，则自带这个依赖

```xml
<dependency>
    <groupId>org.yaml</groupId>
    <artifactId>snakeyaml</artifactId>
    <version>1.27</version>
</dependency>
```

# 使用

## 读取字符串

```java
import org.yaml.snakeyaml.Yaml;

@Test
void testRead(){
    String str = "key: zs";
    Yaml yaml = new Yaml();
    Object load = yaml.load(str);
    System.out.println(load);// {key=zs}
}
```

debug查看，发现返回的是 LinkedHansMap 

## 读取流

```java
@Test
void testRead2() throws FileNotFoundException {
    Yaml yaml = new Yaml();
    Object load = yaml.load(new FileInputStream("src/main/resources/application.yml"));
    System.out.println(load);
}
```

查看发现是一个更复杂的LinkedHansMap 

由于 load 是泛型方法，可以直接指定map接收

```java
Map<String,Object> result = yaml.load(new FileInputStream("src/main/resources/a.yml"));
```

也可以使用 loadAs 转为对象

```java
TStudent result = yaml.loadAs(new FileInputStream("src/main/resources/a.yml"),TStudent.class);
```

得到 `TStudent(id=1, name=zs, age=12, weightKg=98.6, birthday=Mon Dec 12 08:00:00 CST 2022)`

TStudent

```java
@Data
public class TStudent {

    private Integer id;

    private String name;

    private Integer age;

    private BigDecimal weightKg;

    private Date birthday;
}
```

对应 yaml 文件，注意，不能有多余的属性

```yaml
id: 1
name: zs
age: 12
weightKg: 98.6
birthday: 2022-12-12
```

snakeyaml可以识别复杂的对象，如属性为集合，对象等，但是需要保证数据结构正确。

# 导出

通过 dump 方法导出,需调整格式为块结构

```java
@Test
void testDump() throws IOException {
    TStudent student = new TStudent();
    student.setId(2);
    student.setName("王五");
    student.setAge(17);
    student.setWeightKg(new BigDecimal("99.9"));
    student.setBirthday(new Date());
	// 设置格式
    DumperOptions dumperOptions = new DumperOptions();
    dumperOptions.setDefaultFlowStyle(DumperOptions.FlowStyle.BLOCK);
    dumperOptions.setDefaultScalarStyle(DumperOptions.ScalarStyle.PLAIN);

    Yaml yaml = new Yaml(dumperOptions);
    FileWriter fileWriter = new FileWriter("src/main/resources/b.yml");
    yaml.dump(student,fileWriter);
}
```

