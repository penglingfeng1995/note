# 简介

dom4j 是一个优秀的简单易用的 xml 解析库。

# 依赖

旧版,jdk1.4 ,发布依旧，使用广泛。

```xml
<dependency>
    <groupId>dom4j</groupId>
    <artifactId>dom4j</artifactId>
    <version>1.6.1</version>
</dependency>
```

新版,jdk 1.8 支持泛型

```xml
<dependency>
    <groupId>org.dom4j</groupId>
    <artifactId>dom4j</artifactId>
    <version>2.1.3</version>
</dependency>
```

后续使用新版

## 使用

创建 SAXReader 实例，读取一个流，路径，文件等资源，获取 文档对象

```java
import org.dom4j.Document;
import org.dom4j.DocumentException;
import org.dom4j.io.SAXReader;

import java.io.InputStream;

public class App {

    public static void main(String[] args) throws DocumentException {
        SAXReader reader = new SAXReader();
        InputStream inputStream = App.class.getClassLoader().getResourceAsStream("logback.xml");
        Document document = reader.read(inputStream);
    }
}
```

常用方法

```java
// 获取根节点
Element root = document.getRootElement();
// 获取子元素列表
List<Element> elements = root.elements();
// 获取指定标签名称的子元素
Element logger = root.element("logger");
// 获取改元素上的属性
String name = logger.attribute("name").getValue();
```

