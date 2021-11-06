#	1.Spring简介

Spring 是⼀个开源框架，是为了解决企业应⽤程序开发复杂性⽽创建 的。框架的主要优势之⼀就是其分层架构，分层架构允许选择使⽤哪⼀个 组件，同时为 J2EE 应⽤程序开发提供集成的框架。 

![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1579960228.png)

##	1.1 Spring框架的七个模块

1. 核⼼容器(Spring Core)：核⼼容器提供 Spring 框架的基本功能。核⼼容器的主要组件是 BeanFactory，它是⼯⼚模式的实现。BeanFactory 使⽤控制反转（IOC）模式将应⽤程序的配置和依赖性规范与实际的应⽤程序代码分开。
2. Spring 上下⽂(Spring Context)：Spring 上下⽂是⼀个配置⽂件，向 Spring 框架提供上下⽂信息。Spring 上下⽂包括企业服务，例如 JNDI、EJB、电⼦邮件、国际化、校验和调度功能。
3. Spring AOP：通过配置管理特性，Spring AOP 模块直接将⾯向⽅⾯的编程功能集成到了 Spring 框架中。所以，可以很容易地使 Spring 框架管理的任何对象⽀持 AOP。Spring AOP 模块为基于 Spring 的应⽤程序中的对象提供了事务管理服务。通过使⽤ Spring AOP，不⽤依赖 EJB 组件，就可以将声明性事务管理集成到应⽤程序中。
4. Spring DAO：JDBC DAO 抽象层提供了有意义的异常层次结构，可⽤该结构来管理异常处理和不同数据库供应商抛出的错误消息。异常层次结构简化了错误处理，并且极⼤地降低了需要编写的异常代码数量（例如打开和关闭连接）。Spring DAO 的⾯向 JDBC 的异常遵从通⽤的 DAO 异常层次结构。
5. Spring ORM：Spring 框架插⼊了若⼲个 ORM 框架，从⽽提供了 ORM 的对象关系⼯具，其中包括 JDO、Hibernate 和 iBatis SQL Map。所有这些都遵从 Spring 的通⽤事务和DAO 异常层次结构。
6. Spring Web 模块：Web 上下⽂模块建⽴在应⽤程序上下⽂模块之上，为基于 Web 的应⽤程序提供了上下⽂。所以，Spring 框架⽀持与 Jakarta Struts 的集成。Web 模块还简化了处理多部分请求以及将请求参数绑定到域对象的⼯作。
7. Spring MVC 框架：MVC 框架是⼀个全功能的构建 Web 应⽤程序的 MVC 实现。通过策 略接⼝，MVC 框架变成为⾼度可配置的，MVC 容纳了⼤量视图技术，其中包括 JSP、 Velocity、Tiles、iText 和 POI。



##	1.2 使用Spring框架的好处

- Spring 可以使开发人员使用 POJOs 开发企业级的应用程序。只使用 POJOs 的好处是你不需要一个 EJB 容器产品，比如一个应用程序服务器，但是你可以选择使用一个健壮的 servlet 容器，比如 Tomcat 或者一些商业产品。
- Spring 在一个单元模式中是有组织的。即使包和类的数量非常大，你只要担心你需要的，而其它的就可以忽略了。
- Spring 不会让你白费力气做重复工作，它真正的利用了一些现有的技术，像ORM 框架、日志框架、JEE、Quartz 和 JDK 计时器，其他视图技术。
- 测试一个用 Spring 编写的应用程序很容易，因为环境相关的代码被移动到这个框架中。此外，通过使用 JavaBean-style POJOs，它在使用依赖注入注入测试数据时变得更容易。
- Spring 的 web 框架是一个设计良好的 web MVC 框架，它为比如 Structs 或者其他工程上的或者不怎么受欢迎的 web 框架提供了一个很好的供替代的选择。MVC模式导致应用程序的不同方面(输入逻辑，业务逻辑和UI逻辑)分离，同时提供这些元素之间的松散耦合。

- - 模型(Model)封装了应用程序数据，通常它们将由POJO类组成。
  - 视图(View)负责渲染模型数据，一般来说它生成客户端浏览器可以解释HTML输出。
  - 控制器(Controller)负责处理用户请求并构建适当的模型，并将其传递给视图进行渲染。

