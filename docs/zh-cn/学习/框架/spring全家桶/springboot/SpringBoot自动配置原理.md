# 1.SpringBoot的核心功能

## 1.1 起步依赖

起步依赖就是把具备某种功能的依赖都打包到一起，提供一些默认功能，以功能作为一个单位



## 1.2 自动配置

自动完成依赖的注入





# 2.工作原理剖析

Spring Boot关于自动配置的源码在spring-boot-autoconfigure-x.x.x.x.jar中：

![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580614829.png)

当然，自动配置原理的相关描述，官方文档貌似是没有提及。不过我们不难猜出，Spring Boot的启动类上有一个@SpringBootApplication注解，这个注解是Spring Boot项目必不可少的注解。那么自动配置原理一定和这个注解有着千丝万缕的联系！

**@EnableAutoConfiguration**

![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580614988.png)

**@SpringBootApplication**是一个复合注解或派生注解，在@SpringBootApplication中有一个注解@EnableAutoConfiguration，翻译成人话就是开启自动配置，其定义如下：

![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580615014.png)

而这个注解也是一个派生注解，其中的关键功能由@Import提供，其导入的**AutoConfigurationImportSelector**的selectImports()方法通过**SpringFactoriesLoader.loadFactoryNames()**扫描所有具有META-INF/spring.factories的jar包。spring-boot-autoconfigure-x.x.x.x.jar里就有一个这样的spring.factories文件。

这个spring.factories文件也是一组一组的key=value的形式，其中一个key是EnableAutoConfiguration类的全类名，而它的value是一个xxxxAutoConfiguration的类名的列表，这些类名以逗号分隔，如下图所示：

![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580615115.png)



这个@EnableAutoConfiguration注解通过@SpringBootApplication被间接的标记在了Spring Boot的启动类上。在SpringApplication.run(...)的内部就会执行selectImports()方法，找到所有JavaConfig自动配置类的全限定名对应的class，然后将所有自动配置类加载到Spring容器中。





# 3.自动配置生效

每一个XxxxAutoConfiguration自动配置类都是在某些条件之下才会生效的，这些条件的限制在Spring Boot中以注解的形式体现，常见的**条件注解**有如下几项：

+ @ConditionalOnBean：当容器里有指定的bean的条件下。
+ @ConditionalOnMissingBean：当容器里不存在指定bean的条件下。
+ @ConditionalOnClass：当类路径下有指定类的条件下。
+ @ConditionalOnMissingClass：当类路径下不存在指定类的条件下。
+ @ConditionalOnProperty：指定的属性是否有指定的值，比如@ConditionalOnProperties(prefix=”xxx.xxx”, value=”enable”, matchIfMissing=true)，代表当xxx.xxx为enable时条件的布尔值为true，如果没有设置的情况下也为true。

以ServletWebServerFactoryAutoConfiguration配置类为例，解释一下全局配置文件中的属性如何生效，比如：server.port=8081，是如何生效的（当然不配置也会有默认值，这个默认值来自于org.apache.catalina.startup.Tomcat）。



在ServletWebServerFactoryAutoConfiguration类上，有一个@EnableConfigurationProperties注解：**开启配置属性**，而它后面的参数是一个ServerProperties类，这就是习惯优于配置的最终落地点。

![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580615204.png)





在下图这个类上，我们看到了一个非常熟悉的注解：**@ConfigurationProperties**，它的作用就是从配置文件中绑定属性到对应的bean上，而**@EnableConfigurationProperties**负责导入这个已经绑定了属性的bean到spring容器中（见上面截图）。那么所有其他的和这个类相关的属性都可以在全局配置文件中定义，也就是说，真正“限制”我们可以在全局配置文件中配置哪些属性的类就是这些**XxxxProperties**类，它与配置文件中定义的prefix关键字开头的一组属性是唯一对应的。

![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580615300.png)



至此，我们大致可以了解。在全局配置的属性如：server.port等，通过@ConfigurationProperties注解，绑定到对应的XxxxProperties配置实体类上封装为一个bean，然后再通过@EnableConfigurationProperties注解导入到Spring容器中。

而诸多的XxxxAutoConfiguration自动配置类，就是Spring容器的JavaConfig形式，作用就是为Spring 容器导入bean，而所有导入的bean所需要的属性都通过xxxxProperties的bean来获得。



可能到目前为止还是有所疑惑，但面试的时候，其实远远不需要回答的这么具体，你只需要这样回答：

>Spring Boot启动的时候会通过@EnableAutoConfiguration注解找到META-INF/spring.factories配置文件中的所有自动配置类，并对其进行加载，而这些自动配置类都是以AutoConfiguration结尾来命名的，它实际上就是一个JavaConfig形式的Spring容器配置类，它能通过以Properties结尾命名的类中取得在全局配置文件中配置的属性如：server.port，而XxxxProperties类是通过@ConfigurationProperties注解与全局配置文件中对应的属性进行绑定的。



通过一张图标来理解一下这一繁复的流程：

![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580615589.png)







# 4.**SpringBoot自定义Starter起步依赖**

## 4.1 制作起步依赖

1. 创建普通maven项目

2. 在main下创建java和resource并Mark Directory as设置所属资源包

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580616387.png)

3. 在java下创建两个包，config和service包

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580616429.png)

4. 书写service层代码，相当于是实体类，类名CustomService

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580616502.png)

5. 书写config配置类，相当于是创建一个对象，给对象设置属性。注意需要声明bean，另外因为此处需要@configuration注解，需要导入pom依赖，pom只需要引入springboot-context即可。

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580616527.png)

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580616580.png)

6. 在resource下创建META-INF/spring.factories，配置加载启动类

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580616615.png)

7. 将此项目在maven中安装成依赖，最好先clean清除再去安装。

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580616777.png)

8. 起步依赖已经制作完成，可以去自己的maven本地仓查看。





## 4.2 springboot项目引入起步依赖

1. 创建一个SpringBoot项目，定制lombok、Thymeleaf和SpringWeb插件即可。

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580616866.png)

2. pom中引入自定义起步依赖

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580616892.png)

3. 创建controller包，在controller包下创建一个java类，用于将测试结果输出在页面上。

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580616959.png)

4. 书写CustomController.java

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580616996.png)

5. 启动项目，浏览器地址栏发送请求，查看页面和控制台结果

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580617018.png)

   浏览器展示结果

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580617079.png)

   发送请求以后，控制台输出内容

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580617143.png)

   这里结果显示在页面上是使用了Thymeleaf，控制层将结果返回出去，然后直接打印在Thymeleaf页面上。

6. 自定义springboot的starter制作和测试完成。