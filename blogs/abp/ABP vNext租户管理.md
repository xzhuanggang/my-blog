---
title: ABP vNext租户管理
date: 2023-12-28 11:26:28
tags:
 - .net
 - abp vnext
---

ABP vNext租户管理是一种管理多个租户的解决方案，它是ABP框架的一部分。ABP vNext租户管理提供了一个集中式的方式来管理多个租户，包括创建、编辑、删除和禁用租户。

ABP vNext租户管理具有以下特点：
1. 多租户支持：可以创建多个租户，并为每个租户分配独立的资源和权限。
2. 租户隔离：每个租户都有自己独立的数据库，数据和功能是彼此隔离的，一个租户的操作不会影响其他租户。
3. 集中管理：提供一个集中化的管理界面，可以在这里添加、编辑和删除租户，并为租户分配资源和权限。
4. 角色分配：可以为每个租户分配角色，并为每个角色分配权限。
5. 结构化数据：支持将租户数据结构化，并为每个租户提供自定义字段。
6. 多租户身份验证：支持为每个租户单独配置身份验证方式，例如使用单一身份验证、多租户身份验证和外部身份验证。

遇到的问题：不分库的情况下，数据的查询感官比较混乱

![abp提供租户管理接口](https://i-blog.csdnimg.cn/blog_migrate/26bbf455d6a76632d4df146e1306c167.png)
“Tenants” 租户信息表
“TenantConnectionStrings”租户关联字符串，用于连接独立数据库
每个租户代表一个管理员，TenantId关联租户信息
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/27b06f028a738e1583869624db591a90.png)
启用租户管理
```csharp
 Configure<AbpMultiTenancyOptions>(options =>
    {
        options.IsEnabled = MultiTenancyConsts.IsEnabled; // IsEnabled设为True;
});
```
实体类继承IMultiTenant，实现接口TenantId（租户Id）
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/48488d33b5e80ae09e81113673a0c021.png)
租户登录 header参数需要增加__tenant ，租户Id
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/54459cfb37aa71b0cc37503016fc7b24.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/07db9be2d59da72c293229d3144cc8fc.png)