- Spring 对JavaEE开发中非常难用的一些API（JDBC、JavaMail、远程调用等），都提供了封装，使这些API应用难度大大降低。
- 轻量级的 IOC 容器往往是轻量级的，例如，特别是当与 EJB 容器相比的时候。这有利于在内存和 CPU 资源有限的计算机上开发和部署应用程序。
- Spring提供了一致的事务管理接口，可向下扩展到（使用一个单一的数据库，例如）本地事务并扩展到全局事务（例如，使用 JTA）。



##	1.3 依赖注入（DI）

Spring 最认同的技术是控制反转的**依赖注入（DI）**模式。控制反转（IoC）是一个通用的概念，它可以用许多不同的方式去表达，依赖注入仅仅是控制反转的一个具体的例子。

当编写一个复杂的 Java 应用程序时，应用程序类应该尽可能的独立于其他的 Java 类来增加这些类可重用可能性，当进行单元测试时，可以使它们独立于其他类进行测试。依赖注入（或者有时被称为配线）有助于将这些类粘合在一起，并且在同一时间让它们保持独立。

到底什么是依赖注入？让我们将这两个词分开来看一看。这里将依赖关系部分转化为两个类之间的关联。例如，类 A 依赖于类 B。现在，让我们看一看第二部分，注入。所有这一切都意味着类 B 将通过 IoC 被注入到类 A 中。

依赖注入可以以向构造函数传递参数的方式发生，或者通过使用 setter 方法 post-construction。由于依赖注入是 Spring 框架的核心部分，所以我将在一个单独的章节中利用很好的例子去解释这一概念。





#	2.IOC容器

**IOC 容器**具有依赖注入功能的容器，它可以创建对象，IOC 容器负责实例化、定位、配置应用程序中的对象及建立这些对象间的依赖。通常new一个实例，控制权由程序员控制，而"控制反转"是指new实例工作不由程序员来做而是交给Spring容器来做。在Spring中BeanFactory是IOC容器的实际代表者。

IOC也称之为控制反转，标准来说与Spring⽆关，是一种设计模式，Spring提供了IOC的实现(依赖注入)。

<font style="color:red">依赖注入是⽤于控制对象之间的相互引⽤关系以降低对象之间的耦合度，依赖注⼊只在运⾏时将被依赖对象注⼊到对象中。即将每⼀个类看成⼀个组件，互相组合组成模型，这个组合关系由容器管理。IOC就是Spring的核⼼容器。</font>

控制反转模式（也称作依赖性介⼊）的基本概念是：不创建对象，但是描述创建它们的⽅式。在代码中不直接描述类和类之间的关系，这些关系交给容器管理。容器 （在 Spring 框架中是 IOC 容器） 负责将这些联系在⼀起。以后⼯程不new对象，对象交给容器进⾏创建。



##	2.1 传统无IOC的三层架构

Contoller依赖Service   

service依赖Dao层

Dao层依赖DBA

```
//DAO
public class EmpMapper Impl implements EmpMapper {
    @override
    public List select() {
        System.out.printIn("select检索");
        return null;
    }
}

//Service
public class EmpServiceImpl implements EmpService {
    //EmpServiceImpl依赖EmpMapper Impl
    EmpMapper empMapper= new EmpMapperImpl();
    @Override
    public List select() {
        return empMapper.select() ;
    }
}

//Controller
//I0C控制反转，
//解决的就是自己new对象之后，两个类之间有耦合，如果另外一个类改变需要同步改变
public class EmpController {
    public static void main(String[] args) {
    //EmpController依赖EmpServiceImpl
    EmpService empService=new EmpServiceImpl();
        empService.select() ;
    }
}
```



##	2.2 IOC引入的架构

1. 改造new对象的位置----⽣成成员变量，为其添加set⽅法；get⽅法可有可⽆ 

   ```
   public class StudentServiceImpl implements StudentService {
       private StudentDao StudentDao;
   
       public StudentServiceImpl() {
       }
       //set方法进行注入
       public void setStudentDao(StudentDao studentDao) {
           this.StudentDao = studentDao;
       }
       @Override
       public List selectStudent() {
           StudentDao.selectStudent();
           return null;
       }
   }
   ```

   

2. 书写Spring配置⽂件:applicationContext.xml

   ```
   <?xml version="1.0" encoding="UTF-8"?>
   <beans
           xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://www.springframework.org/schema/beans
      http://www.springframework.org/schema/beans/spring-beans-3.1.xsd">
       <bean id="StudentDao" class="com.dao.Impl.StudentDaoImpl"></bean>
       
       <bean id="StudentService" class="com.service.Impl.StudentServiceImpl">
           <property name="StudentDao" ref="StudentDao"/>
       </bean>
   </beans>
   ```

   

