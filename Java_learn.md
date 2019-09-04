## spring学习



### 服务器的三层框架

![1557541047614](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1557541047614.png)



![1557541110703](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1557541110703.png)



Springmvc框架基于组件方式的执行流程

![1557546869441](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1557546869441.png)

## java 三大器

### 1 拦截器

#### 1.1 实例

~~~java
public class HandlerInterceptor1 implements HandlerInterceptor{
         /**

          *  进入handler方法之前执行

          *  用于身份认证，身份校验

          *  比如身份认证，如果认证不通过表示当前用户没有登录，需要此方法拦截不在向下执行

          */

         @Override

         public boolean preHandle(HttpServletRequest arg0, HttpServletResponse arg1, Object arg2) throws Exception {
                   // TODO Auto-generated method stub
                   return false;
         }
         /**

          *  进入handler之后，返回modelandview之前执行

          *  应用场景从modelandview出发，将共用的模型数据（比如菜单导航）在这里传到视图，也可以在这里统一制定视图

          */

         @Override

         public void postHandle(HttpServletRequest arg0, HttpServletResponse arg1, Object arg2, ModelAndView arg3) throws Exception {

                   // TODO Auto-generated method stub
         }
         /**

          *  执行handler完成执行此方法

          *  应用场景：统一异常处理，统一日志处理

          */

         @Override
         public void afterCompletion(HttpServletRequest arg0, HttpServletResponse arg1, Object arg2, Exception arg3)
                            throws Exception {
                   // TODO Auto-generated method stub
         }
}
~~~

#### 1.2配置拦截器

~~~
在springmvc的前端控制器配置文件中配置interceptor拦截器
<!-- 配置拦截器 -->
                 <mvc:interceptors>
              <!-- 多个拦截器,顺序执行 -->
                          <!-- 登录认证拦截器 -->
                           <mvc:interceptor>
                              <mvc:mapping path="/**"/><!-- 标识拦截所有的URL包括所有的子URL -->
                             <bean class="com.jch.interceptor.LoginInterceptor"></bean>
                           </mvc:interceptor>
                 </mvc:interceptors>
~~~

#### 1.3 登录拦截实现

~~~
public class LoginInterceptor implements HandlerInterceptor{

 

         /**

          *  进入handler方法之前执行

          *  用于身份认证，身份校验

          *  比如身份认证，如果认证不通过表示当前用户没有登录，需要此方法拦截不在向下执行

          */

         @Override

         public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {

                   // TODO Auto-generated method stub

                   // 获取请求的URL

                   String url = request.getRequestURI();

                   // 判断是否是公开地址（实际使用将公开地址配置在配置文件中）这里公开地址就是登录提交的地址

                   if(url.indexOf("login.jch")>=0) {

                            // 如果要登录提交，那么放行

                            return true;

                   }

                   // 判断session

                   HttpSession session = request.getSession();

                   String username = (String)session.getAttribute("username");

                   if(username != null) {

                            // 有身份信息，放行

                            return true;

                   }

                  

                   // 身份验证不通过跳转到登录界面

                   request.getRequestDispatcher("/views/home/login.jsp").forward(request, response);

                   System.out.println("这是第1个拦截器，preHandle方法");

                   return false;

         }

        

         /**

          *  进入handler之后，返回modelandview之前执行

          *  应用场景从modelandview出发，将共用的模型数据（比如菜单导航）在这里传到视图，也可以在这里统一制定视图

          */

         @Override

         public void postHandle(HttpServletRequest arg0, HttpServletResponse arg1, Object arg2, ModelAndView arg3)

                            throws Exception {

                   // TODO Auto-generated method stub

                   System.out.println("这是第1个拦截器，postHandle方法");

                  

         }

 

         /**

          *  执行handler完成执行此方法

          *  应用场景：统一异常处理，统一日志处理

          */

         @Override

         public void afterCompletion(HttpServletRequest arg0, HttpServletResponse arg1, Object arg2, Exception arg3)
                            throws Exception {
                   // TODO Auto-generated method stub
                   System.out.println("这是第1个拦截器， afterCompletion方法");
         }
}
~~~



### 2 过滤器

### 3 监听器

