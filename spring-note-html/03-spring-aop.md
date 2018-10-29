# aop

## 基于xml

1导入约束 需要`aop`的约束 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans 
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context 
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/aop 
        http://www.springframework.org/schema/aop/spring-aop.xsd">
</beans>
```

2，导入`jar`包,加上spring的`aop`的和`aspect`的jar包,还需要`aopalliance`,`aspectjrt`,`aspectweaver`三个包

3，写扩展类的代码,并在`xml`中配置

```java
public class UserDaoEx {    
    public void before() {
        System.out.println("UserDaoEx.before()");
    }
    
    public void after() {
        System.out.println("UserDaoEx.after()");
    } 
  //环绕方法要加参数ProceedingJoinPoint,调用proceed方法相当于执行切点的方法
    public void around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
        System.out.println("UserDaoEx.around() 前");
        proceedingJoinPoint.proceed();
        System.out.println("UserDaoEx.around() 后");
    }
}
```

配置

```xml
<bean id="userDaoEx" class="com.znsd.dao.impl.UserDaoEx"></bean>
```

4，配置切面和切点 

切点可以理解为一个方法,切面是一个类,`execution(<访问修饰符>?<返回类型><方法名>(<参数>)<异常>)`

```xml
<aop:config>
    //定义切点 并起个id        表达式为执行方法时，
    <aop:pointcut expression="execution(* com.znsd.dao.impl.UserDaoImpl.add(..))" id="daoAdd"/>
    
    //定义切面,引用一个扩展类
    <aop:aspect ref="userDaoEx">
        //在一个切点执行前时,调用这个切面引用类的before方法
        <aop:before method="before" pointcut-ref="daoAdd"/>
        //后置通知
        <aop:after-returning method="after" pointcut-ref="daoAdd"/>
        //环绕这个切点
        <aop:around method="around" pointcut-ref="daoAdd"/>
    </aop:aspect>
</aop:config>
```

## 基于注解

1,在`xml`中开启注解切面 

```xml
 <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
```

2,把扩展的类定义成切面,加上`@Aspect`,为方法加上对应的注解b 

```java
@Aspect
@Service
public class UserDaoEx {
​
    //前置通知
    @Before(value = "execution(* com.znsd.dao.impl.UserDaoImpl.add(..))")
    public void before() {
        System.out.println("UserDaoEx.before()");
    }
    //后置
    @AfterReturning(value = "execution(* com.znsd.dao.impl.UserDaoImpl.add(..))")
    public void after() {
        System.out.println("UserDaoEx.after()");
    }
    //环绕
    @Around(value = "execution(* com.znsd.dao.impl.UserDaoImpl.add(..))")
    public void around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
        System.out.println("UserDaoEx.around() 前");
        proceedingJoinPoint.proceed();
        System.out.println("UserDaoEx.around() 后");
    }
}
```

