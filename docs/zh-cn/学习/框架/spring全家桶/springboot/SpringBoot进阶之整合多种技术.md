# 1.整合PageHelper进行分页

## 1.1 引入分页插件依赖

```xml
<!--pagehelper分页-->
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper-spring-boot-starter</artifactId>
    <version>1.2.5</version>
</dependency>
```



## 1.2 修改配置文件

```yaml
#pagehelper分页插件
pagehelper:
    helper-dialect: mysql
    reasonable: true
    params: count=countSql
    support-methods-arguments: true
```



## 1.3 编写分页实体类

```java
public class PageEntity {
    private int pageNum;//当前第几页
    private int pageSize;//每页多条
    private int allPage;
    private int allRows;
    private Object data;

    public int getPageNum() {
        return pageNum;
    }

    public void setPageNum(int pageNum) {
        this.pageNum = pageNum;
    }

    public int getPageSize() {
        return pageSize;
    }

    public void setPageSize(int pageSize) {
        this.pageSize = pageSize;
    }

    public int getAllPage() {
        return allPage;
    }

    public void setAllPage(int allPage) {
        this.allPage = allPage;
    }

    public int getAllRows() {
        return allRows;
    }

    public void setAllRows(int allRows) {
        this.allRows = allRows;
    }

    public Object getData() {
        return data;
    }

    public void setData(Object data) {
        this.data = data;
    }
}
```





## 1.4 编写分页查询方法

```java
//EmpServiceImpl.java
@Service
public class EmpServiceImpl implements EmpService {

    @Autowired
    private EmpMapper empMapper;

    @Override
    public PageInfo<Emp> selectAllEmpPage(PageEntity pageEntity) {
        //开启分页，传入页数和每页的条数
        PageHelper.startPage(pageEntity.getPageNum(),pageEntity.getPageSize());
        //查询所有数据放入emps中
        List<Emp> emps = empMapper.selectAll();
        //使用PageInfo，放入数据
        PageInfo<Emp> empPageInfo = new PageInfo<>(emps);
        return empPageInfo;
    }
}
```







# 2.开启日志打印

## 2.1 ⽇志级别

log4j定义了8个级别的log（除去OFF和ALL，可以说分为6个级别），优先级从高到低依次为：OFF、FATAL、ERROR、WARN、INFO、DEBUG、TRACE、 ALL。

1. ALL 最低等级的，用于打开所有日志记录。
2. TRACE designates finer-grained informational events than the DEBUG.Since:1.2.12，很低的日志级别，一般不会使用。
3. DEBUG 指出细粒度信息事件对调试应用程序是非常有帮助的，主要用于开发过程中打印一些运行信息。
4. INFO 消息在粗粒度级别上突出强调应用程序的运行过程。打印一些你感兴趣的或者重要的信息，这个可以用于生产环境中输出程序运行的一些重要信息，但是不能滥用，避免打印过多的日志。
5. WARN 表明会出现潜在错误的情形，有些信息不是错误信息，但是也要给程序员的一些提示。
6. ERROR 指出虽然发生错误事件，但仍然不影响系统的继续运行。打印错误和异常信息，如果不想输出太多的日志，可以使用这个级别。
7. FATAL 指出每个严重的错误事件将会导致应用程序的退出。这个级别比较高了。重大错误，这种级别你可以直接停止程序了。
8. OFF 最高等级的，用于关闭所有日志记录。

如果将log level设置在某一个级别上，那么比此级别优先级高的log都能打印出来。例如，如果设置优先级为WARN，那么OFF、FATAL、ERROR、WARN 4个级别的log能正常输出，而INFO、DEBUG、TRACE、 ALL级别的log则会被忽略。Log4j建议只使用四个级别，优先级从高到低分别是ERROR、WARN、INFO、DEBUG。

从我们实验的结果可以看出，log4j默认的优先级为ERROR或者WARN（实际上是ERROR）。



## 2.2 案例演示

