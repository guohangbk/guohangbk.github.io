# 1.SpringMVC拦截器

## 1.1 SpringMVC拦截器简介

Spring MVC中的拦截器（Interceptor），它主要⽤于拦截⽤户请求并作相应的处理。例如通过拦截器可以进⾏权限验证、记录请求信息的⽇志、判断⽤户是否登录等。要使⽤Spring MVC中的拦截器，就需要对拦截器类进⾏定义和配置。通常拦截器类可以通过两种⽅式来定义。

**实现⽅式：**

SpringMVC 中的Interceptor 拦截请求是通过HandlerInterceptor 来实现的。

**两种⽅式：**

第⼀种⽅式：实现了Spring 的HandlerInterceptor 接⼝或者继承实现了HandlerInterceptor 接⼝的（⽐如抽象类HandlerInterceptorAdapter ），<font style="color:red">拦截之后拒绝请求</font>。

第⼆种⽅式：实现Spring的WebRequestInterceptor接⼝或者是继承实现了WebRequestInterceptor的类。<font style="color:red">只能拦截之后做⼀些操作，但是不能拒绝请求</font>。





## 1.2 拦截器配置

### 1.2.1 实现HandlerInterceptor接⼝

HandlerInterceptor 接⼝中定义了三个⽅法，我们就是通过这三个⽅法来对⽤户的请求进 ⾏拦截处理的。

1. preHandle (HttpServletRequest request, HttpServletResponse response, Object handle) ：该⽅法将在请求处理之前进⾏调⽤。 SpringMVC 中的Interceptor 是链式的调⽤的，每个Interceptor 的调⽤会依据它的声明 顺序依次执⾏，⽽且最先执⾏的都是Interceptor 中的preHandle ⽅法。 该⽅法的返回值是布尔值Boolean类型的，当它返回为false 时，表示请求结束，后续的 Interceptor 和Controller 都不会再执⾏；当返回值为true 时就会继续调⽤下⼀个。
2. postHandle (HttpServletRequest request, HttpServletResponse response, Object handle, ModelAndView modelAndView) ⽅法：是在当前请求进⾏处理之后，也就是 Controller ⽅法调⽤之后执⾏，但是它会在DispatcherServlet 进⾏视图返回渲染之前被调 ⽤，所以我们可以在这个⽅法中对Controller 处理之后的ModelAndView 对象进⾏操作。 postHandle ⽅法被调⽤的⽅向跟preHandle 是相反的，也就是说先声明的Interceptor 的 postHandle ⽅法反⽽会后执⾏。
3. afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handle, Exception ex) ⽅法：将在整个请求结束之后，也就是在DispatcherServlet 渲 染了对应的视图之后执⾏。这个⽅法的主要作⽤是⽤于进⾏资源清理⼯作的 。

**案例**

```java
//Handler.java实现接口
public class Handler implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("preHandle_HandlerInterceptor");
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("postHandle_HandlerInterceptor");
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("afterCompletion_HandlerInterceptor");
    }
}
```

配置拦截器

```xml
//springmvc-servlet.xml
<mvc:interceptors>
    <!--拦截所有-->
    <!--这个位置定义的顺序就是拦截器的执行连的执行顺序-->
    <bean class="com.interceptor.Handler"/>
    <bean class="com.interceptor.WebRequest"/>
    <!--拦截匹配路径-->
    <!--<mvc:interceptor>
        <mvc:mapping path="/dept/**"/>
        <bean class="com.interceptor.Handler"/>
    </mvc:interceptor>-->
</mvc:interceptors>
```

补充

```xml
//springmvc-servlet.xml
<!--SpringMVC过滤器-->
<mvc:interceptors>
    <mvc:interceptor>
        <!--拦截emp下的所有请求-->
        <mvc:mapping path="/emp/**"/>
        <!--拦截file下的所有请求-->
        <mvc:mapping path="/file/**"/>
        <!--放行login请求-->
        <mvc:exclude-mapping path="/emp/login/**"/>
        <!--使用LoginSession规则-->
        <bean class="com.interceptor.LoginSession"/>
    </mvc:interceptor>
</mvc:interceptors>
```





### 1.2.2 实现WebRequestInterceptor接⼝

