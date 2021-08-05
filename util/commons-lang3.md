# 随机字符串

```java
import org.apache.commons.lang3.RandomStringUtils;

@Test
void test1(){
	// 字母大小写 ,MSOpyFlW
    String randomAlphabetic = RandomStringUtils.randomAlphabetic(8);
    log.info("randomAlphabetic:{}",randomAlphabetic); 
	// 字母大小写数字 ,A3EOjPKm
    String randomAlphanumeric = RandomStringUtils.randomAlphanumeric(8);
    log.info("randomAlphanumeric:{}", randomAlphanumeric);
    
}
```

