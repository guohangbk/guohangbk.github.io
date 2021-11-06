# 1.事务管理

## 1.1 事物认识

事务Transaction：可以保证数据的一致性和完整性(避免异常和错误等导致的数据信息异常) 。

事务具备ACID四种特性，ACID是Atomic（原⼦性）、Consistency（⼀致性）、Isolation（隔离性）和Durability（持久性）的英⽂缩写。

1. **原⼦性（Atomicity）**

   事务最基本的操作单元，要么全部成功，要么全部失败，不会结束在中间某个环节。事务在执⾏过程中发⽣错误，会被回滚到事务开始前的状态，就像这个事务从来没有执⾏过⼀样。

2. **⼀致性（Consistency）**

   事务的⼀致性指的是在⼀个事务执⾏之前和执⾏之后数据库都必须处于⼀致性状态。如果事务成功地完成，那么系统中所有变化将正确地应⽤，系统处于有效状态。如果在事务中出现错误，那么系统中的所有变化将⾃动地回滚，系统返回到原始状态。

3. **隔离性（Isolation）**

   指的是在并发环境中，当不同的事务同时操纵相同的数据时，每个事务都有各⾃的完整数据空间。由并发事务所做的修改必须与任何其他并发事务所做的修改隔离。事务查看数据更新时，数据所处的状态要么是另⼀事务修改它之前的状态，要么是另⼀事务修改它之后的状态，事务不会查看到中间状态的数据。

4. **持久性（Durability）**

   指的是只要事务成功结束，它对数据库所做的更新就必须永久保存下来。即使发⽣系统崩溃，重新启动数据库系统后，数据库还能恢复到事务成功结束时的状态。

   

## 1.2 事务的传播性

事务传播⾏为就是多个事务⽅法调⽤时，如何定义⽅法间事务的传播。Spring定义了7种传播⾏为：

1. propagation_requierd：如果当前没有事务，就新建⼀个事务，如果已存在⼀个事务中，加⼊到这个事务中，这是Spring默认的选择。（常用：增删改）
2. propagation_supports：⽀持当前事务，如果没有当前事务，就以⾮事务⽅法执⾏。（常用：查询）
3. propagation_mandatory：使⽤当前事务，如果没有当前事务，就抛出异常。
4. propagation_required_new：新建事务，如果当前存在事务，把当前事务挂起。
5. propagation_not_supported：以⾮事务⽅式执⾏操作，如果当前存在事务，就把当前事务挂起。
6. propagation_never：以⾮事务⽅式执⾏操作，如果当前事务存在则抛出异常。
7. propagation_nested：如果当前存在事务，则在嵌套事务内执⾏。如果当前没有事务，则执⾏与propagation_required类似的操作。



## 1.3 事务的几种实现方式

spring支持<font style="color:red">编程式事务管理</font>和<font style="color:red">声明式事务管理</font>两种方式。

- **编程式事务**使用TransactionTemplate或者直接使用底层的PlatformTransactionManager。对于编程式事务管理，spring推荐使用TransactionTemplate。
- **声明式事务**是建立在AOP之上的。其本质是对方法前后进行拦截，然后在目标方法开始之前创建或者加入一个事务，在执行完目标方法之后根据执行情况提交或者回滚事务。声明式事务最大的优点就是不需要通过编程的方式管理事务，这样就不需要在业务逻辑代码中掺杂事务管理的代码，只需在配置文件中做相关的事务规则声明(或通过基于@Transactional注解的方式)，便可以将事务规则应用到业务逻辑中。

显然声明式事务管理要优于编程式事务管理，这正是spring倡导的非侵入式的开发方式。声明式事务管理使业务代码不受污染，一个普通的POJO对象，只要加上注解就可以获得完全的事务支持。和编程式事务相比，声明式事务唯一不足地方是，它的最细粒度只能作用到方法级别，无法做到像编程式事务那样可以作用到代码块级别。但是即便有这样的需求，也存在很多变通的方法，比如，可以将需要进行事务管理的代码块独立为方法等等。

声明式事务管理也有两种常用的方式，一种是基于tx和aop名字空间的xml配置文件，另一种就是基于@Transactional注解。显然基于注解的方式更简单易用，更清爽。



**四种实现方式**

1. 编程式事务管理对基于 POJO 的应⽤来说是唯⼀选择。我们需要在代码中调⽤beginTransaction()、commit()、rollback()等事务管理相关的⽅法，这就是编程式事务管理，这种方式基本不用。

2. 基于 TransactionProxyFactoryBean的声明式事务管理，这种方式也不用。