1. 编写logback-spring.xml，记录在本地项目所在盘根目录

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <configuration >
       <!-- 彩色日志 -->
       <!-- 彩色日志依赖的渲染类 -->
       <conversionRule conversionWord="clr" converterClass="org.springframework.boot.logging.logback.ColorConverter" />
       <conversionRule conversionWord="wex" converterClass="org.springframework.boot.logging.logback.WhitespaceThrowableProxyConverter" />
       <conversionRule conversionWord="wEx" converterClass="org.springframework.boot.logging.logback.ExtendedWhitespaceThrowableProxyConverter" />
       <!-- 彩色日志格式 -->
       <property name="CONSOLE_LOG_PATTERN" value="${CONSOLE_LOG_PATTERN:-%clr(%d{yyyy-MM-dd HH:mm:ss.SSS}){faint} %clr(${LOG_LEVEL_PATTERN:-%5p}) %clr(${PID:- }){magenta} %clr(---){faint} %clr([%15.15t]){faint} %clr(%logger){cyan} %clr(:){faint} %m%n${LOG_EXCEPTION_CONVERSION_WORD:-%wEx}}" />
       <!--包名输出缩进对齐-->
       <property name="CONSOLE_LOG_PATTERN" value="${CONSOLE_LOG_PATTERN:-%clr(%d{yyyy-MM-dd HH:mm:ss.SSS}){faint} %clr(${LOG_LEVEL_PATTERN:-%5p}) %clr(${PID:- }){magenta} %clr(---){faint} %clr([%15.15t]){faint} %clr(%-40.40logger{39}){cyan} %clr(:){faint} %m%n${LOG_EXCEPTION_CONVERSION_WORD:-%wEx}}" />
   
   
       <contextName>fanxlxs</contextName>
   
       <!--文件夹在当前项目磁盘根目录-->
       <property name="LOG_PATH" value="/modellog" />
       <!--设置系统日志目录-->
       <property name="APPDIR" value="logs" />
   
       <!--  日志记录器，日期滚动记录
               ERROR 级别
        -->
       <appender name="ERROR" class="ch.qos.logback.core.rolling.RollingFileAppender">
           <!-- 正在记录的日志文件的路径及文件名 -->
           <file>${LOG_PATH}/${APPDIR}/model_repatile.log</file>
           <!-- 日志记录器的滚动策略，按日期，按大小记录 -->
           <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
               <!-- 归档的日志文件的路径，例如今天是1992-11-06日志，当前写的日志文件路径为file节点指定，
               可以将此文件与file指定文件路径设置为不同路径，从而将当前日志文件或归档日志文件置不同的目录。
               而1992-11-06的日志文件在由fileNamePattern指定。%d{yyyy-MM-dd}指定日期格式，%i指定索引 -->
               <fileNamePattern>${LOG_PATH}/${APPDIR}/error/model_repatile-error-%d{yyyy-MM-dd}.%i.log</fileNamePattern>
               <!-- 除按日志记录之外，还配置了日志文件不能超过10MB，若超过10MB，日志文件会以索引0开始，
               命名日志文件，例如log-error-1992-11-06.0.log -->
               <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                   <maxFileSize>10MB</maxFileSize>
               </timeBasedFileNamingAndTriggeringPolicy>
           </rollingPolicy>
           <!-- 追加方式记录日志 -->
           <append>true</append>
           <!-- 日志文件的格式 -->
           <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
               <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} %-5level --- [%thread] %logger Line:%-3L - %msg%n</pattern>
               <charset>utf-8</charset>
           </encoder>
           <!-- 此日志文件记录error级别的 -->
           <filter class="ch.qos.logback.classic.filter.LevelFilter">
               <level>error</level>
               <onMatch>ACCEPT</onMatch>
               <onMismatch>DENY</onMismatch>
           </filter>
       </appender>
   
   
       <!-- 日志记录器，日期滚动记录
               WARN  级别
        -->
       <appender name="WARN" class="ch.qos.logback.core.rolling.RollingFileAppender">
           <!-- 正在记录的日志文件的路径及文件名 -->
           <file>${LOG_PATH}/${APPDIR}/model_repatile_warn.log</file>
           <!-- 日志记录器的滚动策略，按日期，按大小记录 -->
           <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
               <!-- 归档的日志文件的路径，例如今天1992-11-06日志，当前写的日志文件路径为file节点指定，
               可以将此文件与file指定文件路径设置为不同路径，从而将当前日志文件或归档日志文件置不同的目录。
               而1992-11-06的日志文件在由fileNamePattern指定。%d{yyyy-MM-dd}指定日期格式，%i指定索引 -->
               <fileNamePattern>${LOG_PATH}/${APPDIR}/warn/model_repatile-warn-%d{yyyy-MM-dd}.%i.log</fileNamePattern>
               <!-- 除按日志记录之外，还配置了日志文件不能超过10MB，若超过10MB，日志文件会以索引0开始，
               命名日志文件，例如log-warn-1992-11-06.0.log -->
               <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                   <maxFileSize>10MB</maxFileSize>
               </timeBasedFileNamingAndTriggeringPolicy>
           </rollingPolicy>
           <!-- 追加方式记录日志 -->
           <append>true</append>
           <!-- 日志文件的格式 -->
           <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
               <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} %-5level --- [%thread] %logger Line:%-3L - %msg%n</pattern>
               <charset>utf-8</charset>
           </encoder>
           <!-- 此日志文件只记录warn级别的 -->
           <filter class="ch.qos.logback.classic.filter.LevelFilter">
               <level>warn</level>
               <onMatch>ACCEPT</onMatch>
               <onMismatch>DENY</onMismatch>
           </filter>
       </appender>
   
   
       <!-- 日志记录器，日期滚动记录
               INFO  级别
       -->
       <appender name="INFO" class="ch.qos.logback.core.rolling.RollingFileAppender">
           <!-- 正在记录的日志文件的路径及文件名 -->
           <file>${LOG_PATH}/${APPDIR}/model_repatile_ginfo.log</file>
           <!-- 日志记录器的滚动策略，按日期，按大小记录 -->
           <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
               <!-- 归档的日志文件的路径，例如今天是1992-11-06日志，当前写的日志文件路径为file节点指定，
               可以将此文件与file指定文件路径设置为不同路径，从而将当前日志文件或归档日志文件置不同的目录。
               而1992-11-06的日志文件在由fileNamePattern指定。%d{yyyy-MM-dd}指定日期格式，%i指定索引 -->
               <fileNamePattern>${LOG_PATH}/${APPDIR}/info/model_repatile-info-%d{yyyy-MM-dd}.%i.log</fileNamePattern>
               <!-- 除按日志记录之外，还配置了日志文件不能超过10MB，若超过10MB，日志文件会以索引0开始，
               命名日志文件，例如log-info-1992-11-06.0.log -->
               <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                   <maxFileSize>10MB</maxFileSize>
               </timeBasedFileNamingAndTriggeringPolicy>
           </rollingPolicy>
           <!-- 追加方式记录日志 -->
           <append>true</append>
           <!-- 日志文件的格式 -->
           <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
               <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} %-5level --- [%thread] %logger Line:%-3L - %msg%n</pattern>
               <charset>utf-8</charset>
           </encoder>
           <!-- 此日志文件只记录info级别的 -->
           <filter class="ch.qos.logback.classic.filter.LevelFilter">
               <level>info</level>
               <onMatch>ACCEPT</onMatch>
               <onMismatch>DENY</onMismatch>
           </filter>
       </appender>
   
   
       <!-- 日志记录器，日期滚动记录
               DEBUG  级别
       -->
       <appender name="DEBUG" class="ch.qos.logback.core.rolling.RollingFileAppender">
           <!-- 正在记录的日志文件的路径及文件名 -->
           <file>${LOG_PATH}/${APPDIR}/model_repatile_debug.log</file>
           <!-- 日志记录器的滚动策略，按日期，按大小记录 -->
           <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
               <!-- 归档的日志文件的路径，例如今天是1992-11-06日志，当前写的日志文件路径为file节点指定，
               可以将此文件与file指定文件路径设置为不同路径，从而将当前日志文件或归档日志文件置不同的目录。
               而1992-11-06的日志文件在由fileNamePattern指定。%d{yyyy-MM-dd}指定日期格式，%i指定索引 -->
               <fileNamePattern>${LOG_PATH}/${APPDIR}/debug/model_repatile-debug-%d{yyyy-MM-dd}.%i.log</fileNamePattern>
               <!-- 除按日志记录之外，还配置了日志文件不能超过10MB，若超过10MB，日志文件会以索引0开始，
               命名日志文件，例如log-debug-1992-11-06.0.log -->
               <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                   <maxFileSize>10MB</maxFileSize>
               </timeBasedFileNamingAndTriggeringPolicy>
           </rollingPolicy>
           <!-- 追加方式记录日志 -->
           <append>true</append>
           <!-- 日志文件的格式 -->
           <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
               <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} %-5level --- [%thread] %logger Line:%-3L - %msg%n</pattern>
               <charset>utf-8</charset>
           </encoder>
           <!-- 此日志文件只记录debug级别的 -->
           <filter class="ch.qos.logback.classic.filter.LevelFilter">
               <level>debug</level>
               <onMatch>ACCEPT</onMatch>
               <onMismatch>DENY</onMismatch>
           </filter>
       </appender>
   
       <!-- ConsoleAppender 控制台输出日志 -->
       <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
           <!--encoder 默认配置为PatternLayoutEncoder-->
           <encoder>
               <pattern>${CONSOLE_LOG_PATTERN}</pattern>
               <!--<pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} %-5level -&#45;&#45; [%thread] %logger Line:%-3L - %msg%n</pattern>-->
               <charset>utf-8</charset>
           </encoder>
           <!--此日志appender是为开发使用，只配置最底级别，控制台输出的日志级别是大于或等于此级别的日志信息-->
           <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
               <level>debug</level>
           </filter>
       </appender>
   
       <!-- FrameworkServlet日志-->
       <logger name="org.springframework" level="WARN" />
   
       <!-- mybatis日志打印-->
       <logger name="org.apache.ibatis" level="DEBUG" />
       <logger name="java.sql" level="DEBUG" />
   
       <!--  项目 mapper 路径
               console控制台显示sql语句：STDOUT.filter.level -> debug级别
       -->
       <logger name="com.limit.mapper" level="DEBUG"></logger>
   
       <!-- 生产环境下，将此级别配置为适合的级别，以免日志文件太多或影响程序性能 -->
       <root level="INFO">
           <appender-ref ref="ERROR" />
           <appender-ref ref="WARN" />
           <appender-ref ref="INFO" />
           <appender-ref ref="DEBUG" />
           <!-- 生产环境将请stdout去掉 -->
           <appender-ref ref="STDOUT" />
       </root>
   
   </configuration>
   ```

   

2. 修改application.yml配置⽂件，添加⽇志打印配置，控制台输出

   ```yaml
   logging:
     level:
       com.aaa.mapper.*: DEBUG
     config: classpath:logback-spring.xml
   #等同于application.properties的
   #logging.level.com.aaa.mapper.*=DEBUG
   #logging.config=classpath:logback-spring.xml
   ```

   

3. 测试，注意优先级别

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580622601.png)

   





# 3.lombok插件的使用

lombok介绍 Lombok是⼀个Java库，能⾃动插⼊编辑器并构建⼯具，简化Java开发。

通过添加注解的⽅式，不需要为类编写get、set或eques⽅法，同时可以⾃动化⽇志变量。简⽽⾔之：Lombok能以简单的注解形式来简化java代码，提⾼开发⼈员的开发效率 。

1. 引入pom.xml

   ```xml
   <!--lombok使用Slf4j日志注解-->
   <dependency>
       <groupId>org.projectlombok</groupId>
       <artifactId>lombok</artifactId>
       <version>1.18.4</version>
       <scope>provided</scope>
   </dependency>
   ```

   

2. 安装插件

   settings-plugins-搜索lombok，下载插件并重启idea

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580622858.png)

3. 解决编译时可能会出错

   编译时出错，可能是没有enable注解处理器。Annotation Processors > Enable annotation processing，设置完成之后程序正常运⾏。

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580622894.png)

4. 测试

   在实体类上可以添加@Data注解，直接默认生成get set方法，不能生成构造

   <font style="color:red">在service层或者controller层添加注解@Slf4j，可以⾃动化⽇志变量 ，这个比较常用。这个注解等同于private static final Logger log = LoggerFactory.getLogger(EmpController.class);</font>

   ***编译前***

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580622953.png)

   ***编译后***

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580622986.png)

5. 常用注解

   ```java
   @Data 注解在类上；提供类所有属性的 getting 和 setting 方法，此外还提供了equals、canEqual、hashCode、toString 方法
   @Setter ：注解在属性上；为属性提供 setting 方法
   @Setter ：注解在属性上；为属性提供 getting 方法
   @Log4j ：注解在类上；为类提供一个 属性名为log 的 log4j 日志对象
   @NoArgsConstructor ：注解在类上；为类提供一个无参的构造方法
   @AllArgsConstructor ：注解在类上；为类提供一个全参的构造方法
   @Cleanup : 可以关闭流
   @Builder ： 被注解的类加个构造者模式
   @Synchronized ： 加个同步锁
   @SneakyThrows : 等同于try/catch 捕获异常
   @NonNull : 如果给参数加个这个注解 参数为null会抛出空指针异常
   @Value : 注解和@Data类似，区别在于它会把所有成员变量默认定义为private final修饰，并且不会生成set方法。
   ```

6. lombok的优缺点

   **优点**： 能通过注解的形式⾃动⽣成构造器、getter/setter、equals、hashcode、toString等⽅法， 提⾼了⼀定的开发效率 让代码变得简洁，不⽤过多的去关注相应的⽅法 属性做修改时，也简化了维护为这些属性所⽣成的getter/setter⽅法等

   **缺点**： 不⽀持多种参数构造器的重载 虽然省去了⼿动创建getter/setter⽅法的麻烦，但⼤⼤降低了源代码的可读性和完整性，降 低了阅读源代码的舒适度 







# 4.Profiles实现多环境下配置切换

开发库：所有开发员⼯都连接开发库。 

测试库：测试⼈员⽤ 

⽣产库：正式上线的数据库



## 4.1 单文件配置

application.yml文件分为四部分,使用 --- 来作为分隔符，第一部分通用配置部分，表示三个环境都通用的属性， 后面三段分别为：开发，测试，生产，用spring.profiles指定了一个值(开发为dev，测试为test，生产为prod)，这个值表示该段配置应该用在哪个profile里面。

注意：如果spring.proﬁles.active没有指定值，那么只会使⽤没有指定spring.proﬁles⽂件的 值，也就是只会加载通⽤的配置

```yaml
spring:
  profiles:
    active: dev

