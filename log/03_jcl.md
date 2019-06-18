# Commons-logging

## 1,引入依赖

```xml
<dependency>
    <groupId>commons-logging</groupId>
    <artifactId>commons-logging</artifactId>
    <version>1.2</version>
</dependency>
```

## 2, 使用接口

```java
import org.junit.Test;

import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;

public class JCLTest {

    private static final Log LOG= LogFactory.getLog(JCLTest.class);

    @Test
    public void test1(){
        LOG.info("jcl测试");
        LOG.error("异常",new Exception("jcl---"));
    }
}
```

## 3,原理

