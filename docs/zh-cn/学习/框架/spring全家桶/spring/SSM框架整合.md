#	1.SSM框架介绍

SSM，即 SpringMVC、Spring 与 MyBatis 三个框架，它们在三层架构中所处的位置是不同的，即它们在三层架构中的功能各不相同，各司其职。

**SpringMVC**：作为 View 层的实现者，完成用户的请求接收功能。SpringMVC 的 Controller作为整个应用的控制器，完成用户请求的转发及对用户的响应；

**MyBatis**：作为 Dao 层的实现者，完成对数据库的增、删、改、查功能；

**Spring**：以整个应用大管家的身份出现。整个应用中所有 Bean 的生命周期行为，均由Spring 来管理。即整个应用中所有对象的创建、初始化、销毁，及对象间关联关系的维护，均由 Spring 进行管理。

![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/20200130202926.png)





#	2.SSM架构层次

##	2.1 持久层：DAO层（mapper）

**DAO层：DAO层主要是做数据持久层的工作，负责与数据库进行联络的一些任务都封装在此。**

- 1. DAO层的设计首先是设计DAO的接口；
  2. 然后在Spring的配置文件中定义此接口的实现类；
  3. 然后就可在模块中调用此接口来进行数据业务的处理，而不用关心此接口的具体实现类是哪个类，显得结构非常清晰；
  4. DAO层的数据源配置，以及有关数据库连接的参数都在Spring的配置文件中进行配置。



##	2.2 业务层：Service层

**Service层：Service层主要负责业务模块的逻辑应用设计。** 

- 1. 首先设计接口，再设计其实现的类；
  2. 接着再在Spring的配置文件中配置其实现的关联。这样我们就可以在应用中调用Service接口来进行业务处理；
  3. Service层的业务实现，具体要调用到已定义的DAO层的接口；
  4. 封装Service层的业务逻辑有利于通用的业务逻辑的独立性和重复利用性，程序显得非常简洁。



##	2.3 表现层：Controller层

**Controller层：Controller层负责具体的业务模块流程的控制。**

- 1. 在此层里面要调用Service层的接口来控制业务流程；
  2. 控制的配置也同样是在Spring的配置文件里面进行，针对具体的业务流程，会有不同的控制器，我们具体的设计过程中可以将流程进行抽象归纳，设计出可以重复利用的子单元流程模块，这样不仅使程序结构变得清晰，也大大减少了代码量。



##	2.4 视图层：View层

**View层：此层与控制层结合比较紧密，需要二者结合起来协同工发。View层主要负责前台jsp页面的表示。**



## 2.5 各层联系

- 1. DAO层、Service层这两个层次都可以单独开发，互相的耦合度很低，完全可以独立进行，这样的一种模式在开发大项目的过程中尤其有优势
  2. Controller、View层因为耦合度比较高，因而要结合在一起开发，但是也可以看作一个整体独立于前两个层进行开发。这样，在层与层之前我们只需要知道接口的定义，调用接口即可完成所需要的逻辑单元应用，一切显得非常清晰简单。
  3. Service逻辑层设计
  4. Service层是建立在DAO层之上的，建立了DAO层后才可以建立Service层，而Service层又是在Controller层之下的，因而Service层应该既调用DAO层的接口，又要提供接口给Controller层的类来进行调用，它刚好处于一个中间层的位置。每个模型都有一个Service接口，每个接口分别封装各自的业务处理方法。





#	3.SSM整合

因为Spring MVC 是Spring框架中的一个子模块，所以 Spring 与 SpringMVC 之间不存在整合的问题。实际上，SSM 框架的整合只涉及 Spring 与 MyBatis 的整合以及 Spring MVC 与 MyBatis 的整合。

大致梳理：

tomcat启动⼯程的时候要读 web.xml⽂件，web.xml中则需要配置Spring(applicationContext.xml)和SpringMVC(mvc-servlet.xml)，而MyBatis则需要托管在Spring容器中。



##	3.1 使用Maven创建项目并导入Pom依赖



##	3.2 创建信息输入页面

在 Webapp目录下的jsp文件夹下创建insertEmp.jsp页面，具体代码如下：

```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
    <script src="../resources/jquery-3.4.1.min.js"></script>
    <script>
        $().ready(function () {
            $("#btn").click(function () {
                $.ajax({
                    url:"/Spring03_SSM1_war_exploded/Emp/insertEmp",
                    data:{
                        id:$("#id").val(),
                        name:$("#name").val(),
                        telephone:$("#telephone").val()
                    },
                    dataType:"json",
                    type:"post",
                    success:function (data) {
                        alert(data.message)
                        window.location.href="/Spring03_SSM1_war_exploded/Emp/selectEmp"
                    }
                })
            })
        })
    </script>
</head>
<body>
    <input type="text" id="id"/>
    <input type="text" id="name"/>
    <input type="text" id="telephone"/>
    <input type="button" id="btn" value="新增">
</body>
</html>
```