---
#开发环境配置
spring:
  profiles: dev

server:
  port: 8080

---
#测试环境配置
spring:
  profiles: uat

server:
  port: 8081

---
#生产环境配置
spring:
  profiles: prod

server:
  port: 1002
```



## 4.2 配置在不同文件中

为了方便维护各种环境的配置，也可以把yml文件进行拆分在**src/main/resource**目录下，再创建yml配置文件，命名规则为application.yml，例如 application-dev.yml 、 application-test.yml 、 application-prod.yml，根据名字⾃动寻找相关的application-name.yml的⽂件

```yaml
//application.yml
spring:
  profiles:
    active: dev

//application-dev.yml
server:
  port: 8080
  
//application-test.yml
 server:
  port: 8081

//application-prod.yml
server:
  port: 8082
```



也可以使用application.properties进行配置，appliaciton.properties:留通⽤配置

```
//application.properties主配置文件
spring.profiles.active=prod
#扫描mapper.xml映射
mybatis.mapper-locations=classpath:mapper/*.xml

logging.level.com.aaa.mapper.*=DEBUG
logging.config=classpath:logback-spring.xml


//application-dev.properties
server.port= 8080
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.username=root
spring.datasource.password=8248
spring.datasource.url=jdbc:mysql://localhost:3306/yybsshop?useUnicode=true&characterEncoding=UTF-8&allowMultiQueries=true&serverTimezone=CTT&zeroDateTimeBehavior=CONVERT_TO_NULL
mybatis.mapper-locations=classpath:mapper/*.xml


//application-prod.properties
server.port= 8081
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.username=root
spring.datasource.password=8248
spring.datasource.url=jdbc:mysql://localhost:3306/yybsshop?useUnicode=true&characterEncoding=UTF-8&allowMultiQueries=true&serverTimezone=CTT&zeroDateTimeBehavior=CONVERT_TO_NULL
mybatis.mapper-locations=classpath:mapper/*.xml



//application-test.properties
server.port= 8082
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.username=root
spring.datasource.password=8248
spring.datasource.url=jdbc:mysql://localhost:3306/yybsshop?useUnicode=true&characterEncoding=UTF-8&allowMultiQueries=true&serverTimezone=CTT&zeroDateTimeBehavior=CONVERT_TO_NULL
mybatis.mapper-locations=classpath:mapper/*.xml
```





## 4.3 @Proﬁle注解

任何组件、配置类有@Component或@Conﬁguration注解都能被@Proﬁle标记，从⽽限制加 载它的时机。即：在哪种环境下才运⾏这个配置类。例如：我们创建配置类TestConﬁg，指定他所加载的时机，只进⾏打印测试，看看何时加载：

```java
@Configuration
@Profile("test")
public class TestConfig {
    @Bean(name="test")
    public void test() {
        System.out.println("配置加载！");
    }
}
```

分别切换不同的环境进⾏运⾏测试，会发现只有test环境⽅可加载 







# 5.SpringBoot的单元测试

SpringBoot 项⽬单元测试也很⽅便, Web项⽬中单元测试应该覆盖: 

1. Service 层 

2. Controller 层 

默认：springBootTest可以涵盖service

```java
@SpringBootTest
class Boot02TestApplicationTests {
    //默认Service
    @Autowired
    private DeptService deptService;

    @Test
    void contextLoads() {
        PageEntity pageEntity = new PageEntity();
        pageEntity.setPageNum(1);
        pageEntity.setPageSize(2);
        PageInfo<department> pageInfo = deptService.selectAllPage(pageEntity);
        System.out.println(pageInfo);
    }

}
```

@SpringBootTest⽤来指定SpringBoot应⽤程序的⼊⼝类，该注解默认会根据包名逐级往上找，⼀直找到⼀个SpringBoot主程序class为⽌， 然后启动该类为单元测试准备Spring上下⽂环境

```java
@SpringBootTest
//配置mock：测试控制层
@AutoConfigureMockMvc
class Spring01ApplicationTests {
    //默认Service
    @Autowired
    private UserService userService;

    @Test
    void contextLoads() {
        PageEntity pageEntity=new PageEntity();
        pageEntity.setPageNum(1);
        pageEntity.setPageSize(2);
        PageInfo page=userService.selectAll(pageEntity);
        System.out.println(page);
    }
    @Autowired
    private MockMvc mvc;
    @Test
    public void selectAll2() throws Exception {
        String json="{pageNum:1,pageSize:2}";
        String responseString = mvc.perform(
                get("/user/selectAll")    //请求的url,请求的方法是get
                        .contentType(MediaType.APPLICATION_JSON)  //数据的格式
                        .content(json)//参数
        ).andExpect(status().isOk())    //返回的状态是200
                .andDo(print())         //打印出请求和相应的内容
                .andReturn().getResponse().getContentAsString();   //将相应的数据转换为字符串
        System.out.println("--------返回的json = " + responseString);
    }

}
```





# 6.HttpClient

HttpClient可以跨工程做调用

1. 引入pom依赖

   ```xml
   <!--接口调用 -->
   <dependency>
       <groupId>org.apache.httpcomponents</groupId>
       <artifactId>httpcore</artifactId>
       <version>4.4</version>
   </dependency>
   <dependency>
       <groupId>org.apache.httpcomponents</groupId>
       <artifactId>httpmime</artifactId>
       <version>4.3.6</version>
   </dependency>
   <dependency>
       <groupId>org.apache.httpcomponents</groupId>
       <artifactId>httpclient</artifactId>
       <version>4.5.3</version>
   </dependency>
   ```

   

2. 调用

   ```java
   @Test
   public void selectEcgByTaskCode(){
       try{
           String url = "http://localhost:8082/user/selectAll";
           PageEntity pageEntity=new PageEntity();
           pageEntity.setPageNum(1);
           pageEntity.setPageSize(2);
           getInfo(url,pageEntity);
       }catch (Exception e){
           e.printStackTrace();
       }
   }
   
   public void getInfo(String url, PageEntity params){
       CloseableHttpClient httpClient = null;
       HttpGet tttpGet = null;
       try {
           httpClient = HttpClients.createDefault();
           RequestConfig requestConfig = RequestConfig.custom().setSocketTimeout(20000).setConnectTimeout(20000).build();
           tttpGet = new HttpGet(url);
           tttpGet.setConfig(requestConfig);
           tttpGet.setHeader("Content-Type", "application/json");
           CloseableHttpResponse response = httpClient.execute(tttpGet);
           HttpEntity httpEntity = response.getEntity();
           System.out.println(EntityUtils.toString(httpEntity,"utf-8"));
       } catch (ClientProtocolException e) {
           e.printStackTrace();
       } catch (IOException e) {
           e.printStackTrace();
       }finally{
           try {
               if(tttpGet!=null){
                   tttpGet.releaseConnection();
               }
               if(httpClient!=null){
                   httpClient.close();
               }
           } catch (IOException e) {
               e.printStackTrace();
           }
       }
   }
   ```

   





# 7.热部署插件使用

热部署就是当应用正在运行时，升级软件就不需要重启应用。就比如我们修改了代码的某一部分，不需要再次启动程序，等启动完毕后再到浏览器刷新。有了热部署，我们只需启动一次程序，当有了修改后，只需刷新就好。

## 7.1 引入依赖，开启热部署

```xml
<!--让springboot工程热加载-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <scope>runtime</scope>
    <optional>true</optional>
</dependency>
```

```xml
<!--maven插件-->
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <configuration>
        <fork>true</fork><!--一定要开启热部署，不然不生效-->
    </configuration>
</plugin>
```





## 7.2 修改项目配置文件

```yaml
devtools:
  restart:
    enabled: true
```

![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580623956.png)





## 7.3 设置idea自动编译

如果使用Thymeleaf模板引擎，需要把模板默认缓存设置为false，\#禁止thymeleaf缓存（建议：开发环境设置为false，生成环境设置为true）

spring.thymeleaf.cache=false

![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580624002.png)

打开设置 --> 查找Registry --> 找到并勾选compiler.automake.allow.when.app.running，最后重启idea

如果idea版本比较新，可能找不到这个，尝试下边方法：

打开设置 --> KeyMap-->查找Registry ，随便补充一个不重复的快捷键，后面要使用一次这个快捷键 比如 Alt+Z

使用快捷键Alt+Z，找到并勾选compiler.automake.allow.when.app.running

![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580624063.png)

测试：

修改html页面会自动加载

修改java代码会自动重启项目







# 8.springboot整合thymeleaf模板

## 8.1 thymeleaf简介：

thymeleaf是一种Java模板引擎，那何为模板引擎呢？

模板引擎就是为了使用户页面和业务数据相互分离而出现的，将从后台返回的数据生成特定的格式的文档，这里说的特定格式一般都指HTML文档。它能够处理html、xml、js、css甚至纯文本，类似于freemarker。

它的优点是语法优雅易懂、原型即页面、遵从web标准。原型即页面是它的特色，所谓原型即页面，就是你写的html，静态的去访问是什么样，动态的去访问还是这样，只不过动态的时候会把数据填充进去。

前端用jsp当界面渲染，不能做到现在所追求的前后端分离，所以很多人开始使用模板引擎，thymeleaf最大的优势后缀为html，就是只需要浏览器就可以展现页面了，还有就是thymeleaf可以很好的和spring集成。



## 8.2 thymeleaf标准方言：

1. 变量表达式：${...}

   例如前端接收一个user,想取出user的name属性，就可以用变量表达式：

   ``<span th:text="${user.name}">``

2. 消息表达式：#{...}

   也称为文本外部化、国际化或i18n.

   ``<p th:text=" #{header.address.city}" >...</p>``

3. 选择表达式：*{...}

   与变量表达式的区别：选择表达式是在当前选择的对象上执行而不是整个上下文。

   ```html
   <form action="/users" th:action="@{/users}" method="POST" th:object="${userModel.user}">  <input type="hidden" name="id" th:value="*{id}"></form>
   ```

   这里id就用了选择表达式，在此处*{id}与${userModel.user.id}效果一样。

4. 链接表达式：@{...}

   url可以是相对的，也可以是绝对的。

   ```html
   <a th:href="@{.../users/list}">...</a><a th:href="@{http://www.baidu.com}">...</a>
   ```

   

5. 分段表达式：th:insert 、th:replace 、th:include，就相当插入，这三个的区别：

   - `th:insert`  ：保留自己的主标签，保留th:fragment的主标签。
   - `th:replace` ：不要自己的主标签，保留th:fragment的主标签。
   - `th:include` ：保留自己的主标签，不要th:fragment的主标签。（官方3.0后不推荐）

   ```html
   需要替换的片段内容：
   <footer th:fragment="copy">
      <script type="text/javascript" th:src="@{/plugins/jquery/jquery-3.0.2.js}"></script>
   </footer>
    
   导入片段：
    
     <div th:insert="footer :: copy"></div>
    
     <div th:replace="footer :: copy"></div>
    
     <div th:include="footer :: copy"></div>
     
    
   结果为：
    
   <div>
       <footer>
          <script type="text/javascript" th:src="@{/plugins/jquery/jquery-3.0.2.js}"></script>
       </footer>  
   </div>  
    
   <footer>
     <script type="text/javascript" th:src="@{/plugins/jquery/jquery-3.0.2.js}"></script>
   </footer>  
    
   <div>
     <script type="text/javascript" th:src="@{/plugins/jquery/jquery-3.0.2.js}"></script>
   </div>  
   ```

   

6. 字面量：

   字面量可以是文本、数字、布尔和null等类型。

7. 算术操作：+、-、*、/、%

   例如：

   ```html
   <div th:with="isEven=(${user.age} % 2 == 0)">
   ```

   

8. 其他运算符

   比较： >、<、>=、<=  (gt、lt、ge、le)

   等价： ==、!= (eq、ne)

   三目运算符：

   ```html
   <tr th:class="${row.even} ? 'even' : 'odd' "></tr>
   ```

   

9. 迭代器：th:each

   相当于Java的foreach.

   ```html
   <tr th:each="user : ${userList}">
   
       <td th:text="${user.id}"></td>
   
       <td th:text="${user.email}"></td>
   </tr>
   ```

   这样就是遍历userList集合。

   迭代器的状态变量有：

   index、count、size、current、even/odd、first、last

10. 条件语句：th:if、th:unless、switch

    ```html
    <div th:switch="${user.role}">
       <p th:case=" 'admin' ">User is admin</p>
       <p th:case=" 'guest' ">User is guest</p>
    </div>
    ```

    

11. 模板布局：th:fragment

    比如定义一个公用的页头：

    ```html
    <div th:fragment="header">
       <h1>Thymeleaf in action</h1>
       <a href="/users" >首页</a>
    </div>
    ```

    在其他页面直接这样引用就行：

    ```html
    <div th:replace="~{fragments/header :: header}"></div>
    ```

    

12. 表达式基本对象

    表达式基本对象有：param、session、application、request、servletContext。





## 8.3 springboot整合thymeleaf

1. 添加依赖：

   ```xml
   <!-- 添加thymeleaf模板引擎支持 -->
   <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
   </dependency>
   ```

   

2. 修改项目配置文件 添加如下配置：

   ```yaml
   thymeleaf:
     check-template-location: true #是否检查模板路径是否存在，默认true
     check-template: true #是否检查模板是否存在，默认true
     enabled: true #是否允许MVC使用Thymeleaf，默认为: true
     prefix: classpath:/templates/ #指定模板的前缀，默认为:classpath:/templates/
     suffix: .html #指定模板的后缀，默认为:.html
     cache: false #是否开启模板缓存，默认true
   ```

   备注更多配置介绍：

   ```xml
   spring.thymeleaf.cache
   是否开启模板缓存，默认true
   
   spring.thymeleaf.check-template-location
   是否检查模板路径是否存在，默认true
   
   spring.thymeleaf.check-template
   Check that the template exists before rendering it.
   
   spring.thymeleaf.content-type
   指定Content-Type，默认为: text/html
   
   spring.thymeleaf.enabled
   是否允许MVC使用Thymeleaf，默认为: true
   
   spring.thymeleaf.encoding
   指定模板的编码，默认为: UTF-8
   
   spring.thymeleaf.excluded-view-names
   指定不使用模板的视图名称，多个以逗号分隔.
   
   spring.thymeleaf.mode
   指定模板的模式，具体查看StandardTemplateModeHandlers，默认为: HTML5
   
   spring.thymeleaf.prefix
   指定模板的前缀，默认为:classpath:/templates/
   
   spring.thymeleaf.suffix
   指定模板的后缀，默认为:.html
   
   spring.thymeleaf.template-resolver-order
   指定模板的解析顺序，默认为第一个.
   
   spring.thymeleaf.view-names
   指定使用模板的视图名，多个以逗号分隔.
   ```

   

3. 编写测试代码

   ```java
   //controller
   /**
    * 测试thymeleaf
    * @return
    */
   @RequestMapping(value = "/helloThymeleaf" )
   public String helloThymeleaf(HttpServletRequest request,ModelMap model){
       request.setAttribute("name","thymeleaf");
       request.setAttribute("age","20");
       model.addAttribute("remark","good");
       List<Course> courses = empMapper.selectAll();
       //测试条件判断
       //courses = new ArrayList<Course>();
       request.setAttribute("courses",courses);
       return "helloThymeleaf";
   }
   ```

   

4. helloThymeleaf.html

   **引入头文件：**

   xmlns:th="http://www.thymeleaf.org"

   **遍历取值：**

   th:if="${not#lists.isEmpty(courses)}"

   th:text="${course.courseId}"

   **条件判断：**

   th:if="${not#lists.isEmpty(allPerson)}"

   ```html
   <!DOCTYPE html>
   <html lang="en" xmlns:th="http://www.thymeleaf.org">
   <head>
       <meta charset="UTF-8">
       <title>thymeleaf</title>
   </head>
   
   <body>
       this is a html!<br/>
       姓名：<span th:text="${name}"></span><br/>
       年龄：<span th:text="${age}"></span><br/>
       备注：<span th:text="${remark}"></span><br/>
       <table border="1px" th:if="${not#lists.isEmpty(courses)}">
           <tr>
               <td>课程id：</td>
               <td>课程编号：</td>
               <td>课程名称：</td>
               <td>课程备注：</td>
           </tr>
           <tr th:each="course:${courses}">
               <td th:text="${course.courseId}"></td>
               <td th:text="${course.courseNum}"></td>
               <td th:text="${course.courseName}"></td>
               <td th:text="${course.courseRemark}"></td>
           </tr>
       </table>
   </body>
   </html>
   ```

   

5. 访问测试结果：

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/20200202143717.png)







# 9.ElementUI整合pageHelper分页

```javascript
//html中分页代码
<el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="currentPage"
        :page-sizes="[2, 4, 6, 8]"
        :page-size="pageSize"
        layout="total, sizes, prev, pager, next, jumper"
        :total="totalCount">