3. bean的获取：

   ```
   public class StudentController {
       @Test
       public void selectStudent(){
           ApplicationContext applicationContext = new ClassPathXmlApplicationContext("ApplicationContext.xml");
           StudentService studentService = (StudentService) applicationContext.getBean("StudentService");
           studentService.selectStudent();
       }
   }
   ```

   



#	3.Spring IOC详解

##	3.1 Spring 提供了两种不同类型的容器

1. Spring BeanFactory 容器它是最简单的容器，给 DI 提供了基本的支持，它用 org.springframework.beans.factory.BeanFactory 接口来定义。BeanFactory 或者相关的接口，如 BeanFactoryAware，InitializingBean，DisposableBean，在 Spring 中仍然存在具有大量的与 Spring 整合的第三方框架的反向兼容性的目的。BeanFactory 是IOC容器的核⼼接⼝，它的职责包括：实例化、定位、配置应⽤程序中的对象及建⽴这些对象间的依赖。

   **BeanFactory接⼝包含以下基本⽅法**

   - 1. boolean containsBean(String beanName) 判断⼯⼚中是否包含给定名称的bean定义，若有则返回true

     2. Object getBean(String) 返回给定名称注册的bean实例。根据bean的配置情况，如果是singleton模式将返回⼀个共享实例，否则将返回⼀个新建的实例，如果没有找到指定bean,该⽅法可能会抛出异常

     3. Object getBean(String, Class) 返回以给定名称注册的bean实例，并转换为给定class类型

     4. Class getType(String name) 返回给定名称的bean的Class,如果没有找到指定的bean实例，则排除NoSuchBeanDeﬁnitionException异常

     5. boolean isSingleton(String) 判断给定名称的bean定义是否为单例模式

     6. String[] getAliases(String name) 返回给定bean名称的所有别名

        

2. Spring ApplicationContext 容器该容器添加了更多的企业特定的功能，例如从一个属性文件中解析文本信息的能力，发布应用程序事件给感兴趣的事件监听器的能力。该容器是由 org.springframework.context.ApplicationContext 接口定义。 ApplicationContext是基于BeanFactory之上的，提供了应⽤程序框架服务，扩展的新功能如下： 提供国际化的⽀持、资源访问(如URL和⽂件)、事件传递、载⼊多个配置⽂件等

   ApplicationContext 容器包括 BeanFactory 容器的所有功能，所以通常建议超过 BeanFactory。BeanFactory 仍然可以用于轻量级的应用程序，如移动设备或基于 applet 的应用程序，其中它的数据量和速度是显著。

   ClassPathXmlAPplicationContext：xml---classpath路径加载xml⽂件的 

   FileSystemXmlApplicationContext：基于项⽬根路径进⾏加载xml⽂件的 

   AnnotationConﬁgApplicationContext：基于注解的配置。基于类书写的配置

   WebApplicationContext：web⼯程整合后



##	3.2 Bean的定义

配置⽂件中定义Bean。 

Spring将类都做为Bean进⾏管理，bean和bean之间关系其实就是类和类之间的关系。

Spring以Bean的⽅式管理所有的组件，J2EE的全部组件都被视为Bean管理。即所有类对Spring容器⽽⾔都是以类的形式存在的。对Spring⽽⾔，所有类都是Bean，同bean管理类，给类起名字，类和类之间的关系也是bean之间的关系。

Spring负责创建Bean的实例，并管理其⽣命周期。

Bean配置信息定义了Bean的实现及依赖关系，Spring容器根据各种形式的Bean配置信息在容器内部建⽴Bean定义注册表，然后根据注册表加载、实例化Bean，并建⽴Bean和Bean的依赖关系，最后将这些准备就绪的Bean放到Bean缓存池中，以供外层的应⽤程序进⾏调⽤。

Bean在Spring的容器有两种基本⾏为

**singleton**：单态（默认），Spring容器⾥只有⼀个共享的实例存在 

**non-singleton 或 prototype**：原型 ，每次请求ID的bean都将产⽣新的实例 



**Bean 与 Spring 容器的关系**

下图表达了Bean 与 Spring 容器之间的关系：

![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1579960878.jpeg)



##	3.3 Bean的配置

Spring IoC 容器完全由实际编写的配置元数据的格式解耦。有下面三个重要的方法把配置元数据提供给 Spring 容器：

- 基于 XML 的配置文件
- 基于注解的配置
- 基于 Java 的配置

```
//配置文件头
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">
</beans>
```



###	3.3.1 Bean的定义

