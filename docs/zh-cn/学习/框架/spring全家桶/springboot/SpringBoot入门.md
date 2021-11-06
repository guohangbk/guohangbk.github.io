# 1.SpringBoot概念

Spring Boot 是由 Pivotal 团队提供的全新框架，其设计目的是用来简化新 Spring 应用的初始搭建以及开发过程。该框架使用了特定的方式来进行配置，从而使开发人员 不再需要定义样板化的配置。用我的话来理解，就是 Spring Boot 其实不是什么新的框 架，它默认配置了很多框架的使用方式，就像 Maven 整合了所有的 Jar 包，Spring Boot 整合了所有的框架。 虽然 Spring 的组件代码是轻量级的，但它的配置却是重量级的。

1. **第⼀阶段：xml配置**

   在Spring 1.x时代，使⽤Spring开发满眼都是xml配置的Bean，随着项⽬的扩⼤，我们需要把 xml配置⽂件放到不同的配置⽂件⾥，那时需要频繁的在开发的类和配置⽂件之间进⾏切换

2. **第⼆阶段：注解配置**

   在Spring 2.5 时代，随着JDK1.5带来的注解⽀持，Spring提供了声明Bean的注解（例如 @Component、@Service），⼤⼤减少了配置量。主要使⽤的⽅式是应⽤的基本配置（如数据库配置）⽤xml，业务配置⽤注解  

3. **第⼆阶段：java配置**

   Spring 4.0 引⼊了基于 Java 的配置能⼒，这是⼀种类型安全的可重构配置⽅式，可以代替 XML。我们⽬前刚好处于这个时代，Spring4.x和Spring Boot都推荐使⽤Java配置。





# 2.SpringBoot特征

1. 可以创建独⽴的Spring应⽤程序，并且基于其Maven或Gradle插件，可以创建可执⾏的 JARs和WARs；
2. 内嵌Tomcat或Jetty等Servlet容器；
3. 提供⾃动配置的“starter”项⽬对象模型（POMS）以简化Maven配置；
4. 尽可能⾃动配置Spring容器；
5. 提供准备好的特性，如指标、健康检查和外部化配置；
6. 绝对没有代码⽣成，不需要XML配置。(虽然没有xml，但是多了java配置类)





# 3.关于SpringBoot的两个策略

SpringBoot框架中还有两个⾮常重要的策略：开箱即⽤和约定优于配置。 

1. 开箱即⽤: Outofbox，是指在开发过程中，通过在MAVEN项⽬的pom⽂件中添加相关依赖 包，然后使⽤对应注解来代替繁琐的XML配置⽂件以管理对象的⽣命周期。这个特点使得开发 ⼈员摆脱了复杂的配置⼯作以及依赖的管理⼯作，更加专注于业务逻辑。 
2. 约定优于配置:  Convention over conﬁguration，是⼀种由SpringBoot本身来配置⽬标结 构，由开发者在结构中添加信息的软件设计范式。这⼀特点虽降低了部分灵活性，增加了BUG 定位的复杂性，但减少了开发⼈员需要做出决定的数量，同时减少了⼤量的XML配置，并且可 以将代码编译、测试和打包等⼯作⾃动化。





# 4.SpringBoot项目基础架构说明

1. 前端常使⽤模板引擎，主要有FreeMarker和Thymeleaf，它们都是⽤Java语⾔编写的，渲染模板并输出相应⽂本，使得界⾯的设计与应⽤的逻辑分离，同时前端开发还会使⽤到 Bootstrap、AngularJS、JQuery等； 
2. 在浏览器的数据传输格式上采⽤Json，⾮xml， 同时提供RESTfulAPI； 
3. SpringMVC框架⽤于数据到达服务器后处理请求； 
4. 到数据访问层主要有Hibernate、MyBatis、JPA等持久层框架； 
5. 数据库常⽤MySQL；
6. 开发⼯具推荐IntelliJIDEA(集成了SpringBoot项目选项)，如果是eclipse，需要⾃⼰添加依赖





# 5.构建SpringBoot项目

1. 创建项目

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580620203.png)

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580620227.png)

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580620238.png)

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580620297.png)

