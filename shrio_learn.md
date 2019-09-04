# 权限管理

## 1 用户认证管理

## 2用户授权管理

~~~
shiro 中的AuthorizingRealm有2个方法doGetAuthorizationInfo()和doGetAuthenticationInfo(),一般实际开发中，

我们都继承AuthorizingRealm类然后重写doGetAuthorizationInfo和doGetAuthenticationInfo。         

doGetAuthenticationInfo这个方法是在用户登录的时候调用的也就是执行SecurityUtils.getSubject().login（）的时候调用；(即:登录验证)

而doGetAuthorizationInfo方法是在我们调用SecurityUtils.getSubject().isPermitted（）这个方法时会调用doGetAuthorizationInfo（），

而我们的@RequiresPermissions这个注解起始就是在执行SecurityUtils.getSubject().isPermitted（）。


我们在某个方法上加上@RequiresPermissions这个，那么我们访问这个方法的时候，就会自动调用SecurityUtils.getSubject().isPermitted（），从而区调用doGetAuthorizationInfo 匹配
 
~~~

