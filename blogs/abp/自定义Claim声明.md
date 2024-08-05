---
title: ABP vNext 扩展 CurrentUser，自定义Claim声明
date: 2023-12-08 16:04:21
tags:
 - .net
 - abp vnext
---


ABP内置Users表，我们可以对其字段进行扩展，辅助进行更详细的数据记录
### ICurrentUser 是主要的服务,用于获取有关当前活动的用户信息.
		以下是 ICurrentUser 接口的基本属性:
		
		 1. IsAuthenticated 如果当前用户已登录(已认证),则返回 true. 如果用户尚未登录,则 Id 和 UserName
		 2. 将返回 null. 	Id (Guid?): 当前用户的Id,如果用户未登录,返回 null. 	UserName (string):
		 3. 当前用户的用户名称. 如果用户未登录,返回 null. 	TenantId (Guid?): 当前用户的租户Id. 对于多租户
		 4. 应用程序很有用. 如果当前用户未分配给租户,返回 null. 	Email (string): 当前用户的电子邮件地址.
		 5. 如果当前用户尚未登录或未设置电子邮件地址,返回 null. 	EmailVerified (bool):
		 6. 如果当前用户的电子邮件地址已经过验证,返回 true. 	PhoneNumber (string): 当前用户的电话号码.
		 7. 如果当前用户尚未登录或未设置电话号码,返回 null. 	PhoneNumberVerified (bool):
		 8. 如果当前用户的电话号码已经过验证,返回 true. 	Roles (string[]): 当前用户的角色.
		 9. 返回当前用户角色名称的字符串数组.
### 如何将扩展字段加入ICurrentUser ：
	找到Domain类库下的 IdentityServerDataSeedContributor.cs，增加想要扩展的字段别名
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/ac5c24bd3a98a0b95a173bc067e1e3f5.png)
在Application类库创建MyUserClaimsPrincipalFactory工厂
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/4d62dced7b0990d500f6a9e24fb20d3d.png)

```csharp
namespace Creating.Drgs.Hospital.IdentityServer
{
	[Volo.Abp.DependencyInjection.Dependency(ReplaceServices = true)]
	[ExposeServices(typeof(AbpUserClaimsPrincipalFactory))] // 替换旧的AbpUserClaimsPrincipalFactory
	public class MyUserClaimsPrincipalFactory : AbpUserClaimsPrincipalFactory, IScopedDependency
	{
		public MyUserClaimsPrincipalFactory(){}
		public override async Task<ClaimsPrincipal> CreateAsync(Volo.Abp.Identity.IdentityUser user)
		{
			//获取当前登录人信息
			var principal = await base.CreateAsync(user);
			var identityPrincipal = principal.Identities.First();
			//扩展信息
			identityPrincipal.AddClaim(new Claim("doctor",  user.Name));
			return principal;
		}
	}
}
```
	FindClaim: 获取给定名称的声明,如果未找到返回 null
	FindClaims: 获取具有给定名称的所有声明(允许具有相同名称的多个声明值).
	FindClaimValue: 获取具有给定名称的声明的值,如果未找到返回 null. 它有一个泛型重载将值强制转换为特定类型.
	CurrentUser.FindClaimValue("doctor")  //读取扩展信息

[ABP Framework 中文文档](https://www.bookstack.cn/read/abp-4.3-zh/087a6ad052a321b0.md)