WebRequestInterceptor 中也定义了三个⽅法，这三个⽅法都传递了同⼀个参WebRequest WebRequest 是Spring 定义的⼀个接⼝，它⾥⾯的⽅法定义跟HttpServletRequest基本 ⼀样，在WebRequestInterceptor 中对WebRequest 进⾏的所有操作都将同步到HttpServletRequest 中，然后在当前请求中⼀直传递。	

1. preHandle(WebRequest request) ⽅法。该⽅法将在请求处理之前进⾏调⽤，该⽅ 法的返回值是void ，也就是没有返回值，所以我们⼀般主要⽤它来进⾏资源的准备⼯作，通 过WebRequest 的setAttribute(name, value, scope)把它放到WebRequest 的属性中。 Scope，int类型，有三个值：   SCOPE_REQUEST ：它的值是0 ，代表只有在request 中可以访问。   SCOPE_SESSION ：它的值是1 ，如果环境允许的话它代表的是⼀个局部的隔离的 session，否则就代表普通的session，并且在该session范围内可以访问。   SCOPE_GLOBAL_SESSION ：它的值是2 ，如果环境允许的话，它代表的是⼀个全局共享 的session，否则就代表普通的session，并且在该session 范围内可以访问。
2. postHandle(WebRequest request, ModelMap model) ⽅法。在Controller ⽅法调 ⽤之后被调⽤视图返回被渲染之前被调⽤ 可以在这个⽅法⾥⾯通过改变数据模型ModelMap 来改变数据的展示。ModelMap 就是 Controller 处理之后返回的Model 对象，我们可以通过改变它的属性来改变返回的Model 模 型。
3. afterCompletion(WebRequest request, Exception ex) ⽅法。该⽅法会在整个请求 处理完成，也就是在视图返回并被渲染之后执⾏。 Exception 参数表示的是当前请求的异常对象，如果在Controller 中抛出的异常已经被 Spring 的异常处理器给处理了的话，那么这个异常对象就是是null 。

**案例**

```java
public class WebRequest implements WebRequestInterceptor {
    @Override
    public void preHandle(org.springframework.web.context.request.WebRequest request) throws Exception {
        System.out.println("preHandle_WebRequest");
    }

    @Override
    public void postHandle(org.springframework.web.context.request.WebRequest request, ModelMap model) throws Exception {
        System.out.println("postHandle_WebRequest");
    }

    @Override
    public void afterCompletion(org.springframework.web.context.request.WebRequest request, Exception ex) throws Exception {
        System.out.println("afterCompletion_WebRequest");
    }
}
```

配置拦截器

```xml
//springmvc-servlet.xml
<mvc:interceptors>
    <!--拦截所有-->
    <!--这个位置定义的顺序就是拦截器的执行连的执行顺序-->
    <bean class="com.interceptor.Handler"/>
    <bean class="com.interceptor.WebRequest"/>
    <!--拦截匹配路径-->
    <!--<mvc:interceptor>
        <mvc:mapping path="/dept/**"/>
        <bean class="com.interceptor.Handler"/>
    </mvc:interceptor>-->
</mvc:interceptors>
```



### 1.2.3 WebRequestInterceptor和HandlerInterceptor这两个拦截接⼝的区别： 

1. HandlerInterceptor接⼝的perHandle ⽅法有Boolean类型返回值，⽽ WebRequestInterceptor对应⽅法没有返回值。
2. HandlerInterceptor是针对请求的整个过程的，⽅法中都有response参数，⽽ WebRequestInterceptor是针对请求的，接⼝⽅法中没有response。





## 1.3 拦截器执行链

多个拦截器的执⾏是有⼀定的顺序，这个顺序和配置类的拦截器配置顺序有关，配置在前⾯的拦截⾸先进⾏拦截。 

```xml
<mvc:interceptors>
     <!--使用bean定义一个Interceptor 直接定义在mvc:interceptors根下面的Interceptor将拦截所有的请求 -->
    <mvc:interceptor>
        <mvc:mapping path="/emp/*"/>
        <!-- 定义在mvc:interceptor下面的表示是对特定的请求才进行拦截的 -->
        <bean class="com.MyInterceptor"/>
    </mvc:interceptor>
    <mvc:interceptor>
        <mvc:mapping path="/emp/*"/>
        <!-- 定义在mvc:interceptor下面的表示是对特定的请求才进行拦截的 -->
        <bean class="com.MyIn2"/>
    </mvc:interceptor>
</mvc:interceptors>
```





