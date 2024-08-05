---
title: ABP vNext 审计日志使用
date: 2024-07-22 11:53:35
tags:
 - .net
 - abp vnext
---


# 关于审计日志

审计跟踪（也称为审核日志）是一个安全相关的时间顺序记录，记录这些记录的目的是为已经影响在任何时候的详细操作，提供程序运行的证明文件记录、源或事件。

ABP提供了能够为应用程序交互自动记录日志的基础设施，它能记录你调用的方法的调用者信息和参数信息。从根本上来说，存储区域包含：

tenant id（相关的租户Id），

user id（请求用户Id），

server name（请求的服务名称【调用方法对应的类】），

method name（调用方法名称），

parameters（方法的参数【JSON格式】），

execution time（执行时间），

duration （执行耗时时间【通常是毫秒】），

IP address （客户端IP地址），

computer name（客户机名称），

exception （异常【如果方法抛出异常】）等信息。

有了这些信息，我们不仅能够知道谁进行了操作，还能够估算出应用程序的性能及抛出的异常。甚至更多的，你可以得到有关应用程序的使用情况统计。

## 开启审计日志
```csharp
Configure<AbpAuditingOptions>(options =>
{
    options.IsEnabled = true;  //启动审计
    options.IsEnabledForGetRequests = true;  //启用记录get请求
});
```
## 如何自定义审计日志

 AuditingStore: 实现了IAuditingStore接口,实现了将AuditLog的信息保存到数据库的功能
```csharp
 public class AuditingStore : IAuditingStore, ITransientDependency
 {
	public async Task SaveAsync(AuditLogInfo auditInfo)
	{
	}
}
```

// 替换掉默认的服务
```csharp
context.Services.Replace(ServiceDescriptor.Singleton<IAuditingStore, AuditingStore>());
```

