#	1.SpringMVC简介

## 1.1 SpringMVC的介绍

SpringMVC 框架提供了模型-视图-控制的体系结构和可以用来开发灵活、松散耦合的 web 应用程序的组件。MVC 模式导致了应用程序的不同方面(输入逻辑、业务逻辑和 UI 逻辑)的分离，同时提供了在这些元素之间的松散耦合。

- - **模型**封装了应用程序数据，并且通常它们由 POJO 组成。
  - **视图**主要用于呈现模型数据，并且通常它生成客户端的浏览器可以解释的 HTML 输出。
  - **控制器**主要用于处理用户请求，并且构建合适的模型并将其传递到视图呈现。

## 1.2 SpringMVC的优点

- - 是⼀种基于Java的实现了Web MVC设计模式的请求驱动类型的轻量级Web框架 
  - 让我们能⾮常简单的设计出⼲净的Controller层和薄薄的Controller层
  - 进⾏更简洁的Controller层的开发
  - 天⽣与Spring框架集成（如IoC容器、AOP等）
  - 提供强⼤的约定⼤于配置的契约式编程⽀持
  - ⾮常灵活的数据验证、格式化——（⽇期格式化）和数据绑定机制
  - ⽀持Restful⻛格





#	2.SpringMVC核心类和接口

**DispatcherServlet**：

Spring Web 模型-视图-控制（MVC）框架是围绕 *DispatcherServlet* 设计的，*DispatcherServlet* 用来处理所有的 HTTP 请求和响应。Spring Web MVC *DispatcherServlet* 的请求处理的工作流程如下图所示：

![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1579962196.png)



+ DispatherServlet：核⼼控制器，接收请求 
+ HanderMapping：映射处理器，路径配置到⼀个配置⽂件⾥或者使⽤注解，请求映射到 类上。 
+ HandlerAdapter：处理适配器，将映射到的类和⽅法处理成⾃⼰需要的格式，然后执⾏，返回⼀个对象--ModelAndView(封装了两类东⻄：回参   路径跳转) 
+ Handler：处理器，处理请求的
+ ViewResolver:解析ModelAndView，返回⼀个对象View，View进⾏处理渲染界⾯—— JSTLView





#	3.SpringMVC请求流程

下面是对应于 *DispatcherServlet* 传入 HTTP 请求的事件序列：

- 收到一个 HTTP 请求后，*DispatcherServlet* 根据 *HandlerMapping* 来选择并且调用适当的*控制器*。
- *控制器*接受请求，并基于使用的 GET 或 POST 方法来调用适当的 service 方法。Service 方法将设置基于定义的业务逻辑的模型数据，并返回视图名称到 *DispatcherServlet* 中。
- *DispatcherServlet* 会从 *ViewResolver* 获取帮助，为请求检取定义视图。
- 一旦确定视图，*DispatcherServlet* 将把模型数据传递给视图，最后呈现在浏览器中。

简单通俗的说就是：

DispatherServlet接收到请求以后，调⽤HanderMapping根据请求找配置或者注解映射出来需要调⽤的类，HanderAdapter处理适配器将映射类（Hander）处理成⾃⼰想要的格式然后执⾏返回ModelAndView。DispatherServlet将modelAndView交给ViewResolver进⾏处理，处理完后交给view进⾏界⾯渲染。

***看图理解请求流程***

![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1579962342.png)

![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1579962354.png)





# 4.SpringMVC使用方法

##	4.1 SpringMVC的XML解析使用

1. 配置前置控制器，拦截所有路径——核心控制器

   ```java
   //web.xml
   <!DOCTYPE web-app PUBLIC
    "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
    "http://java.sun.com/dtd/web-app_2_3.dtd" >
   <web-app>
     <display-name>Archetype Created Web Application</display-name>
     <servlet>
       <servlet-name>dispatcherServlet</servlet-name>
       <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
       <init-param>
         <param-name>contextConfigLocation</param-name>
         <param-value>classpath:dispatcherServlet-servlet.xml</param-value>
       </init-param>
     </servlet>
     <servlet-mapping>
       <servlet-name>dispatcherServlet</servlet-name>
       <url-pattern>/</url-pattern>
     </servlet-mapping>
   </web-app>
   ```

   