</el-pagination>

//js代码
$.ajax({
    url:"/emp/selectAllEmpPage",
    type:"get",
    data:{
        pageNum:1,
        pageSize:2
    },
    dataType:"json",
    success:function(back){
        app.emps = back.data.list;
        app.totalCount = back.data.total;
    }
})

var app = new Vue({
    el:"#app",
    data:{
        emps:[],
        totalCount:0,
        currentPage:1, //初始页
        pageSize:2,    //默认每页的数据
        formLabelWidth: '80px'
    },
    methods:{
        getData:function(){
            $.ajax({
                url:"/emp/selectAllEmpPage",
                type:"get",
                data:{
                    pageNum:this.currentPage,
                    pageSize:this.pageSize
                },
                dataType:"json",
                success:function(back){
                    app.emps = back.data.list;
                }
            })
        },
        handleSizeChange:function(val) {
            //改变每页显示数量重新请求数据，重置当前页为第一页
            this.pageSize = val;
            this.currentPage = 1;
            this.getData()
        },
        handleCurrentChange:function(val) {
            //点击改变当前页
            this.currentPage = val;
            this.getData()
        },
    }
})
```



java后台代码，响应回参带有分页信息

```java
@Slf4j
@RestController
@RequestMapping("/emp")
public class EmpController {
    @Autowired
    private EmpService empService;
    
    @GetMapping("/selectAllEmpPage")
    public ResultData selectAllEmpPage(PageEntity pageEntity){
        PageInfo<Emp> empPageInfo = empService.selectAllEmpPage(pageEntity);
        if(empPageInfo != null){
            log.info("查询员工信息分页成功");
            return new ResultData(true,"查询员工信息成功",empPageInfo);
        }else{
            log.error("查询员工信息分页失败");
            return new ResultData(false,"查询员工信息失败",null);
        }
    }
}
```