- 1. id：是⼀个唯⼀性标志，可以不写
  2. name：给bean起名字，但是可以重名，可以不写 
  3. class：全类名。报名.类名；必须写。
  4. scope：用于定义类的范围。在spring2.0之前bean只有2种作⽤域即：
  5. singleton(单例)、non-singleton（也称 prototype）, Spring2.0以后，增加了session、request、global session三种专⽤于Web应⽤程序上下⽂的Bean。
     + singleton 作⽤域：单例。所有请求都是⼀个对象。
     + prototype：⾮单例。每次请求都是⼀个新的对象。
     + request：request表示该针对每⼀次HTTP请求都会产⽣⼀个新的bean，同时该 bean仅在当前HTTP request内有效
     + session：session作⽤域表示该针对每⼀次HTTP请求都会产⽣⼀个新的bean，同时该bean仅在当前HTTP session内有效：
     + global session：类似于标准的HTTP Session作⽤域，不过它仅仅在基于portlet 的web应⽤中才有意义。
  6.  autowire：byName根据属性名⾃动去配置中找名字或者ID为属性名的。 byType根据数据类型去配置⽂件中找，如果找到⼀个那么就注⼊，如果找到两个直接报错。
  7. constructor：类似于 byType，但该类型适⽤于构造函数参数类型。如果在容器 中没有⼀个构造函数参数类型的 bean，则⼀个致命错误将会发⽣。 
  8. no：这是默认设置，⽆⾃动注⼊
  9. property单例：配置属性的。
  10. name：书写类的对应的成员变量名
  11. ref：引⼊对象的。——new对象注⼊到对应的属性中
  12. value：字符串、数字——具体的值——会讲值直接复制给属性。



###	3.3.2 基于 XML 的配置文件

- 1. **基于set方法配置**

     ```
     public class StudentServiceImpl implements StudentService {
         private StudentDao StudentDao;
     
         public StudentServiceImpl() {
         }
     
         public void setStudentDao(StudentDao studentDao) {
             this.StudentDao = studentDao;
         }
     
         @Override
         public List selectStudent() {
             StudentDao.selectStudent();
             return null;
         }
     }
     ```

     

  2. **基于构造配置**

     基于构造⽅法注⼊，如果index有0那么new的时候调⽤的时候有⼀个参数的有参构造，如果有01调⽤的是两个参数的有参构造，如果没有任何的constructor-arg,那么调⽤的⽆参构 造。并且顺序必须⼀致。

     ```
     <bean id="studentService" class="com.service.StudentServiceImpl" scope="singleton">
         <constructor-arg index="0" ref="studentDAO"/>
         <constructor-arg index="1" value="dddded"/> 
     </bean>
     ```

     改造类：必须有构造⽅法，set⽅法可以不要

     ```
     //set⽅法进⾏注⼊:必须有set⽅法:需要依赖⽆参构造;⾃动注⼊都需要依赖⽆参构造 
     private StudentDAO studentDAO; 
     private String propetyName; 
     public StudentServiceImpl(StudentDAO studentDAO,String propetyName){
         this.studentDAO=studentDAO;    
         this.propetyName=propetyName; 
     } 
     //⼀般情况写了有参构造都写⼀个⽆参构造 
     public StudentServiceImpl(){}
     ```

     

  3. **两种XML方式的比较**

     设置注⼊的优点 :调⽤⽆参构造先创建，然后调⽤set⽅法进⾏赋值 如果依赖关系（或继承关系）较为复杂，设置注⼊模式往往更为简洁 有些地⽅有其局限性 直观，更加⾃然

     构造注⼊的优点：new的同时直接赋值，构造器即创建⼀个完整、合法的对象 避免了繁琐的setter⽅法的编写 ⽆需担⼼在调⽤过程中执⾏setter⽅法对组件依赖关系产⽣破坏 在构造函数中决定依赖关系的注⼊顺序



###	3.3.3 基于注解的配置

- 1. @Component：指定管理的Bean：componet可以不起名字，只能autowired根据类型匹 配，如果类型匹配多个会报错。 
  2. @scope：指定bean作⽤的范围 
  3. @Autowired：指定⾃动装配依赖，默认根据类型⾃动注⼊。如果⼀个类型匹配出来多个，会⾃动继续根据name进⾏匹配，如果名字也匹配不上才报错。 
  4. @Qualiﬁer：⽤@Autowired⾃动匹配时，如果有多个同类型对象则需要⽤该注解指定注⼊ Bean的名称。名字不⼀致的时候才需要 
  5. @Resource([name=''],[type=''])：通过名字或类型⾃动注⼊资源@Resource(name="empMapper1")。resource实现显示注⼊。可以根据名 称可以根据类型。这个默认根据name注⼊。
  6. @PostConstruct：Bean在构造完成时调⽤的⽅法
  7. @PreDestroy：Bean在销毁之前调⽤的⽅法

