---
title: SpringBoot下使用Quartz设置定时任务
date: 2023-11-28 11:46
tags: Java
---
### **基础使用**

Quartz 的核心类有以下三部分：

- **任务 Job ：** 需要实现的任务类，实现 `execute()` 方法，执行后完成任务。
- **触发器 Trigger ：** 包括 `SimpleTrigger` 和 `CronTrigger`。
- **调度器 Scheduler ：** 任务调度器，负责基于 `Trigger`触发器，来执行 Job任务。

### 添加依赖
```xml
<!-- 核心包 -->
<dependency>
    <groupId>org.quartz-scheduler</groupId>
    <artifactId>quartz</artifactId>
    <version>2.3.0</version>
</dependency>
<!-- 工具包 -->
<dependency>
    <groupId>org.quartz-scheduler</groupId>
    <artifactId>quartz-jobs</artifactId>
    <version>2.3.0</version>
</dependency>
```
### demo
QuartzConfig类
这个类用来进行任务描述，进行定时执行并将对应的执行逻辑类进行绑定
```java
import org.quartz.*;  
import org.springframework.context.annotation.Bean;  
import org.springframework.context.annotation.Configuration;  
  
/**  
 * 定义任务描述和具体的执行时间  
 */  
@Configuration  
public class QuartzConfig {  
    @Bean    public JobDetail jobDetail() {  
        //指定任务描述具体的实现类  
        return JobBuilder.newJob(IDCardWarningJob.class)  
                // 指定任务的名称  
                .withIdentity("IDCardWarningJob")  
                // 任务描述  
                .withDescription("任务描述：身份预警更新表")  
                // 每次任务执行后进行存储  
                .storeDurably()  
                .build();  
    }  
  
    @Bean  
    public Trigger trigger() {  
        //创建触发器  
        return TriggerBuilder.newTrigger()  
                // 绑定工作任务  
                .forJob(jobDetail())  
//                .withSchedule(CronScheduleBuilder.dailyAtHourAndMinute(1, 0))  // 设置每天凌晨1点触发一次任务  
                .withSchedule(CronScheduleBuilder.cronSchedule("0 0 1 * * ?"))  // 设置每天凌晨1点触发一次任务  
                .build();  
    }  
}
```
demoJob类
这个类负责定时任务的逻辑处理（execute方法中写代码逻辑）
```java
import com.alibaba.druid.support.logging.Log;  
import com.alibaba.druid.support.logging.LogFactory;  
import com.iknight.cost.service.IdentityWarningService;  
import org.quartz.Job;  
import org.quartz.JobExecutionContext;  
import org.quartz.JobExecutionException;  
import org.springframework.beans.factory.annotation.Autowired;  
  
  
public class IDCardWarningJob implements Job {  
    private static final Log logger = LogFactory.getLog(IDCardWarningJob.class);  
    @Autowired  
    private IdentityWarningService service;  
    public void setClaimPostServiceImpl(IdentityWarningService service) {  
        this.service = service;  
    }  
  
    @Override  
    public void execute(JobExecutionContext context) throws JobExecutionException {  
        service.updateDate();  
    }  
}
```

关于cron表达式可以跳转这个连接->[表达式生成器](https://qqe2.com/cron)
