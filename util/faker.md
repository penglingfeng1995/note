# 概述

faker 是一款模拟数据的工具

# 使用

引入

```xml
<dependency>
    <groupId>com.github.javafaker</groupId>
    <artifactId>javafaker</artifactId>
    <version>1.0.2</version>
</dependency>
```

使用

```java
Faker faker = new Faker(Locale.CHINA);

for (int i=0;i<100;i++){
    System.out.println(faker.name().name()+"-"+faker.university().name());
}
```

得到

```
戴致远-南农业大学
覃展鹏-南艺术大学
杨苑博-东技术大学
段鹏-东体育大学
任熠彤-北体育大学
```

# 原理

在源码中有对应的 `zh-CN.yml`  ，枚举了常见的姓氏，名字，拼装而成