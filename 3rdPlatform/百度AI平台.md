# 概述

百度AI平台 ，https://ai.baidu.com/  ，提供了各种 ai 的开放功能，如语音识别，人脸识别，文字识别，图像识别。

## 接入准备

注册百度账号，登录控制台  https://console.bce.baidu.com/  

添加 sdk 依赖，项目里使用了slf4j做日志，需要排除调自带的日志实现。

```xml
<dependency>
    <groupId>com.baidu.aip</groupId>
    <artifactId>java-sdk</artifactId>
    <version>4.15.4</version>
    <exclusions>
        <exclusion>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-simple</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

# 使用

大部分情况下，每个功能都会以应用的形式存在，需要先创建应用，获取到 ，进行调用

1，选择想要添加的业务

![](img/1.png)

2, 点击创建应用

![](img/2.png)

3, 创建应用，默认会全部勾选对应的一级功能下的所有子功能。

![](img/3.png)

4, 返回应用列表，拿到对应的  AppId ,APIKey, SecretKey

![](img/4.png)

5,编写代码，构建 Client 实例

比如 用作文字识别的Client 为 AipOcr  ，使用三个参数构建。

```java
String appId = "123";
String apiKey = "aaa";
String secretKey = "bbbb";

// 初始化一个AipOcr
AipOcr aipOcr = new AipOcr(appId, apiKey, secretKey);

// 可选：设置网络连接参数
aipOcr.setConnectionTimeoutInMillis(2000);
aipOcr.setSocketTimeoutInMillis(60000);
```

该实例最好为单例，建议在spring容器中配置为bean。

使用时,调用对应接口方法即可。

本质上都是发起 http 请求，但是使用sdk的接口，可以省去一些操作，比如交换 Access Key ，封装请求参数，图片转base64编码等。

接口返回的都是 `org.json.JSONObject` 需要自己处理参数，详见 官方的接口文档。

## 文字识别

官方文档 ，https://ai.baidu.com/ai-doc/OCR/

### 卡证识别

#### 营业执照

```java
String path = "f://demoBL.jpg";
JSONObject res = aipOcr.businessLicense(path, new HashMap<>());
BaiduBusinessLicenseVo baiduBusinessLicenseVo = transToBusinessLicense(res);
```

转换的方法

```java
private BaiduBusinessLicenseVo transToBusinessLicense(JSONObject res){
    System.out.println(res);
    BaiduBusinessLicenseVo baiduBusinessLicenseVo = new BaiduBusinessLicenseVo();
    try {
        JSONObject wordsResult = res.getJSONObject("words_result");
        baiduBusinessLicenseVo.setCompanyName(wordsResult.getJSONObject("单位名称").getString("words"));
        baiduBusinessLicenseVo.setLegalPerson(wordsResult.getJSONObject("法人").getString("words"));
        baiduBusinessLicenseVo.setAddress(wordsResult.getJSONObject("地址").getString("words"));
        baiduBusinessLicenseVo.setValidityTerm(wordsResult.getJSONObject("有效期").getString("words"));
        baiduBusinessLicenseVo.setDocumentNumber(wordsResult.getJSONObject("证件编号").getString("words"));
        baiduBusinessLicenseVo.setSocialCreditCode(wordsResult.getJSONObject("社会信用代码").getString("words"));
        baiduBusinessLicenseVo.setOrganizationType(wordsResult.getJSONObject("组成形式").getString("words"));
        baiduBusinessLicenseVo.setBusinessScope(wordsResult.getJSONObject("经营范围").getString("words"));
        baiduBusinessLicenseVo.setRegisteredCapital(wordsResult.getJSONObject("注册资本").getString("words"));
        baiduBusinessLicenseVo.setPaidInCapital(wordsResult.getJSONObject("实收资本").getString("words"));
        baiduBusinessLicenseVo.setEstablishmentDate(wordsResult.getJSONObject("成立日期").getString("words"));
        baiduBusinessLicenseVo.setTaxRegistrationNumber(wordsResult.getJSONObject("税务登记号").getString("words"));
        baiduBusinessLicenseVo.setRegistrationAuthority(wordsResult.getJSONObject("登记机关").getString("words"));
        baiduBusinessLicenseVo.setType(wordsResult.getJSONObject("类型").getString("words"));
    } catch (JSONException e) {
        System.out.println("解析失败,errorMsg:{},errorCode:{}");
    }
    return baiduBusinessLicenseVo;
}
```

对应实体类

```java
@Data
public class BaiduBusinessLicenseVo {

    /**
     * 单位名称
     */
    private String companyName;

    /**
     * 法人
     */
    private String legalPerson;

    /**
     * 地址
     */
    private String address;

    /**
     * 有效期
     */
    private String validityTerm;

    /**
     * 证件编号
     */
    private String documentNumber;

    /**
     * 社会信用代码
     */
    private String socialCreditCode;

    /**
     * 组成形式
     */
    private String organizationType;

    /**
     * 经营范围
     */
    private String  businessScope;

    /**
     * 注册资本
     */
    private String registeredCapital;

    /**
     * 实收资本
     */
    private String  paidInCapital;

    /**
     * 成立日期
     */
    private String establishmentDate;

    /**
     * 税务登记号
     */
    private String taxRegistrationNumber;

    /**
     * 登记机关
     */
    private String registrationAuthority;

    /**
     * 类型
     */
    private String type;
}
```

