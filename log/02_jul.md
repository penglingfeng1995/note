# JUL

jul 是jdk自带的api，无需引入任何依赖

```java
import org.junit.Test;

import java.util.logging.Level;
import java.util.logging.Logger;

public class JULTest {

    private static final Logger LOG = Logger.getLogger("com.plf.zoe.test.JULTest");

    @Test
    public void test1(){
        LOG.log(Level.INFO,"jul测试");
        LOG.log(Level.WARNING,"异常",new Exception("jul---"));
    }
}

```