3. 基于 @Transactional 的声明式事务管理 配置简单，但是每⼀个需要事务的⽅法上都需要加注解。相对灵活⼀些，并且不限层次。优点就是配置简单，缺点每个⽅法都的写 。(<font style="color:red">重点</font>)

   ```xml
   //applicationContext.xml中加入在数据库连接池下
   <!--事物的通知类-->
   <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
       <property name="dataSource" ref="dataSource"/>
   </bean>
   <tx:annotation-driven transaction-manager="transactionManager"/>
   
   //在service实现的类上或者方法上方加此注解
   @Transactional(rollbackFor = Exception.class)
   ```

   

4. 基于Aspectj AOP配置的声明式事务，配置⽐较麻烦，使⽤的时候什么不⽤写。(<font style="color:red">重点</font>)

   ```xml
   <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
       <property name="dataSource" ref="ds"/>
   </bean>
   <tx:advice id="aa" transaction-manager="transactionManager">
       <tx:attributes>
       //这里是说对哪些异常进行事务处理
           <tx:method name="*" rollback-for="Exception" propagation="REQUIRED"/>
       </tx:attributes>
   </tx:advice>
   <aop:config>
       //这里是说对哪个包下的文件进行事务配置
       <aop:pointcut id="tt" expression="execution(* com.service.*.*(..))"/>
       <aop:advisor advice-ref="aa" pointcut-ref="tt"/>
   </aop:config>
   ```

   





# 2.RESTful

## 2.1 什么是REST？

REST（Representational State Transfer）表象化状态转变（表述性状态转变），在2000年被提出，基于HTTP、URI、XML、JSON等标准和协议，支持轻量级、跨平台、跨语言的架构设计。是Web服务的一种新的架构风格（一种思想）。REST是REpresentational State Transfer的缩写（一般中文翻译为表述性状态转移），REST 是一种体系结构。在 REST 中，资源通过 URL 进行识别和定位，然后通过行为(即HTTP方法)来定义REST来完成怎样的功能。



## 2.2 REST架构的主要原则

- 对网络上所有的资源都有一个资源标志符。
- 对资源的操作不会改变标识符。
- 同一资源有多种表现形式（xml、json）
- 所有操作都是无状态的（Stateless）



## 2.3 RESTful介绍

RESTful是一种常见的REST应用，是遵循REST风格的web服务，REST式的web服务是一种ROA（面向资源的架构）。



## 2.4 RESTful资源操作

| **http方法** | **资源操作** | **幂等** | **安全** |
| ------------ | ------------ | -------- | -------- |
| GET          | SELECT       | 是       | 是       |
| POST         | INSERT       | 否       | 否       |
| PUT          | UPDATE       | 是       | 否       |
| DELETE       | DELETE       | 是       | 否       |



## 2.5 RESTful和传统风格的对比资源操作

![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/20200201204044.png)

备注：put请求传递json格式参数



## 2.6 开启HiddenHttpMethodFilter

工作原理是将jsp页面的form表单的method属性值在doFilterInternal方法中转化为标准 的Http方法，即GET、POST、 HEAD、OPTIONS、PUT、DELETE、TRACE，然后到 Controller中找到对应的方法

```xml
//web.xml
<!-- 增加HiddenHttpMethodFilte过滤器：给普通浏览器增加 put|delete请求方式 -->
<filter>
   <filter-name>HiddenHttpMethodFilter</filter-name>
   <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
</filter>

<filter-mapping>
   <filter-name>HiddenHttpMethodFilter</filter-name>
   <url-pattern>/*</url-pattern>
</filter-mapping>
```

```html
<form action="handler/testRest/1234" method="post">
    <input type="hidden"  name="_method" value="DELETE"/>
    <input type="submit" value="删">
</form>
<!-- method必须是POST，然后设置隐藏域的value值（DELETE|PUT）后台进指定的方法中
```

需要注意的是，由于doFilterInternal方法只对method为post的表单进行过滤。同时，HiddenHttpMethodFilter必须作用于dispatcher前

```xml
<!--Servlet不支持PUT表单，需要Spring支持-->
<filter>
    <filter-name>httpPutFormContentFilter</filter-name>
    <filterclass>org.springframework.web.filter.HttpPutFormContentFilter</filterclass>
</filter>
<filter-mapping>
    <filter-name>httpPutFormContentFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

该过滤器只能接受enctype值为application/x-www-formurlencoded的表单



<font style="color:red">**@Controller和@RestController的区别？**</font>

@RestController注解相当于@ResponseBody ＋ @Controller合在一起的作用

1. 如果只是使用@RestController注解Controller，则Controller中的方法无法返回jsp页面，配置的视图解析器InternalResourceViewResolver不起作用，返回的内容就是Return 里的内容。
2. 例如：本来应该到success.jsp页面的，则其显示success.
3. 如果需要返回到指定页面，则需要用 @Controller配合视图解析器InternalResourceViewResolver才行。
4. 如果需要返回JSON，XML或自定义mediaType内容到页面，则需要在对应的方法上加上@ResponseBody注解。





# 3.RESTful风格的增删改查

## 3.1 查询

查询所有员工信息

```java
//java
@RequestMapping(value = "/selectEmpDept",method = RequestMethod.GET)
public List<HashMap> selectEmpDept(){
    List<HashMap> hashMaps = empService.selectEmpDept();
    return hashMaps;
}

