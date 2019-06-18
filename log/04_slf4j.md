## 引入依赖

```xml
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>1.7.25</version>
</dependency>
```

## 使用接口

```java
import org.junit.Test;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class Slf4jTest {

    private static final Logger LOG = LoggerFactory.getLogger(Slf4jTest.class);

    @Test
    public void test1(){
        LOG.info("slf4j测试");
        LOG.error("异常",new Exception("slf4j---"));
    }
}
```