## 1.4 拦截器实现session权限控制

```java
public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
    System.out.println("preHandle_HandlerInterceptor");
    response.setContentType("application/json;charsetEncoding=utf-8");
    HttpSession session = request.getSession();
    Object user = session.getAttribute("user");
    if(user==null){
        response.reset();
        PrintWriter writer = response.getWriter();
        writer.print("{\"state\":false,\"message\":\"没有登录\"}");
        writer.flush();
        writer.close();
        return false;
    }
    return true;
}
```

```xml
<mvc:interceptors>
    <mvc:interceptor>
        <mvc:mapping path="/dept/**"/>
        <bean class="com.interceptor.Handler"/>
    </mvc:interceptor>
</mvc:interceptors>
```







#	2.SpringMVC异常处理

SpringMVC中提供了⼀个全局异常处理器对系统中出现的异常进⾏统⼀处理。⼀般系统中，DAO，Service，controller层出现异常都以 throw Exception形式向上抛出。最后由SpringMVC的前端控制器DispatcherServlet统⼀交由全局异常处理器进⾏异常处理。 



## 2.1 声明一个自定义异常

⾃定义⼀个异常类 继承Exception，getMessage()是⽤来获取异常信息，⼀般可以定义⼀个或者多个来说明类别信息。

```java
public class BussinessException extends Exception {
    public BussinessException(String message){
        super(message);
    }
}
```





## 2.2 三种实现方式

Spring MVC处理异常有3种⽅式：

1. 使⽤Spring MVC提供的简单异常处理器SimpleMappingExceptionResolver；
2. 实现Spring的异常处理接⼝HandlerExceptionResolver ⾃定义⾃⼰的异常处理器；
3. 使⽤@ExceptionHandler注解实现异常处理； 



### 2.2.1 SimpleMappingExceptionResolver

系统写好的统⼀异常类，⾃⼰负责配置即可，现在基本没有用

```xml
<bean class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
 <!-- 定义默认的异常处理页面，当该异常类型的注册时使用 -->
    <property name="defaultErrorView" value="error"></property>
    <!-- 定义异常处理页面用来获取异常信息的变量名，默认名为exception -->
    <property name="exceptionAttribute" value="exception"></property>
    <!-- 定义需要特殊处理的异常，用类名或完全路径名作为key，异常也页名作为值 -->
    <property name="exceptionMappings">
       <props>
           <prop key="java.lang.NullPointerException">error-null</prop>
           <prop key="com.exception.BusinessException">errorbusiss</prop> 
           <!-- 这里还可以继续扩展对不同异常类型的处理 -->
      </props>
   </property>
</bean>
```

使⽤SimpleMappingExceptionResolver进⾏异常处理，具有集成简单、有良好的扩展性、对已有代码没有⼊侵性等优点，但该⽅法仅能获取到异常信息，若在出现异常时，对需要获取除异常以外的数据的情况不适⽤。



### 2.2.2 HandlerExceptionResolver 

```java
public class ExceptionReslover implements HandlerExceptionResolver {
    @Override
    public ModelAndView resolveException(HttpServletRequest httpServletRequest,
                                         HttpServletResponse httpServletResponse, Object o, Exception e) {
        System.out.println("处理异常");
        ModelAndView modelAndView=new ModelAndView();
        modelAndView.addObject("errorInfo",e.getMessage());
        if(e instanceof BussinessException){
            modelAndView.setViewName("500");
        }else if(e instanceof NullPointerException){
            modelAndView.setViewName("404");
        }else{
            modelAndView.setViewName("error");
        }
        return modelAndView;
    }
}
```

配置

```
<bean id="exceptionReslover" class="com.exception.ExceptionReslover"> </bean>
```

使⽤实现HandlerExceptionResolver接⼝的异常处理器进⾏异常处理，具有集成简单、有良好的扩展性、对已有代码没有⼊侵性等优点，同时，在异常处理时能获取导致出现异常的对象，有利于提供更详细的异常处理信息。 