//html
$.ajax({
    url:"/Spring06_Restful_war_exploded/emp/selectEmpDept",
    type:"get",
    data:{},
    dataType:"json",
    success:function(data){
        app.emps = data;
    }
})
```

根据id查询员工信息

```java
//java
@RequestMapping(value = "/{id}",method = RequestMethod.GET)
public HashMap selectEmpByEmpno(@PathVariable("id") Integer empno){
    return empService.selectEmpByEmpno(empno);
}

//html
$.ajax({
    url:"/Spring06_Restful_war_exploded/emp/"+empno,
    type:"get",
    data:{},
    dataType:"json",
    success:function(data){
        app.emp = data;
    }
})
```



## 3.2 修改

```java
//java
@RequestMapping(method = RequestMethod.POST)
public ResultData updateEmp(@RequestBody HashMap map){
    int i = empService.updateEmp(map);
    if(i > 0){
        return new ResultData(true,"修改成功",null);
    }else{
        return new ResultData(false,"修改失败",null);
    }
}

//html
$.ajax({
    url:"/Spring06_Restful_war_exploded/emp",
    type:"post",
    data:JSON.stringify(app.emp),
    contentType:"application/json",
    dataType:"json",
    success:function(data) {
        if(data.state){
            alert(data.message);
            window.location.href="/Spring06_Restful_war_exploded/html/empList.html"
        }
    }
});
```



## 3.3 新增

```java
//java
@RequestMapping(method = RequestMethod.PUT)
public ResultData insertEmp(@RequestBody HashMap map){
    int i = empService.insertEmp(map);
    if(i > 0){
        return new ResultData(true,"添加成功",null);
    }else{
        return new ResultData(false,"添加失败",null);
    }
}

//html
$.ajax({
    url:"/Spring06_Restful_war_exploded/emp",
    type:"put",
    data:JSON.stringify(app.emp),
    contentType:"application/json",
    dataType:"json",
    success:function(data) {
        if(data.state){
            alert(data.message);
            window.location.href="/Spring06_Restful_war_exploded/html/empList.html"
        }
    }
});
```



## 3.4 删除

```java
//java
@RequestMapping(value = "/{empno}",method = RequestMethod.DELETE)
public ResultData deleteEmp(@PathVariable("empno") Integer empno){
    int i = empService.deleteEmp(empno);
    if(i > 0){
        return new ResultData(true,"删除成功",null);
    }else{
        return new ResultData(false,"删除失败",null);
    }
}

//html
$.ajax({
    url:"/Spring06_Restful_war_exploded/emp/"+empno,
    type:"delete",
    data:{},
    dataType:"json",
    success:function(data){
        alert(data.message);
        if(data.state){
            window.location.reload();
        }
    }
})
```





# 4.PostMan

## 4.1 背景介绍

用户在开发或者调试网络程序或者是网页B/S模式的程序的时候是需要一些方法来跟踪网页请求的，用户可以使用一些网络的监视工具比如著名的Firebug等网页调试工具。Postman这款网页调试工具不仅可以调试简单的css、html、脚本等简单的网页基本信息，它还可以发送几乎所有类型的HTTP请求！Postman在发送网络HTTP请求方面可以说是Chrome插件类产品中的代表产品之一。



## 4.2 基础功能

![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/20200201204932.png)



## 4.3 主要功能

主要有headers和body需要设置，header主要来存放cookie 信息的，body主要用来存放post或者put的一些数据，比如username ="xxxx"&password="124"还有就是要上传的图片的nsdata数据，get和delete不需要设置这两项。

multipart/form-data：既可以上传文件等二进制数据，也可以上传表单键值对，只是最后会转化为一条信息；x-www-form-urlencoded：只能上传键值对，并且键值对都是间隔分开的。 

body中有4个选项：form-data、x-www-form-urlencoded、raw、binary 

1. form-data：既可以上传键值对，也可以上传⽂件。当上传的字段是⽂件时，会有Content-Type来说明⽂件类型，可以上传多个⽂件。 

2. x-www-form-urlencoded:会将表单内的数据转换为键值对，⽐如,name=java&age = 2，只能上传键值对 

3. raw：可以上传任意格式的⽂本，可以上传text、json、xml、html等

4. binary:只可以上传⼆进制数据，通常⽤来上传⽂件，由于没有键值，所以，⼀次只能上传⼀个⽂件。

   

参考链接：https://blog.csdn.net/fxbin123/article/details/80428216