***代码示例***

```
//ApplicationContext.xml
<?xml version="1.0" encoding="UTF-8"?>
<beans
        xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:context="http://www.springframework.org/schema/context"
        xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.1.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context.xsd">
    <context:component-scan base-package="com"/>

</beans>

//StudentDao
public interface StudentDao {
    List selectStudent();
}

//StudnetDaoImpl
@Component
public class StudnetDaoImpl implements StudentDao {
    @Override
    public List selectStudent() {
        System.out.println("查询所有学生信息。。。");
        return null;
    }
}

//StudentService
public interface StudentService {
    List selectStudent();
}

//StudentServiceImpl
@Component("StudentService")
public class StudentServiceImpl implements StudentService {
//set⽅法进⾏注⼊:必须有set⽅法:需要依赖⽆参构造;⾃动注⼊都需要依赖⽆参构造    
//⾃动注⼊：autowired默认根据类型注⼊；如果类型注⼊可以⾃动匹配名称⼀致。 
    @Autowired
    private StudentDao studentDao;

    public StudentServiceImpl() {
    }

    public void setStudentDao(StudentDao studentDao) {
        this.studentDao = studentDao;
    }

    @Override
    public List selectStudent() {
        studentDao.selectStudent();
        return null;
    }
}

//StudentController
public class StudentController {
    @Test
    public void selectStudent(){
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("ApplicationContext.xml");
        StudentService studentService = (StudentService) applicationContext.getBean("StudentService");
        studentService.selectStudent();
    }
}
```

​	

###	3.3.4 基于 Java 的配置

基于 Java 的配置选项，可以使你在不用配置 XML 的情况下编写大多数的 Spring，但是一些有帮助的基于 Java 的注解，解释如下：

**@Configuration 和 @Bean 注解**

@Configuration 的注解类表示这个类可以使用 Spring IoC 容器作为 bean 定义的来源。

@Bean 注解告诉 Spring，一个带有 @Bean 的注解方法将返回一个对象，该对象应该被注册为在 Spring 应用程序上下文中的 bean。最简单可行的 @Configuration 类如下所示：

```
package com.tutorialspoint;
import org.springframework.context.annotation.*;
@Configuration
public class HelloWorldConfig {
   @Bean 
   public HelloWorld helloWorld(){
      return new HelloWorld();
   }
}
//上面的代码将等同于下面的 XML 配置：
<beans>
   <bean id="helloWorld" class="com.tutorialspoint.HelloWorld" />
</beans>
//在这里，带有 @Bean 注解的方法名称作为 bean 的 ID，它创建并返回实际的 bean。
```



***案例演示***

```
//ApplicationConfig.java
@Configuration
public class ApplicationConfig {

    @Bean(name="studentDao")
    public StudentDao studentDao(){
        return new StudentDaoImpl();
    }

    @Bean(name = "studentService")
    public StudentService studentService(){
        StudentServiceImpl studentService = new StudentServiceImpl();
        studentService.setParam("dddsdsd");
        studentService.setStudentDao(studentDao());
        return studentService;
    }
}

//StudentDao
public interface StudentDao {
    List selectStudent();
}

//StudentDaoImpl
public class StudentDaoImpl implements StudentDao {
    @Override
    public List selectStudent() {
        System.out.println("查询所有学生...");
        return null;
    }
}

//StudentService
public interface StudentService {
    List selectStudent();
}

//StudentServiceImpl
public class StudentServiceImpl implements StudentService {

    private StudentDao studentDao;
    private String param;

    public StudentServiceImpl() {
    }

    public void setStudentDao(StudentDao studentDao) {
        this.studentDao = studentDao;
    }

    public void setParam(String param) {
        this.param = param;
    }

    @Override
    public List selectStudent() {
        studentDao.selectStudent();
        System.out.println(param);
        return null;
    }
}

//StudentController
public class StudentController {
    @Test
    public void selectStudent(){
        AnnotationConfigApplicationContext annotationConfigApplicationContext = new AnnotationConfigApplicationContext(ApplicationConfig.class);
        StudentService studentService = (StudentService) annotationConfigApplicationContext.getBean("studentService");
        studentService.selectStudent();
    }
}
```