2. 创建配置文件，注意默认是WEB-INF下的名称为web.xml中servlet名称-servlet.xml

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1579962608.png)

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1579962623.png)

   

3. 配置⽂件中配置：将HandlerMapping、HandlerAdapter、viewResolver等接⼝的实现类配置成bean，其中DispatcherServlet.propertires 资源配置⽂件中信息

   ```java
   //dispatcherServlet-servlet.xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans
           xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xmlns:content="http://www.springframework.org/schema/context"
           xmlns:mvc="http://www.springframework.org/schema/mvc"
           xsi:schemaLocation="http://www.springframework.org/schema/beans
          http://www.springframework.org/schema/beans/spring-beans-3.1.xsd
          http://www.springframework.org/schema/mvc
          http://www.springframework.org/schema/mvc/spring-mvc.xsd">
       <!--映射处理器-->
       <bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>
       <!--处理适配器：处理EmpController类，执行以及结果封装处理适配器接管-->
       <bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>
       <!--ViewResource:视图处理器-->
       <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
           <property name="viewClass" value="org.springframework.web.servlet.view.JstlView"/>
           <property name="suffix" value=".jsp"/>
           <property name="prefix" value="/module/"/>
       </bean>
       <bean id="/selectEmp" class="com.controller.EmpController"/>
   </beans>
   ```

   

4. 书写Controller即Adapter处理器，实现Controller接⼝

   ```java
   //Controller.java
   package com.controller;
   
   import org.springframework.web.servlet.ModelAndView;
   import org.springframework.web.servlet.mvc.Controller;
   import javax.servlet.http.HttpServletRequest;
   import javax.servlet.http.HttpServletResponse;
   
   /**
    * @author gh
    * @date 2019/12/4 9:40
    */
   public class EmpController implements Controller {
       @Override
       public ModelAndView handleRequest(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception {
           ModelAndView modelAndView = new ModelAndView();
           modelAndView.addObject("returninfo","这里进行一个测试，看看结果吧！");
           modelAndView.setViewName("index");
           return modelAndView;
       }
   }
   ```

   

5. 配置访问路径

   ```java
   <bean id="/selectEmp" class="com.controller.EmpController"/>
   ```

   

6. Spring视图文件index.jsp的内容

   ```java
   <%@ page contentType="text/html;charset=UTF-8" language="java" %>
   <%@ page isELIgnored="false" %>
   <html>
   <head>
       <title>Title</title>
   </head>
   <body>
       ${returninfo}
   </body>
   </html>
   ```

   

7. 运行结果

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1579962733.png)



##	4.2 SpringMVC的注解使用

1. 配置web.xml核心控制器

   ```java
   //web.xml
   <!DOCTYPE web-app PUBLIC
    "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
    "http://java.sun.com/dtd/web-app_2_3.dtd" >
   <web-app>
     <servlet>
       <servlet-name>dispatcherServlet</servlet-name>
       <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
       <init-param>
         <param-name>contextConfigLocation</param-name>
         <param-value>classpath:dispatcherServlet-servlet.xml</param-value>
       </init-param>
     </servlet>
     <servlet-mapping>
     <servlet-name>dispatcherServlet</servlet-name>
     <url-pattern>/</url-pattern>
     </servlet-mapping>
   </web-app>
   ```

   

