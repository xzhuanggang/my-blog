---
title: ABP vNext服务 - 集成Hangfire
date: 2024-07-22 13:51:40
tags:
 - .net
 - abp vnext
---

@[TOC](ABP vNext—集成Hangfire)
# 简介
Hangfire是一个综合性的后台作业管理工具。你可以用Hangfire来替换ABP中默认实现的后台作业管理者。你可以对Hangfire使用相同的后台作业API。因此，你的代码将独立于Hangfire。但是，如果你喜欢，你也可以直接的使用 Hangfire 的API。

## 注入Hnagfire服务
```csharp  
    public static ConnectionMultiplexer Redis= "127.0.0.1:6379";
    
  GlobalConfiguration.Configuration.UseRedisStorage(Redis, new RedisStorageOptions { InvisibilityTimeout = TimeSpan.MaxValue });
    
    context.Services.AddHangfire(configuration =>
    {
     
        configuration.UseRedisStorage(Redis, new RedisStorageOptions { InvisibilityTimeout = TimeSpan.MaxValue });
    });
```

## 启用Hangfire面板，配置后台仪表盘
```csharp
  app.UseHangfireDashboard("/hangfire", new DashboardOptions
  {
      Authorization = new[] { new MyAuthorizationFilter() }
  });
```

## 创建工作任务
```csharp
 public interface ITestWorker : IHangfireBackgroundWorker
 {
 }
 
[ExposeServices(typeof(ITestWorker))]
public class TestWorker : HangfireBackgroundWorkerBase, ITestWorker
{
	 public SynchronousBasicInfoWorker()
	 {
	
	     RecurringJobId = "标题";
	     //CronExpression = "0 6,9,12,15,18,21 * * *";
	 }
	 
	 [JobDisplayName("标题")]
	 [UnitOfWork]
	 public override Task DoWorkAsync()
	 {
	 }
}
```

## 控制任务启动
```csharp
 RecurringJob.AddOrUpdate<TestWorker>(cron.JobName, x => x.DoWorkAsync(), "0 6 * * *", TimeZoneInfo.Local);
```