##	3.3 创建持久化类

在 src 目录下创建一个名为 com.entity的包，并在该包中创建一个 PO 类 Emp。具体代码如下：

```java
public class Emp {
    private Integer id;
    private String name;
    private String telephone;

    public Emp(Integer id, String name, String telephone) {
        this.id = id;
        this.name = name;
        this.telephone = telephone;
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getTelephone() {
        return telephone;
    }

    public void setTelephone(String telephone) {
        this.telephone = telephone;
    }

    @Override
    public String toString() {
        return "Emp{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", telephone='" + telephone + '\'' +
                '}';
    }
}
```



##	3.4 创建Dao层

在 src 目录下创建一个名为 com.mapper的包，并在该包中创建一个名为 EmpMapper的接口。具体代码如下：

```java
public interface EmpMapper {
    List<HashMap> selectEmp();
    //接口方法对应SQL映射文件EmpMapper.xml中的id
    int insertEmp(Map map);
    int updateEmp(Map map);
    int deleteEmp(Integer id);
}
```



## 3.5 创建Service层

在 src 目录下创建一个名为 com.service 的包，并在该包中创建一个名为 EmpService的接口和该接口的实现类 EmpServiceImpl。

EmpService 接口的代码如下：

```java
public interface EmpService {
    List<HashMap> selectEmp();
    int insertEmp(Map map);
    int updateEmp(Map map);
    int deleteEmp(Integer id);
}
```

EmpServiceImpl 实现类的代码如下：

```java
@Service
public class EmpServiceImpl implements EmpService {
    @Autowired
    private EmpMapper empMapper;
    @Override
    public List<HashMap> selectEmp() {
        return empMapper.selectEmp();
    }
    @Override
    public int insertEmp(Map map) {
        return empMapper.insertEmp(map);
    }
    @Override
    public int updateEmp(Map map) {
        return empMapper.updateEmp(map);
    }
    @Override
    public int deleteEmp(Integer id) {
        return empMapper.deleteEmp(id);
    }
}
```



##	3.6 创建Controller层

在 src 目录下创建一个名为 com.controller 的包，并在该包中创建一个名为 EmpController的控制器类。具体代码如下：

```java
package com.controller;

import com.entity.Emp;
import com.entity.ResultData;
import com.service.EmpService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;

import java.util.Map;

/**
 * @author gh
 * @date 2019/12/5 11:20
 */
@Controller
@RequestMapping("/Emp")
public class EmpController {

    @Autowired
    private EmpService empService;

    @RequestMapping("/selectEmp")
    public String selectEmp(Model model){
        model.addAttribute("emps",empService.selectEmp());
        return "selectEmp";
    }

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

    @RequestMapping("/toupdateEmp")
    public String toupdateEmp(Integer id,String name,String telephone,Model model){
        Emp emp = new Emp(id,name,telephone);
        System.out.println(name);
        model.addAttribute(emp);
        return "updateEmp";
    }

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

    @RequestMapping("/deleteEmp")
    public String deleteEmp(Integer id){
        int i = empService.deleteEmp(id);
        return "redirect:selectEmp";
    }
}
```



##	3.7 创建用户信息显示页面

在 WEB-INF 目录下创建文件夹 JSP，并在该文件夹下创建用户信息显示页面 selectEmp.jsp。具体代码如下：

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%@ page isELIgnored="false" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<a href="/Spring03_SSM1_war_exploded/jsp/insertEmp.jsp">新增员工</a>
<table border="1" cellspacing="0" width="500px">
    <tr>
        <td>工号</td>
        <td>姓名</td>
        <td>电话</td>
        <td>操作</td>
    </tr>
    <c:forEach items="${emps}" var="emp">
        <tr>
            <td>${emp.id}</td>
            <td>${emp.name}</td>
            <td>${emp.telephone}</td>
            <td>
                <a href="toupdateEmp?id=${emp.id}&name=${emp.name}&telephone=${emp.telephone}">编辑</a>
                <a href="deleteEmp?id=${emp.id}">删除</a>
            </td>
        </tr>
    </c:forEach>
