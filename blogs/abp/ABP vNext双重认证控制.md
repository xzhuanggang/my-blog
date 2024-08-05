---
title: ABP vNext双重认证控制
date: 2023-12-28 11:07:18
tags:
 - .net
 - abp vnext
---

创建派生类SignInManager.cs
传入用户信息user、用户密码 password、登录失败多少次锁定 lockoutOnFailure
直接返回 SignInResult.Success，可以无需进行其他认证，完成登录
或者可以自定义一个固定验证进行登录，
例如：验证输入密码为0000，直接跳过验证完成身份认证
```csharp
   public class HospitalSignInManager : Microsoft.AspNetCore.Identity.SignInManager<Volo.Abp.Identity.IdentityUser>
   {
       public HospitalSignInManager(UserManager<Volo.Abp.Identity.IdentityUser> userManager, IHttpContextAccessor contextAccessor, IUserClaimsPrincipalFactory<Volo.Abp.Identity.IdentityUser> claimsFactory, IOptions<IdentityOptions> optionsAccessor, ILogger<SignInManager<Volo.Abp.Identity.IdentityUser>> logger, IAuthenticationSchemeProvider schemes, IUserConfirmation<Volo.Abp.Identity.IdentityUser> confirmation) : base(userManager, contextAccessor, claimsFactory, optionsAccessor, logger, schemes, confirmation)
       {
       }

       public async override Task<SignInResult> CheckPasswordSignInAsync(Volo.Abp.Identity.IdentityUser user, string password, bool lockoutOnFailure)
       {
           // 校验password模式，如果匹配password模式匹配加密方式直接放回SignInResult.Success
           if (password == "0000")
           {
               return SignInResult.Success;
           }
           // 否则返回原有逻辑
           return await base.CheckPasswordSignInAsync(user, password, lockoutOnFailure);
       }
   }
```
然后在启动类注入服务

```csharp

        PreConfigure<IdentityBuilder>(identityBuilder => { identityBuilder.AddSignInManager<SignInManager>(); });
```