2. 配置资源配置信息，此处需要注意文件名是servlet名称-servlet.xml

   ```java
   //dispatcherServlet-servlet.xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans
           xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xmlns:content="http://www.springframework.org/schema/context"
           xmlns:mvc="http://www.springframework.org/schema/mvc"
           xmlns:context="http://www.springframework.org/schema/context"
           xsi:schemaLocation="http://www.springframework.org/schema/beans
          http://www.springframework.org/schema/beans/spring-beans-3.1.xsd
          http://www.springframework.org/schema/mvc
          http://www.springframework.org/schema/mvc/spring-mvc.xsd http://www.springframework.org/schema/context
          http://www.springframework.org/schema/context/spring-context.xsd">
       <!--在配置文件中开启注解扫描，注意此处只扫描controller包下的注解-->
       <context:component-scan base-package="com.controller"/>
       <!--在配置文件中加载MVC驱动，注意此处选择加入依赖的时候，选择后缀是mvc的-->
       <mvc:annotation-driven/>
       <!--注解形式，此处不需要映射处理器和处理适配器，只需要视图处理器即可-->
       <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
           <property name="viewClass" value="org.springframework.web.servlet.view.JstlView"/>
           <!--后缀加上.jsp-->
           <property name="suffix" value=".jsp"/>
           <!--前边加上路径名-->
           <property name="prefix" value="/module/"/>
       </bean>
   </beans>
   ```

   

3. module/index.jsp

   ```java
   <%@ page contentType="text/html;charset=UTF-8" language="java" %>
   <%@ page isELIgnored="false" %>
   <html>
   <head>
       <title>Title</title>
   </head>
   <body>
       ${returninfo}
   </body>
   </html>
   ```

   

4. 书写Controller类

   @Controller声明控制层类，不⽤再实现接⼝

   @RequestMapping路径映射加到⽅法上即可，如果加在了类上，是窄化路径

   ```java
   package com.controller;
   
   import org.springframework.stereotype.Controller;
   import org.springframework.ui.Model;
   import org.springframework.web.bind.annotation.RequestMapping;
   
   /**
    * @author gh
    * @date 2019/12/4 10:48
    */
   //Component:声明通用组件；Controller声明一个控制层组件
   @Controller
   public class EmpController {
       @RequestMapping("/selectEmp")
       //返回的String就是要转发的路径
       public String selectEmp(Model model){
           model.addAttribute("returninfo","破哦哦哦哦哦哦哦哦哦");
           return "index";
       }
   }
   ```

   

5. 运行截图

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1579962963.png)





# 5.SpringMVC的注解详解

##	5.1 请求路径

⽅法名上可以直接使⽤@RequestMapping("/empList")代表请求URL到处理器功能处理⽅法的映射。

类名上同样可以使⽤@RequestMapping(“/emp”)来为所有本类中所有⽅法添加统⼀路径前缀，也叫窄化路径。



##	5.2 回参数和跳转路径

1. return ModelAndView

2. ⽅法参数中可与直接书写Model，然后设置回参数，返回视图名称，即jsp的名字

   ```java
   @RequestMapping("/selectEmp")
   //返回的String就是要转发的路径
   public String selectEmp(Model model){
       model.addAttribute("returninfo","破哦哦哦哦哦哦哦哦哦");
       return "index";
   }
   ```

   

3. ⽅法参数中可以直接书写：HttpServletRequest、HttpSession等原始对象

   ```java
   @RequestMapping("/insertDept")
   public String insertDept(HttpServletRequest httpServletRequest, HttpSession httpSession){
       request和session就可以回参
       return "index";
   }
   ```

   

4. ajax返回json数据  直接⽅法返回类型为返回的数据值，⽅法前添加：@responseBody处理器功能处理⽅法的返回值作为响应体（通过HttpMessageConverter进⾏类型转换），需要依赖：jackson包

   ```java
   @RequestMapping("updateEmp")
   @ResponseBody
   public ResultData updateEmp(@RequestParam Map map){
       int i = empService.updateEmp(map);
       ResultData resultData = null;
       if(i > 0){
           resultData = new ResultData(true, "修改成功", null);
       }else{
           resultData = new ResultData(false, "修改失败", null);
       }
       return resultData;
   }
   ```

   

5. redirect：重定向："redirect:/index.jsp" 或者return "redirect:list" 这是到list指向的⽅法。可以重定向到jsp；也可以到action⽅法

   ```java
   @RequestMapping("/deleteEmp")
   public String deleteEmp(Integer id){
       int i = empService.deleteEmp(id);
       return "redirect:selectEmp";
   }
   ```

   

6. forward：转发。return "forward:list" 可以实现转发到jsp也可以到action的⽅法

   



## 5.3 接参数