### 2.2.3 @ExceptionHandler

```java
@ControllerAdvice//如果不加只能对一个控制层类生效
public class CommonExceptionReslover {


    @ExceptionHandler(Exception.class)
    @ResponseBody//异步异常 返回一个json
    public ResultData bussess(Exception e){
        return new ResultData(false,"发生异常"+e.getMessage(),null);
    }


    @ExceptionHandler(BussinessException.class)
    @ResponseBody
    public ResultData bussess2(Exception e){
        return new ResultData(false,"发生异常"+e.getMessage(),null);
    }

    @ExceptionHandler(ArithmeticException.class)
    @ResponseBody
    public ResultData bussess3(Exception e){
        return new ResultData(false,"发生异常"+e.getMessage(),null);
    }

    //同步异常 界面跳转
    @ExceptionHandler(BussinessException.class)
    public String to500(){
        return "500";
    }

}
```

如果单使用@ExceptionHandler，只能在当前Controller中处理异常。但当配合 @ControllerAdvice一起使用的时候，就可以摆脱那个限制了。



<font style="color:red">tips：</font>

web.xml中可以加入error-page，可以处理404异常

![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580612684.png)







# 3.定时任务

在企业级应用中，经常会制定一些“计划任务”，即在某个时间点做某件事情，核心是以时间为关注点，即在一个特定的时间点，系统执行指定的一个操作，常见的任务调度框架有Quartz和SpringTask等。

定时任务有单线程的也有多线程的，如果启动的是单线程的定时任务耗时比较久，那么其他任务需要等待此任务执行完毕以后才会执行。而多线程就不会受此影响，各自执行各自的，互不相干。所以一般情况下我们启动的是多线程的定时任务。



## 3.1 案例演示

定时任务是属于Spring的，所以我们在applicationContext.xml中加入配置。注意加入加入task的命名空间

```xml
<!-- 定时器开关-->
//多线程任务，pool-size：线程数
<task:scheduler id="myScheduler" pool-size="5"/>
<!--开启SpringTask注解驱动-->
<task:annotation-driven scheduler="myScheduler"/>


//单线程 不指定线程池
<!--<task:annotation-driven/>-->
```

```java
//java测试代码
@Service
public class ScheduleService {
    //private EmployeeMapper employeeMapper;

    @Scheduled(cron = "0/10 * * * * ?")
    public void testTask(){
        DateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        System.out.println(sdf.format(DateTime.now().toDate())+"*********A任务每10秒执行一次进入测试");
    }
    //spring的定时任务默认是单线程，多个任务执行起来时间会有问题。
    // 会等待。当前任务如果不结束，下一个任务进不来，哪怕我时间到了
    @Scheduled(cron = "0/5 * * * * ?")
    //rate :延迟  cron 是规则
    public void testTask02(){
        try {
            TimeUnit.SECONDS.sleep(20);//20SECONDS_20S
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        DateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        System.out.println(sdf.format(DateTime.now().toDate())+"*********B任务每5秒执行一次进入测试");
    }
}
```

多线程测试结果

![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580612822.png)





## 3.2 CRON表达式规则说明

### 3.2.1 Cron的两种语法格式：【如果不指定年，推荐使用第二个表达式】

1. 表达式格式1

   ```java
   Seconds Minutes Hours DayofMonth Month DayofWeek Year
   ```

   

2. 表达式格式2

   ```java
   Seconds Minutes Hours DayofMonth Month DayofWeek
   ```

   

3. 使用方式

   ```java
   @Scheduled(cron = "表达式")
   ```



### 3.2.2 每个域可出现的字符：

| 域         | 可出现的字符            | 范围                                                         |
| ---------- | ----------------------- | ------------------------------------------------------------ |
| Seconds    | , - * /四个字符         | 有效范围为0-59的整数                                         |
| Minutes    | , - * /四个字符         | 有效范围为0-59的整数                                         |
| Hours      | , - * /四个字符         | 有效范围为0-23的整数                                         |
| DayofMonth | , - * / ? L W C八个字符 | 有效范围为1-31的整数                                         |
| Month      | , - * /四个字符         | 有效范围为1-12的整数或JAN-DEc                                |
| DayofWeek  | , - * / ? L C #八个字符 | 有效范围为1-7的整数或SUN-SAT两个范围。1表示星期天，2表示星期一， 依次类推 |
| Year       | , - * /四个字符         | 有效范围为1970-2099年                                        |



