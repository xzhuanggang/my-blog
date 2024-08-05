---
title: ABP vNext 身份验证登录过滤器
date: 2023-12-28 10:53:48
tags:
 - .net
 - abp vnext
---


## 定义登录过滤器
通过过滤器控制身份认证
```csharp
    public class LicenseResourceOwnerPasswordValidator : AbpResourceOwnerPasswordValidator
    {
        Utility.Util util = new Utility.Util();
        public LicenseResourceOwnerPasswordValidator(UserManager<Volo.Abp.Identity.IdentityUser> userManager,
         SignInManager<Volo.Abp.Identity.IdentityUser> signInManager,
         IdentitySecurityLogManager identitySecurityLogManager, 
         ILogger<ResourceOwnerPasswordValidator<Volo.Abp.Identity.IdentityUser>> logger, 
         IStringLocalizer<AbpIdentityServerResource> localizer,
         IOptions<AbpIdentityOptions> abpIdentityOptions,
         IHybridServiceScopeFactory serviceScopeFactory,
         IOptions<IdentityOptions> identityOptions) 
        : base(userManager, signInManager, identitySecurityLogManager, logger, localizer, abpIdentityOptions, serviceScopeFactory, identityOptions)
        {
        }

        public override Task ValidateAsync(ResourceOwnerPasswordValidationContext context)
        {
            DateTime expiration_date = Convert.ToDateTime(util.KeyValue("Expiration_Date"));///过期时间
            DateTime startdate = DateTime.Now;
            int day = expiration_date.Subtract(startdate).Days;
            //授权验证
#if !DEBUG
           
                if (expiration_date >= DateTime.Now)
                {
                        var Validate = base.ValidateAsync(context);
                        if (context.Result.IsError && (context.Result.ErrorDescription != null && context.Result.ErrorDescription.IndexOf("用户账户已被锁定") > -1))
                        {
                            var user = UserManager.FindByNameAsync(context.UserName);
                            int Minutes = user.Result.LockoutEnd.Value.Subtract(DateTime.Now).Minutes;
                            context.Result = new GrantValidationResult(TokenRequestErrors.InvalidGrant, $"登录失败,用户账户已被锁定.请{Minutes}分钟后再试.");
                        }
                        else if (!context.Result.IsError)
                        {
                            if (day >= 0 && day <= 7)
                            {
                                Logger.LogWarning($"授权时间剩余{day}天！");
                            }
                        }
                        return Validate;
                    
                }
                context.Result = new GrantValidationResult(TokenRequestErrors.InvalidGrant, $"提示信息");
            return Task.FromResult(0);
#else 
            var Validate = base.ValidateAsync(context);
            return Validate;
#endif
        }
```

AbpApplicationModule.cs 注入过滤器
```csharp
  public override void PreConfigureServices(ServiceConfigurationContext context)
 {
	 PreConfigure<IIdentityServerBuilder>(builder =>
	  {
	      builder.AddResourceOwnerValidator<LicenseResourceOwnerPasswordValidator>();
	  });
 	 context.Services.Replace(
    		ServiceDescriptor.Transient<AbpResourceOwnerPasswordValidator, LicenseResourceOwnerPasswordValidator>());
    }
```
