#  spring security

### 1.导入依赖

在pom.xml中需要web和config,会自动依赖core包

```xml
<dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-web</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-config</artifactId>
</dependency>
```

### 2,配置过滤器和监听器

在web.xml中，监听器用来加载配置文件，过滤器用来拦截请求

```xml
<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:spring/spring-*.xml</param-value>
</context-param>

<!-- 用来加载以上的配置文件 -->
<listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>

<filter>
    <filter-name>springSecurityFilterChain</filter-name>
    <filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>
</filter>
<!--拦截请求的代理类-->
<filter-mapping>
    <filter-name>springSecurityFilterChain</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

### 3,配置spring配置文件

在resource下创建spring-security.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:security="http://www.springframework.org/schema/security"
	xsi:schemaLocation="http://www.springframework.org/schema/security http://www.springframework.org/schema/security/spring-security-4.1.xsd
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

	<!-- 设置security="none"表示放行的路径 ,放行登录页及静态资源,*是通配符，**可以通配多层路径-->
	<security:http pattern="/*.html" security="none"></security:http>
	<security:http pattern="/css/**" security="none"></security:http>
	<security:http pattern="/js/**" security="none"></security:http>
	<security:http pattern="/plugins/**" security="none"></security:http>
	<security:http pattern="/img/**" security="none"></security:http>
    <!--如果一个请求一直请求不到，看配置是否放行了-->
	<security:http pattern="/seller/add.do" security="none"></security:http>

	<security:http use-expressions="false"><!-- 不使用表达式 -->
		<!-- 设置要拦截的路径，并设置访问权限为ROLE_SELLER （权限名必须以ROLE_开头） -->
		<security:intercept-url pattern="/**" access="ROLE_SELLER" />
		<!-- 设置登录页login-page请求/login即可登录 默认参数为username,password,默认跳转页，登录失败页，并且登录成功总是跳转到默认跳转页 -->
		<security:form-login login-page="/shoplogin.html"
			default-target-url="/admin/index.html" authentication-failure-url="/shoplogin.html"
			always-use-default-target="true" />
		<!-- 如果出现403没有csrf token错误，加上这个配置 -->
		<security:csrf disabled="true" />
		<!-- 设置不拦截iframe -->
		<security:headers>
			<security:frame-options policy="SAMEORIGIN" />
		</security:headers>
		<!-- 设置登出，请求/logout即可注销登录 -->
		<security:logout/>
	</security:http>

	<security:authentication-manager>
        <!--设置权限校验提供者-->
		<security:authentication-provider user-service-ref="userDetailsService">
            <!--配置bcrypt解密器-->
			<security:password-encoder ref="bcryptEncoder"></security:password-encoder>
		</security:authentication-provider>
	</security:authentication-manager>
	<!--配置校验提供者-->
	<bean id="userDetailsService" class="com.pinyougou.manager.service.impl.UserDetailsServiceImpl">
		<property name="sellerService" ref="sellerService"></property>
	</bean>
	<!--实例bcrypt解码器-->
	<bean id="bcryptEncoder" class="org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder"></bean>
</beans>

```

### 4，编写校验提供者

编写的自己的校验服务类,从数据库中查询对象

```java
//实现spring提供的UserDetailsService接口
public class UserDetailsServiceImpl implements UserDetailsService {

	private SellerService sellerService;

	public void setSellerService(SellerService sellerService) {
		this.sellerService = sellerService;
	}
//重写这个方法
	@Override
    //表单输入的username会作为参数传入
	public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
	//我们通过别的服务接口，从数据库查询出整个对象
		TbSeller seller = sellerService.findOneById(username);
        //创建权限列表
		List<GrantedAuthority> authorities = new ArrayList<>();
        //添加一个ROLE_SELLER权限
		authorities.add(new SimpleGrantedAuthority("ROLE_SELLER"));
        //创建spring提供的UserDetails接口的实现类，参数分别文username，password,权限list
		User user = new User(seller.getSellerId(), seller.getPassword(), authorities);
		return user;
	}

}
```

### 5,添加用户的时候加密

```java
//实例加密器
BCryptPasswordEncoder passwordEncoder=new BCryptPasswordEncoder();
//用encode方法对密码进行加密并设置回去
seller.setPassword(passwordEncoder.encode(seller.getPassword()));
```

### 6,得到当前用户信息

通过SecurityContextHolder得到当前身份

```java
@RequestMapping("getLoginName")
public Map getLoginName() {
    String name = SecurityContextHolder.getContext().getAuthentication().getName();
    Map map=new HashMap<>();
    map.put("loginName", name);
    return map;
}
```













