# 概述

quartz 是一款企业级的任务调度框架。

# 基本使用

引入依赖

```xml
<dependency>
    <groupId>org.quartz-scheduler</groupId>
    <artifactId>quartz</artifactId>
    <version>2.3.0</version>
</dependency>
```

启动一个默认调度器

```java
public static void main(String[] args) throws Exception {
    Scheduler scheduler = StdSchedulerFactory.getDefaultScheduler();
    scheduler.start();
    scheduler.shutdown();
}
```

此时没有任何任务执行，我们现在来创建一个任务。

任务类 实现 `org.quartz.Job` 接口。

```java
@Slf4j
public class HelloJob implements Job {

    @Override
    public void execute(JobExecutionContext jobExecutionContext) throws JobExecutionException {
        log.info("hello quartz");
    }
}
```

通过实现的任务类，创建任务详情 `JobDetail`。

创建调度规则 `SimpleScheduleBuilder` ，每15秒执行一次，永不停止。

创建触发器 `Trigger` 立即触发当前任务。

最后注册进调度器。注释掉shutdown，让任务能一直跑下去，此处不能使用 junit ，junit会执行完测试方法后结束进程。

```java
public static void main(String[] args) throws Exception {
    Scheduler scheduler = StdSchedulerFactory.getDefaultScheduler();
    scheduler.start();

    JobDetail jobDetail = JobBuilder.newJob(HelloJob.class)
        .build();

    SimpleScheduleBuilder scheduleBuilder = SimpleScheduleBuilder.simpleSchedule()
        .withIntervalInSeconds(15)
        .repeatForever();

    Trigger trigger = TriggerBuilder.newTrigger()
        .startNow()
        .withSchedule(scheduleBuilder)
        .build();

    scheduler.scheduleJob(jobDetail,trigger);
    //scheduler.shutdown();
}
```

查看日志,每15s执行一次

![](img/q1.png)

## 传递参数

创建任务详情时设置参数，job 通过 `JobDataMap`获取。

```java
JobDetail jobDetail = JobBuilder.newJob(HelloJob.class)
    .usingJobData("username","张三")
    .usingJobData("userAge",12)
    .build();
```

获取

```java
@Slf4j
public class HelloJob implements Job {

    @Override
    public void execute(JobExecutionContext context) throws JobExecutionException {
        JobDataMap jobDataMap = context.getJobDetail().getJobDataMap();
        String username = jobDataMap.getString("username");
        int userAge = jobDataMap.getIntValue("userAge");

        log.info("hello quartz,name:"+username+",age:"+userAge);
    }
}
```

