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
public static void main(String[] args) {
    try {
        Scheduler scheduler = StdSchedulerFactory.getDefaultScheduler();
        scheduler.start();
	    // 在这直接编写任务
        scheduler.shutdown();
    } catch (SchedulerException e) {
        e.printStackTrace();
    }
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

调度器 Scheduler 进行一个调度任务，由两部分构成

`JobDetail` 任务详情，指定了任务类，任务名称，任务组，和执行的任务的参数。

`Trigger` 触发器，指定了触发规则和调度规则

```java
public static void main(String[] args) {
    try {
        Scheduler scheduler = StdSchedulerFactory.getDefaultScheduler();
        scheduler.start();
		// 创建任务详情
        JobDetail jobDetail = JobBuilder.newJob(HelloJob.class)
            .withIdentity("job1", "group1")
            .build();
		// 创建触发器
        Trigger trigger = TriggerBuilder.newTrigger()
            .withIdentity("trg1", "group1")
            .startNow()
            .withSchedule(SimpleScheduleBuilder.simpleSchedule()
                          .withIntervalInSeconds(10)
                          .repeatForever())
            .build();
		// 进行调度
        scheduler.scheduleJob(jobDetail,trigger);

        //scheduler.shutdown();
    } catch (SchedulerException e) {
        e.printStackTrace();
    }
}
```

以上 `JobDetail`任务详情指定 `HelloJob` 为任务类，和任务组，任务名

触发器 `Trigger` 设置了触发器名称，触发器组，触发方式（立即启动），调度规则（每10s执行一次,不停止）

查看日志,每10s执行一次

![](img/q1.png)

# 传递参数

创建任务详情时设置参数` JobDataMap`,也可以直接设置`JobData`，job 通过 `JobDetail`获取。

```java
JobDataMap jobDataMap=new JobDataMap();
jobDataMap.put("username","张三");
jobDataMap.put("age",12);

JobDetail jobDetail = JobBuilder.newJob(HelloJob.class)
    .withIdentity("job1", "group1")
    .usingJobData(jobDataMap)
    .usingJobData("address","北京")
    .build();
```

通过`JobExecutionContext`获取

```java
@Slf4j
public class HelloJob implements Job {
    @Override
    public void execute(JobExecutionContext jobExecutionContext) throws JobExecutionException {
        JobDataMap jobDataMap = jobExecutionContext.getJobDetail().getJobDataMap();

        String username = jobDataMap.getString("username");
        int age = jobDataMap.getInt("age");
        String address = jobDataMap.getString("address");

        log.info("hello,{},{},{}",username,age,address);
    }
}
```

如果按照bean规范对Job进行属性设置，并存在 set/get 方法，则创建任务的时候`JobFactory`会自动对这些属性赋值

```java
@Slf4j
@Data
public class HelloJob implements Job {

    private String username;
    private Integer age;
    private String address;

    @Override
    public void execute(JobExecutionContext jobExecutionContext) throws JobExecutionException {
        log.info("hello,{},{},{}",username,age,address);
    }
    
}
```

# 状态与并发

默认情况下，如果一个任务的执行时间，超过了调度的周期时间，则调度会另起一个线程，继续执行任务。

```java
@Slf4j
public class HelloJob implements Job {
    @Override
    public void execute(JobExecutionContext jobExecutionContext) throws JobExecutionException {
        log.info("start");
        try {
            // 执行消耗设置为5s
            TimeUnit.SECONDS.sleep(5);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        log.info("end");
    }
}

// 调度周期设置为3s
Trigger trigger = TriggerBuilder.newTrigger()
    .withIdentity("trg1", "group1")
    .startNow()
    .withSchedule(SimpleScheduleBuilder.simpleSchedule()
                  .withIntervalInSeconds(3)
                  .repeatForever())
    .build();
```

以上的demo，当过了3s后，会再开一个任务，此时存在多个任务并发执行的场景

![](img/q2.png)

如果只希望同时只存在一个线程执行该job，通过注解`@DisallowConcurrentExecution` 标注。

```java
@Slf4j
@DisallowConcurrentExecution
public class HelloJob implements Job {
```

此时相当于加了同步,第二个任务会等待其他任务执行完才执行。

![](img/q3.png)

如果希望执行完一次任务后，下次任务执行更新后的数据，而不是一开始设置的数据，则需要

使用注解`@PersistJobDataAfterExecution`标注任务类后，数据则会更新。

通常与 `@DisallowConcurrentExecution` 一起使用，防止两个job线程同时修改数据，引发线程安全问题。

```java
@Slf4j
@Data
@PersistJobDataAfterExecution
public class HelloJob implements Job {
    private String username;
    private Integer age;
    private String address;
    @Override
    public void execute(JobExecutionContext jobExecutionContext) throws JobExecutionException {
        log.info("hello,{},{},{}", username, age, address);
		// 执行完后，更新jobData数据
        JobDataMap jobDataMap = jobExecutionContext.getJobDetail().getJobDataMap();
        jobDataMap.put("age", ++age);
    }
}
```

得到

![](img/q4.png)

# Trigger

## 触发时间

可以设置 开始触发时间，和结束触发时间

```java
// 2020年9月4日，17点25分10s
Date startTime = DateBuilder.dateOf(17, 25, 10, 4, 9, 2020);
Date endTime = DateBuilder.dateOf(17, 26, 10, 4, 9, 2020);

Trigger trigger = TriggerBuilder.newTrigger()
    .withIdentity("trg1", "group1")
    .startAt(startTime)
    .endAt(endTime)
    .withSchedule(SimpleScheduleBuilder.simpleSchedule()
                  .withIntervalInSeconds(3)
                  .repeatForever())
    .build();
```

## SimpleTrigger

简单的触发器，可用定义一个任务，什么时候执行，执行几次。

ex: 不做任何设置，立即触发，只执行一次。可用通过以上的设置触发时间

```java
Trigger trigger =TriggerBuilder.newTrigger()
    .withIdentity("trg1", "group1")
    .build();
```

ex: 绑定一个调度规则，此时生成的对象为 SimpleTrigger ，

repeat 表示重复，interval 表示间隔 ，两者需配合设置。只设置间隔，不设置重复，将只执行一次。只设置重复，不设置间隔或间隔为0会报错。

```java
SimpleTrigger trigger = TriggerBuilder.newTrigger()
    .withIdentity("trg1", "group1")
    .withSchedule(SimpleScheduleBuilder.simpleSchedule()
                  .withIntervalInSeconds(3)
                  .repeatForever())
    .build();
```

以上 方法是 `withIntervalInSeconds` 表示每次执行间隔多少秒，类似的方法还有 `withIntervalInMilliseconds` 毫秒，`withIntervalInMinutes` 分钟，`withIntervalInHours` 小时。

`repeatForever()` 表示一直重复，直到结束时间。 还可以使用`withRepeatCount()` 指定重复次数。

```java
SimpleTrigger trigger = TriggerBuilder.newTrigger()
    .withIdentity("trg1", "group1")
    .withSchedule(SimpleScheduleBuilder.simpleSchedule()
                  .withIntervalInMinutes(1)
                  .withRepeatCount(3))
    .build();
```

以上代码，设置重复n次，总共会执行 n+1 次任务。