### 3.2.3 每一个域都使用数字，但还可以出现如下特殊字符，它们的含义：

1. **\***：表示匹配该域的任意值，假如在Minutes域使用*, 即表示每分钟都会触发事件。
2. **?**：只能用在DayofMonth和DayofWeek两个域【互斥，表示放弃当前位置的匹配】。比如DayofMonth和 DayofWeek会相互影响。例如想在每月的20日触发调度，不管20日到底是星期几，则只能使用如下写法： 13 13 15 20 * ?, 其中最后一位只能用？，而不能使用，如果使用表示不管星期几都会触发，实际上并不是这样。
3. **-**：表示范围，例如在Minutes域使用5-20，表示从5分到20分钟每分钟触发一次
4. **/**：表示起始时间开始触发，然后每隔固定时间触发一次，例如在Minutes域使用5/20,则意味着5分钟触发一次，而25，45等分别触发一次.
5. **,**：表示列出枚举值值。例如：在Minutes域使用5,20，则意味着在5和20分每分钟触发一次。
6. **L**：表示最后，只能出现在DayofWeek和DayofMonth域，如果在DayofWeek域使用5L,意味着在最后的一个星期四触发。
7. **W**：表示有效工作日(周一到周五),只能出现在DayofMonth域，系统将在离指定日期的最近的有效工作日触发事件。例如：在 DayofMonth使用5W，如果5日是星期六，则将在最近的工作日：星期五，即4日触发。如果5日是星期天，则在6日(周一)触发；如果5日在星期一 到星期五中的一天，则就在5日触发。另外一点，W的最近寻找不会跨过月份
8. **LW**：这两个字符可以连用，表示在某个月最后一个工作日，即最后一个星期五。
9. **#**：用于确定每个月第几个星期几，只能出现在DayofMonth域。例如在4#2，表示某月的第二个星期三。



### 3.2.4 Cron表达式例子：

```java
0 0 10,14,16 * * ? 每天上午10点，下午2点，4点
0 0/30 9-17 * * ? 朝九晚五工作时间内每半小时
0 0 12 ? * WED 表示每个星期三中午12点
"0 0 12 * * ?" 每天中午12点触发
"0 15 10 ? * *" 每天上午10:15触发
"0 15 10 * * ?" 每天上午10:15触发
"0 15 10 * * ? *" 每天上午10:15触发
"0 15 10 * * ? 2005" 2005年的每天上午10:15触发
"0 * 14 * * ?" 在每天下午2点到下午2:59期间的每1分钟触发
"0 0/5 14 * * ?" 在每天下午2点到下午2:55期间的每5分钟触发
"0 0/5 14,18 * * ?" 在每天下午2点到2:55期间和下午6点到6:55期间的每5分钟触发
"0 0-5 14 * * ?" 在每天下午2点到下午2:05期间的每1分钟触发
"0 10,44 14 ? 3 WED" 每年三月的星期三的下午2:10和2:44触发
"0 15 10 ? * MON-FRI" 周一至周五的上午10:15触发
"0 15 10 15 * ?" 每月15日上午10:15触发
"0 15 10 L * ?" 每月最后一日的上午10:15触发
"0 15 10 ? * 6L" 每月的最后一个星期五上午10:15触发
"0 15 10 ? * 6L 2002-2005" 2002年至2005年的每月的最后一个星期五上午10:15触发
"0 15 10 ? * 6#3" 每月的第三个星期五上午10:15触发
```



### 3.2.5 使用案例

```java
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;
import java.util.Date;

@Component
public class SeckillTask {
    /**
     * 刷新秒杀商品
     *  @Scheduled 注解为注册任务调度
     *  cron 参数 ： cron 表达式: 执行的时间规则！
     */
    @Scheduled(cron = "0/10 * * * * ?")
    public void refreshSeckillGoods() {
        System.out.println("execute task: " + new Date());
    }
}
```





参考链接：https://www.jianshu.com/p/31c6b5d7b154