1. ⽅法名(中间参数可以直接接收前台参数)，不要⽤ int ﬂoat double 如果前台没有传递int的值，会直接报错，尽量⽤：Integer

   ```java
   @RequestMapping("/insertDept2")
   public String insertDept2(Integer deptno,String dname,String loc){
       System.out.println(deptno+"----"+dname+"----"+loc);
       return "index";
   }
   ```

   

2. ⽅法名（参数可以直接是实体，⾃动进⾏匹配，类似模型驱动，但是既不需要ModelDriven也不需要前台进⾏对象.属性名，回⾃动匹配）

   ```java
   @RequestMapping("/insertDept3")
   public String insertDept3(DeptEntity dept){
       System.out.println(dept);
       return "index";
   }
   ```

   

3. 特殊情况下需要@RequestParam；⽐如说后台想将前台所有内容直接封装成map进⾏接收，可以直接在前边添加@RequestParam；某⼀种参数必须进⾏传递可以⽤@RequestParam(value=”username”,required=true)。注解@RequestParam接收的参数是来⾃requestHeader中，即请求头，通常⽤于GET请求。@RequestParam⽤来处理 Content-Type 为 application/x-www-form-urlencoded 编码的内容，Content-Type默认为该属性。不⽀持批量数据，即json的复杂数据，可以⽤application/json，使⽤RequestBody

   ```java
   @RequestMapping("/insertEmp")
   @ResponseBody   //异步方法需要加上
   public ResultData insertEmp(@RequestParam Map map){
       int i = empService.insertEmp(map);
       ResultData resultData = null;
       if(i > 0){
           resultData = new ResultData(true, "新增成功", null);
       }else{
           resultData = new ResultData(false, "新增失败", null);
       }
       return resultData;
   }
   ```

   

4. 原始对象

   ```java
   //使用原始对象接收参数
   @RequestMapping("/insertDept")
   public String insertDept(HttpServletRequest httpServletRequest, HttpSession httpSession){
       String deptno = httpServletRequest.getParameter("deptno");
       String dname = httpServletRequest.getParameter("dname");
       String loc = httpServletRequest.getParameter("loc");
       System.out.println(deptno+"----"+dname+"----"+loc);
       return "index";
   }
   ```

   

5. 方法名。⽅法名（接收数组或者List）：使⽤@RequestBody：注解@RequestBody接收的参数是来⾃requestBody中，即请求体。⼀般⽤于处理⾮ Content-Type: application/x-www-form-urlencoded编码格式的数据，⽐如：application/json、application/xml等类型的数据。就application/json类型的数据⽽⾔，使⽤注解@RequestBody可以将body⾥⾯所有的json数据传到后端，后端再进⾏解析。可以接收json封装实体、封装Map、封装List<Map> 、封装List<实体>、封装List<String、Integer>、Stirng[]、Integer[]数组等。

   ```java
   @RequestMapping("/insertEmp")
   @ResponseBody   //异步方法需要加上
   public ResultData insertEmp(@RequestParam Map map){
       int i = empService.insertEmp(map);
       ResultData resultData = null;
       if(i > 0){
           resultData = new ResultData(true, "新增成功", null);
       }else{
           resultData = new ResultData(false, "新增失败", null);
       }
       return resultData;
   }
   ```

   

6. @PathVariable一般是配合restful风格进行处理的，是spring3.0的一个新功能：接收请求路径中占位符的值

   ```java
   @PathVariable("xxx")
   通过 @PathVariable 可以将URL中占位符参数{xxx}绑定到处理器类的方法形参中@PathVariable(“xxx“) 
    
   @RequestMapping(value=”user/{id}/{name}”)
   请求路径：http://localhost:8080/hello/show5/1/james
   ```

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/20200125230001.png)



<font style="color:red"> 注意：@RequestParam接收参数，可以省略</font>

@RequestParam写与不写区别在哪里？

三种写法：

1. `test(String name)`
2. `test(@RequestParam String name)`
3. `test(@RequestParam("userName") String name)`

第一种如果没有name 参数不会报错

第二种没有name 参数会报错，（也可以设置required = false)

第三种跟第二种的区别是name 参数换为userName。