</table>
</body>
</html>
```



##	3.8 配置相关文件，整合SSM

###	3.8.1 web.xml

当服务器启动的时候会先去找到web.xml文件进行解读，在 WEB-INF 目录下创建 web.xml 文件，并在该文件中实例化 ApplicationContext 容器、启动 Spring 容器、配置 DispatcherServlet 以及配置字符编码过滤器。具体代码如下：

```xml
//web.xml
<!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd" >
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns="http://java.sun.com/xml/ns/javaee"
         xmlns:web="http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
         version="3.0">
  <display-name>Archetype Created Web Application</display-name>
  <!--配置Spring MVC的配置文件路径，配置 DispatcherServlet-->
  <servlet>
    <servlet-name>springmvc</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:mvc-servlet.xml</param-value>
    </init-param>
  </servlet>
  <servlet-mapping>
    <servlet-name>springmvc</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>
  <!-- 实例化ApplicationContext容器 -->
  <context-param>
    <param-name>contextConfigLocation</param-name>
    <!-- 加载src目录下的 applicationContext.xml文件-->
    <param-value>classpath:applicationContext.xml</param-value>
  </context-param>
  <!--设置监听操作-->
  <listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
  </listener>
</web-app>
```



###	3.8.2 mvc-servlet.xml

在 WEB-INF 目录下创建 Spring MVC 的核心配置文件 mvc-servlet.xml，在该文件中仅配置控制器扫描包和视图解析器，具体代码如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context.xsd
    http://www.springframework.org/schema/mvc
    http://www.springframework.org/schema/mvc/spring-mvc.xsd">
    <!-- 使用扫描机制扫描包 -->
    <context:component-scan base-package="com.controller"/>
    <mvc:annotation-driven/>
    <!--不将静态资源给DispatcherServlet处理。-->
    <mvc:default-servlet-handler/>
    <!-- 完成视图的对应 -->
    <!-- 对转向页面的路径解析。prefix：前缀， suffix：后缀 -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="viewClass" value="org.springframework.web.servlet.view.JstlView"/>
        <property name="suffix" value=".jsp"/>
        <property name="prefix" value="/jsp/"/>
    </bean>
</beans>
```



###	3.8.3 applicationContext.xml

在 src 目录下创建 Spring 的配置文件 applicationContext.xml，在该文件中配置数据源、添加事务支持、开启事务注解、配置 MyBatis 工厂、进行 Mapper 代理开发以及指定扫描包。具体代码如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context.xsd">
    <!-- 指定需要扫描的包（包括子包），使注解生效 -->
    <context:component-scan base-package="com"/>
    <!--在Spring容器中托管数据库连接池-->
    <context:property-placeholder location="classpath:db.properties"/>
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="username" value="${db.username}"/>
        <property name="password" value="${db.password}"/>
        <property name="driverClassName" value="${db.driverClass}"/>
        <property name="url" value="${db.url}"/>
    </bean>
    <!-- 配置mybitas工厂，同时指定数据源，并与MyBatis完美整合 -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
        <!-- configLocation的属性值为Mybatis的核心配置文件 -->
        <property name="configLocation" value="classpath:mybatis.xml"/>
        <property name="mapperLocations" value="classpath:com/mapper/*.xml"/>
    </bean>
    <!-- Mapper代理开发，使用Spring自动扫描Mybatis的接口并装配 -->
    <bean id="mapperScannerConfigurer" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <!-- mybatis-spring组件的扫描器 -->
        <property name="basePackage" value="com.mapper"/>
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
    </bean>
</beans>
```



###	3.8.4 mybatis.xml是mybatis的核心配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <settings>
        <setting name="autoMappingBehavior" value="PARTIAL"/>
        <setting name="cacheEnabled" value="true"/>
        <setting name="logImpl" value="STDOUT_LOGGING"/>
    </settings>
    <typeAliases>
        <package name="com.entity"/>
    </typeAliases>
</configuration>
```



###	3.8.5 db.properties配置数据库连接池

```
db.url=jdbc:mysql://localhost:3306/yybsshop?useUnicode=true&characterEncoding=UTF-8
db.driverClass=com.mysql.jdbc.Driver
db.username=root
db.password=8248
```



###	3.8.6 resources/com/mapper下创建 SQL 映射文件

```xml
//EmpMapper.xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.mapper.EmpMapper">
    <select id="selectEmp" resultType="HashMap">
      select * from employee
    </select>
    <insert id="insertEmp" parameterType="HashMap">
      insert into employee(id,name,telephone) values (#{id},#{name},#{telephone})
    </insert>
    <update id="updateEmp" parameterType="HashMap">
        update employee set name=#{name},telephone=#{telephone} where id=#{id}
    </update>
    <delete id="deleteEmp">
        delete from employee where id=#{id}
    </delete>
</mapper>
```



<font style="color:red">springvmc采用经典的三层分层控制结构，在持久层，业务层和控制层分别采用@Repository、@Service、@Controller对分层中的类进行注解，而@Component对那些比较中立的类进行注解</font>