2. pom.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
       <modelVersion>4.0.0</modelVersion>
       <parent>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-parent</artifactId>
           <version>2.2.2.RELEASE</version>
           <relativePath/> <!-- lookup parent from repository -->
       </parent>
       <groupId>com.aaa</groupId>
       <artifactId>boot02_test</artifactId>
       <version>0.0.1-SNAPSHOT</version>
       <name>boot02_test</name>
       <description>Demo project for Spring Boot</description>
   
       <properties>
           <java.version>1.8</java.version>
       </properties>
   
       <dependencies>
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-jdbc</artifactId>
           </dependency>
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-thymeleaf</artifactId>
           </dependency>
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-web</artifactId>
           </dependency>
           <dependency>
               <groupId>org.mybatis.spring.boot</groupId>
               <artifactId>mybatis-spring-boot-starter</artifactId>
               <version>2.1.1</version>
           </dependency>
   
           <dependency>
               <groupId>mysql</groupId>
               <artifactId>mysql-connector-java</artifactId>
               <scope>runtime</scope>
           </dependency>
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-test</artifactId>
               <scope>test</scope>
               <exclusions>
                   <exclusion>
                       <groupId>org.junit.vintage</groupId>
                       <artifactId>junit-vintage-engine</artifactId>
                   </exclusion>
               </exclusions>
           </dependency>
   
           <dependency>
               <groupId>com.alibaba</groupId>
               <artifactId>druid</artifactId>
               <version>1.0.18</version>
           </dependency>
       </dependencies>
   
       <build>
           <plugins>
               <plugin>
                   <groupId>org.springframework.boot</groupId>
                   <artifactId>spring-boot-maven-plugin</artifactId>
               </plugin>
           </plugins>
       </build>
   
   </project>
   ```

   

3. application.properties

   ```xml
   spring.datasource.driver-class-name=com.mysql.jdbc.Driver
   spring.datasource.url=jdbc:mysql://localhost:3306/shop?useUnicode=true&characterEncoding=UTF-8&allowMultiQueries=true&serverTimezone=CTT&zeroDateTimeBehavior=CONVERT_TO_NULL
   spring.datasource.username=root
   spring.datasource.password=8248
   #扫描mapper.xml映射
   mybatis.mapper-locations=classpath:mapper/*.xml
   #同步跳转的时候补前缀和后缀用的
   #spring.thymeleaf.prefix=classpath:/templates/
   #spring.thymeleaf.suffix=.html
   ```

   

4. 在resources下创建mapper文件夹并书写mapper.xml

   ```xml
   //EmpMapper.xml
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
   <mapper namespace="com.aaa.springboot01_01.mapper.EmpMapper">
       <select id="EmpLogin" resultType="HashMap" parameterType="HashMap">
           select * from login where username=#{username} and password=#{password}
       </select>
   
       <select id="selectAll" resultType="com.aaa.springboot01_01.entity.Emp">
       select id, empno, empname, job, mgr, hiredate, salary, comm, deptid
       from emp
     </select>
   </mapper>
   ```

   在java类文件中创建mapper包，书写mapper接口

   ```java
   //EmpMapper.java
   @Mapper
   @Repository
   public interface EmpMapper{
   
       HashMap EmpLogin(HashMap map);
   
       List<Emp> selectAll();
   
   }
   ```

   

5. Controller、Service和Html与Spring基本一致

   

6. 启动类

   ```java
   //启动类:默认扫描的是当前包以及下的子包，如果要扫描其他包，可以用ComponentScan
   @SpringBootApplication
   //@ComponentScan("com")
   public class Springboot0101Application {
   
       public static void main(String[] args) {
           SpringApplication.run(Springboot0101Application.class, args);
       }
   
   }
   ```

   

7. 说明

   **templates⾥边⼀般放的是动态⽣成的东⻄，默认是不能直接访问的，有两个解决办法**

   + 直接将templates开放

     ```xml
     //application.properties
     # 覆 盖 原 有 的 静 态 ⽂ 件 开 放 资 源 
     #默认是
     #spring.thymeleaf.prefix=classpath:/templates/
     #spring.thymeleaf.suffix=.html
     spring.resources.staticlocations=classpath:/templates,classpath:/static,classpath:/public,classpath:/resources, classpath:/META-INF/resources
     ```

     

   + 去后台绕一圈

     ```java
     @Controller
     public class ToPath {
     
         @Autowired
         private Person person;
     
         @RequestMapping("/toIndex")
         public String toIndex(){
             return "index1";
         }
     }
     
     
     
     //application.properties
     #spring.thymeleaf.prefix=classpath:/templates/
     #spring.thymeleaf.suffix=.html
     ```

     

   **mapper接口一定要添加@Mapper注解**







# 6.配置文件

## 6.1 SpringBoot配置文件

SpringBoot使⽤⼀个以application命名的配置⽂件作为默认的全局配置⽂件。⽀持 **properties**后缀结尾的配置⽂件或者以**yml/yaml**后缀结尾的YAML的⽂件配置，以设置应⽤端⼝为例 



properties⽂件示例(application.properties)

```xml
server.port=8888
my.name.age=19
```



YAML⽂件示例(application.yml)：

```yaml
server:
  port: 8089
my:
  name:
   age: 19
```



两个⽂件的内容不冲突的话，两个文件都可以使⽤。如果冲突：properties配置优先级 > YML配置优先级，会使用优先级较高的冲突的配置，不冲突的配置不受影响。

静态资源文件夹的先后顺序:META-INF/resources > resources > static > public



## 6.2 配置文件目录

SpringBoot配置⽂件可以放置在多种路径下，不同路径下的配置优先级有所不同。 可放置⽬录(优先级从⾼到低)

1. ﬁle:./conﬁg/ (当前项⽬路径conﬁg⽬录下); 
2. ﬁle:./ (当前项⽬路径下); 
3. classpath:/conﬁg/ (类路径conﬁg⽬录下); 
4. classpath:/ (类路径conﬁg下). 

优先级由⾼到底，⾼优先级的配置会覆盖低优先级的配置，不同的会互补。 

我们可以从ConﬁgFileApplicationListener这类便可看出，其中DEFAULT_SEARCH_LOCATIONS属性设置了加载的⽬录：

```xml
private static ﬁnal String DEFAULT_SEARCH_LOCATIONS = "classpath:/,classpath:/conﬁg/,ﬁle:./,ﬁle:./conﬁg/"; 
```

先读第一个，如果后边有和第一个冲突，则后边将前边覆盖掉



## 6.3 自定义配置属性

@value：单个属性值注入

```java
@Controller
public class ToPath {

    @Value("${my.name}")
    private String name;

    @Autowired
    private Person person;

    @RequestMapping("/toIndex")
    public String toIndex(){
        System.out.println(name);
        return "index1";
    }
}

//application.properties
my.name=guohang
my.age=22
```

@ConfigurationProperties：批量注入

```java
//pom.xml
<!--批量注入-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>

//Person.java实体类
@Component
@ConfigurationProperties("my")
public class Person {
    private String name;
    private int age;


    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}


//ToPath.java
@Controller
public class ToPath {

    @Value("${my.name}")
    private String name;

    @Autowired
    private Person person;

    @RequestMapping("/toIndex")
    public String toIndex(){
        System.out.println(person.getName()+"---"+person.getAge());
        System.out.println(name);
        return "index1";
    }
}

//application.properties
my.name=guohang
my.age=22
```

@PropertySource+ConfigurationProperties：指定文件注入

@PropertySource(value = {"classpath:config/user.properties"}) 

@ConfigurationProperties(prefix = "user")

```java
@PropertySource(value = {"classpath:config/redis.properties"})
@ConfigurationProperties(prefix="my")
public class Person {
}
```







# 7.SpringBoot+MyBatis整合逆向工程、通用Mapper

1. pom.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
       <modelVersion>4.0.0</modelVersion>
       <parent>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-parent</artifactId>
           <version>2.2.2.RELEASE</version>
           <relativePath/> <!-- lookup parent from repository -->
       </parent>
       <groupId>com.aaa</groupId>
       <artifactId>boot01_02</artifactId>
       <version>0.0.1-SNAPSHOT</version>
       <name>boot01_02</name>
       <description>Demo project for Spring Boot</description>
   
       <properties>
           <java.version>1.8</java.version>
       </properties>
   
       <dependencies>
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-jdbc</artifactId>
           </dependency>
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-thymeleaf</artifactId>
           </dependency>
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-web</artifactId>
           </dependency>
           <dependency>
               <groupId>org.mybatis.spring.boot</groupId>
               <artifactId>mybatis-spring-boot-starter</artifactId>
               <version>2.1.1</version>
           </dependency>
   
           <dependency>
               <groupId>mysql</groupId>
               <artifactId>mysql-connector-java</artifactId>
               <scope>runtime</scope>
           </dependency>
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-test</artifactId>
               <scope>test</scope>
               <exclusions>
                   <exclusion>
                       <groupId>org.junit.vintage</groupId>
                       <artifactId>junit-vintage-engine</artifactId>
                   </exclusion>
               </exclusions>
           </dependency>
           <!--通用mapper-->
           <dependency>
               <groupId>tk.mybatis</groupId>
               <artifactId>mapper</artifactId>
               <version>4.0.3</version>
           </dependency>
           <dependency>
               <groupId>tk.mybatis</groupId>
               <artifactId>mapper-spring-boot-starter</artifactId>
               <version>2.0.3</version>
           </dependency>
       </dependencies>
   
       <build>
           <plugins>
               <plugin>
                   <groupId>org.springframework.boot</groupId>
                   <artifactId>spring-boot-maven-plugin</artifactId>
               </plugin>
               <!--逆向工程-->
               <plugin>
                   <groupId>org.mybatis.generator</groupId>
                   <artifactId>mybatis-generator-maven-plugin</artifactId>
                   <version>1.3.5</version>
                   <executions>
                       <execution>
                           <id>Generate MyBatis Artifacts</id>
                           <phase>deploy</phase>
                           <goals>
                               <goal>generate</goal>
                           </goals>
                       </execution>
                   </executions>
                   <configuration>
                       <configurationFile>${basedir}/src/main/resources/generator/generatorConfig.xml</configurationFile>
                       <overwrite>true</overwrite>
                       <verbose>false</verbose>
                       <contexts>Mysql</contexts>
                   <!-- <contexts>Mysql,SqlServer</contexts>-->
                   </configuration>
                   <dependencies>
                       <dependency>
                           <groupId>org.mybatis.generator</groupId>
                           <artifactId>mybatis-generator-core</artifactId>
                           <version>1.3.5</version>
                       </dependency>
                       <dependency>
                           <groupId>mysql</groupId>
                           <artifactId>mysql-connector-java</artifactId>
                           <version>5.1.46</version>
                       </dependency>
                       <dependency>
                           <groupId>tk.mybatis</groupId>
                           <artifactId>mapper</artifactId>
                           <version>3.4.4</version>
                       </dependency>
                   </dependencies>
               </plugin>
           </plugins>
       </build>
   
   </project>
   ```

   

2. resources/generator/generatorConfig.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE generatorConfiguration
           PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
           "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
   
   <generatorConfiguration>
       <!--MYSQL-->
       <context id="Mysql" targetRuntime="MyBatis3Simple" defaultModelType="flat">
           <property name="javaFileEncoding" value="UTF-8"/>
   
           <!--通用 Mapper 插件，可以生成带注解的实体类-->
           <plugin type="tk.mybatis.mapper.generator.MapperPlugin">
               <property name="mappers" value="com.aaa.commMapper.CommMapper"/>
               <property name="caseSensitive" value="true"/>
               <property name="forceAnnotation" value="true"/>
               <property name="beginningDelimiter" value="`"/>
               <property name="endingDelimiter" value="`"/>
           </plugin>
   
           <commentGenerator>
               <!-- 是否去除自动生成的注释 true：是 ： false:否 -->
               <property name="suppressAllComments" value="true"/>
               <property name="suppressDate" value="true"/>
               <property name="addRemarkComments" value="true"/>
           </commentGenerator>
   
           <!-- 数据库链接URL、用户名、密码 -->
           <jdbcConnection driverClass="com.mysql.jdbc.Driver"
                           connectionURL="jdbc:mysql://localhost:3306/shop?useUnicode=true&amp;characterEncoding=utf8"
                           userId="root"
                           password="8248">
           </jdbcConnection>
   
           <!-- 生成模型的包名和位置 -->
           <javaModelGenerator targetPackage="com.aaa.entity" targetProject="src/main/java">
               <!-- 是否  自动为每一个生成的类创建一个构造方法-->
               <property name="constructorBased" value="false"/>
               <property name="useActualColumnNames" value="true" />
               <!-- 在targetPackage的基础上，根据数据库的schema再生成一层package-->
               <property name="enableSubPackages" value="false"/>
               <!-- 是否创建一个不可变的类 -->
               <property name="immutable" value="false"/>
               <!-- 设置是否在getter方法中，对String类型字段调用trim()方法 -->
               <property name="trimStrings" value="true"/>
           </javaModelGenerator>
   
           <!-- 生成的映射文件包名和位置 -->
           <sqlMapGenerator targetPackage="mapper" targetProject="src/main/resources">
               <property name="enableSubPackages" value="true"/>
           </sqlMapGenerator>
   
           <javaClientGenerator targetPackage="com.aaa.mapper" targetProject="src/main/java" type="XMLMAPPER">
               <property name="enableSubPackages" value="true"/>
           </javaClientGenerator>
   
           <table tableName="login" domainObjectName="Login"
                  enableCountByExample="false" enableUpdateByExample="false" enableDeleteByExample="false"
                  enableSelectByExample="false" selectByExampleQueryId="false">
               <!--mysql 配置-->
               <generatedKey column="id" sqlStatement="SELECT LAST_INSERT_ID()" identity="true"/>
           </table>
   
           <table tableName="emp" domainObjectName="Emp"
                  enableCountByExample="false" enableUpdateByExample="false" enableDeleteByExample="false"
                  enableSelectByExample="false" selectByExampleQueryId="false">
               <!--mysql 配置-->
               <generatedKey column="id" sqlStatement="SELECT LAST_INSERT_ID()" identity="true"/>
           </table>
   
       </context>
   </generatorConfiguration>
   ```

   

3. application.properties

   ```xml
   spring.datasource.driver-class-name=com.mysql.jdbc.Driver
   spring.datasource.url=jdbc:mysql://localhost:3306/shop?useUnicode=true&characterEncoding=UTF-8&allowMultiQueries=true&serverTimezone=CTT&zeroDateTimeBehavior=CONVERT_TO_NULL
   spring.datasource.username=root
   spring.datasource.password=8248
   #扫描mapper.xml映射
   mybatis.mapper-locations=classpath:mapper/*.xml
   ```

   

4. 创建mapper、entity、controller、service、commMapper包，启动逆向工程

   ```java
   //CommMapper.java
   //这里是引入的tk.mybatis包，因为在mapper层要引入@Mapper注解，可能会冲突，所以在这里创建一个接口继承Mapper
   public interface CommMapper<T> extends Mapper<T> {
   }
   ```

   

5. 在mapper层的接口中注意添加注解

   ```java
   @Mapper //该注解可以让类自动被映射，交给Spring管理
   @Repository
   public interface EmpMapper extends CommMapper<Emp> {
   }
   ```

   

6. service层、controller层和Spring一样，可以直接在service层调用tk.mybatis的方法

   

<font style="color:red">tips：通用mapper的修改方法如果没有主键，则需要自定义检索条件</font>

```java
@Override
    public int updateEmp(Emp emp) {//传入的是对象
    Integer empno = emp.getEmpno();  //拿对象中的empno
    Example example=new Example(Emp.class);
    Example.Criteria criteria=example.createCriteria(); 
    criteria.andEqualTo("empno",empno);  //设定检索列为表中的empno，并设置值
    return empMapper.updateByExampleSelective(emp,example);//调用方法，传入对象和检索条件
}
```







# 8.SpringBoot的拦截器

1. interceptor包/LoginInterceptor .java

   ```java
   //拦截条件
   //LoginInterceptor .java
   public class LoginInterceptor implements HandlerInterceptor {
   
       @Override
       public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
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
   
       @Override
       public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
   
       }
   
       @Override
       public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
   
       }
   
   }
   ```

   

2. java下创建一个config包/InterceptorConfig.java

   ```java
   //通过配置类注册拦截器
   //InterceptorConfig.java
   @Configuration
   public class InterceptorConfig implements WebMvcConfigurer {
   
       @Override
       public void addInterceptors(InterceptorRegistry registry) {
           //需要拦截的路径，/**表示需要拦截所有请求
           String[]addPathPatterns={"/**"};
           //不需要拦截的路径
           String [] excludePathPaterns={
                   "/user/**",
                   "/index.html",
                   "/boot/put",
                   "/myservlet"
           };
           //注册一个登录拦截器
           registry.addInterceptor(new LoginInterceptor())
                   .addPathPatterns(addPathPatterns)
                   .excludePathPatterns(excludePathPaterns);
           //注册一个权限拦截器  如果有多个拦截器 ，只需要添加以下一行代码
           //registry.addInterceptor(new LoginInterceptor())
           // .addPathPatterns(addPathPatterns)
           // .excludePathPatterns(excludePathPatterns);
   
       }
   
   }
   ```

   

