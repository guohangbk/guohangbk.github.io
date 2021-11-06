# 1.缓存

MyBatis 内置了一个强大的事务性查询缓存机制，它可以非常方便地配置和定制。 为了使它更加强大而且易于配置，我们对 MyBatis 3 中的缓存实现进行了许多改进。

默认情况下，只启用了本地的会话缓存，它仅仅对一个会话中的数据进行缓存。 要启用全局的二级缓存，只需要在你的 SQL 映射文件中添加一行：

```xml
<cache/>
```



## 1.1 一级缓存

⼀级缓存：有效返回为SqlSession⼀次打开关闭。内容的保存期限。

mybaits+Spring做整合，整合以后，spring调⽤⼀次mybaits就打开关闭⼀次sqlsession，⼀ 个sql就需要打开⼀次关闭⼀次。

执⾏查询的时候：先判断缓存中有没有，有就读取没有就查询放到缓存中

执⾏增删改：清除缓存

关闭session：清除缓存

sqlSession.clearCache()：清除缓存 



**案例**

```java
//OrderMapperxml
<!--一级缓存-->
<select id="selectOrderEasy" resultType="HashMap">
  select * from tab_order
</select>
<insert id="insertOrders" parameterType="HashMap">
  insert into tab_order(orderid)values(sys_guid())
</insert>

//OrderMapper.java
List selectOrderEasy();
int insertOrders();

//test.java
public void selectOrderEasy(){
    SqlSession sqlSession = bd.getSession();
    OrderMapper orderMapper = sqlSession.getMapper(OrderMapper.class);
    //每一次执行查询：首先先判断缓存中是否有（sql语句：保证同一个文件，sql语句完全一致。）
    //如果有就读取
    //如果没有就执行sql，然后放到缓存中。
    orderMapper.selectOrderEasy();
    System.out.println("----------------第二次----------------");
    List list=orderMapper.selectOrderEasy();
    System.out.println(list);
    //执行增删改的时候会将本文件设计的所有缓存清空。
    orderMapper.insertOrders();
    System.out.println("-----第三次");
    list=orderMapper.selectOrderEasy();
    System.out.println(list);
    System.out.println("-----第四次");
    list=orderMapper.selectOrderEasy();
    System.out.println(list);
    sqlSession.clearCache();
    System.out.println("-----第五次");
    list=orderMapper.selectOrderEasy();
    System.out.println(list);
    sqlSession.close();
    sqlSession=bd.getSession();
    System.out.println("-----第六次");
    orderMapper=sqlSession.getMapper(OrderMapper.class);
    list=orderMapper.selectOrderEasy();
    System.out.println(list);
}
```





## 1.2 二级缓存

要启⽤全局的⼆级缓存，只需要在你的 SQL 映射⽂件中添加⼀⾏：

```xml
<cache/> 
```

⼆级缓存：sqlSessionFactory，只要sqlsessionfactory没有重新创建就缓存⼀致⽣效：创建 ⼯程创建，所以基本上⼀个⼯程只要不关闭就有效。

```xml
<mapper namespace="com.mapper.TabOrderMapper">
    <cache/>
```

注意：

1. 映射语句⽂件中的所有 select 语句的结果将会被缓存。
2. 映射语句⽂件中的所有 insert、update 和 delete 语句会刷新缓存。
3. 缓存会使⽤最近最少使⽤算法（LRU, Least Recently Used）算法来清除不需要的缓存。
4. 缓存不会定时进⾏刷新（也就是说，没有刷新间隔）。
5. 缓存会被视为读/写缓存，这意味着获取到的对象并不是共享的，可以安全地被调⽤者修 改，⽽不⼲扰其他调⽤者或线程所做的潜在修改。

```xml
完整写法： 
<cache eviction="FIFO" ﬂushInterval="60000" size="512" readOnly="true"/>
```

这个更⾼级的配置创建了⼀个 FIFO 缓存，每隔 60 秒刷新，最多可以存储结果对象 或列表的 512个引⽤，⽽且返回的对象被认为是只读的，可⽤的清除策略有：

- - LRU – 最近最少使⽤：移除最⻓时间不被使⽤的对象。           
  - FIFO – 先进先出：按对象进⼊缓存的顺序来移除它们。           
  - SOFT – 软引⽤：基于垃圾回收器状态和软引⽤规则移除对象。           
  - WEAK – 弱引⽤：更积极地基于垃圾收集器状态和弱引⽤规则移除对象。      



# 2.逆向工程

根据数据库，⾃动⽣成xml文件以及接⼝还有实体。

1. 将配置代码放到maven插件中，**需要自己配置插件，就需要把标签删除**

   ```xml
   <!--逆向工程配置-->
   <plugin>
     <groupId>org.mybatis.generator</groupId>
     <artifactId>mybatis-generator-maven-plugin</artifactId>
     <version>1.3.5</version>
     <configuration>
       <!-- 是否覆盖 -->
       <overwrite>true</overwrite>
       <!--允许移动生成的文件 -->
       <verbose>true</verbose>
       <!-- 自动生成的配置,${basedir}表示项目根目录 ,configurationFile默认在resource目录下-->
       <!--<configurationFile>${basedir}/src/main/resources/generatorConfig.xml</configurationFile>-->
     </configuration>
     <dependencies>
       <dependency>
         <groupId>com.oracle</groupId>
         <artifactId>ojdbc7</artifactId>
         <version>7.0</version>
       </dependency>
     </dependencies>
   </plugin>
   ```

   

2. 将逆向工程的配置文件generatorConﬁg.xml放在resources根目录下

   ```xml
   <?xml version='1.0' encoding='UTF-8'?>
   <!DOCTYPE generatorConfiguration
           PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
           "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
   
   <generatorConfiguration>
       <classPathEntry location="E:\Development\Maven\mavenDownload\com\oracle\ojdbc7\7.0\ojdbc7-7.0.jar"/>
       <context id="mybatisGenerator" targetRuntime="MyBatis3Simple">
   
           <commentGenerator>
               <!-- 是否去除自动生成的注释 true：是 ： false:否 -->
               <property name="suppressAllComments" value="true" />
           </commentGenerator>
           <!--数据库连接的信息：驱动类、连接地址、用户名、密码 -->
           <jdbcConnection driverClass="oracle.jdbc.driver.OracleDriver"
                           connectionURL="jdbc:oracle:thin:@localhost:1521:orcl"
                           userId="scott"
                           password="root">
           </jdbcConnection>
   
           <!-- 默认false，把JDBC DECIMAL 和 NUMERIC 类型解析为 Integer，为 true时把JDBC DECIMAL 和
               NUMERIC 类型解析为java.math.BigDecimal -->
           <javaTypeResolver>
               <property name="forceBigDecimals" value="false" />
           </javaTypeResolver>
   
           <!-- targetProject:生成PO类的位置 -->
           <javaModelGenerator targetPackage="com.entity"
                               targetProject=".\src\main\java">
               <!-- enableSubPackages:是否让schema作为包的后缀 -->
               <property name="enableSubPackages" value="false" />
               <!-- 从数据库返回的值被清理前后的空格 -->
               <property name="trimStrings" value="true" />
           </javaModelGenerator>
           <!-- targetProject:mapper映射文件生成的位置 -->
           <sqlMapGenerator targetPackage="com.mapper"
                            targetProject=".\src\main\resources">
               <!-- enableSubPackages:是否让schema作为包的后缀 -->
               <property name="enableSubPackages" value="false" />
           </sqlMapGenerator>
           <!-- targetPackage：mapper接口生成的位置 -->
           <javaClientGenerator type="XMLMAPPER"
                                targetPackage="com.mapper"
                                targetProject=".\src\main\java">
               <!-- enableSubPackages:是否让schema作为包的后缀 -->
               <property name="enableSubPackages" value="false" />
           </javaClientGenerator>
           <!-- 指定数据库表 -->
           <table tableName="tab_product" ></table>
           <table tableName="tab_order" ></table>
           <table tableName="tab_orderdeatil" ></table>
       </context>
   </generatorConfiguration>
   ```

   

3. 设置generatorConfig.xml配置文件中的数据库配置和需要逆向生成数据库的表

   ```xml
   <!-- 指定数据库表 -->
   <table tableName="tab_product" ></table>
   <table tableName="tab_order" ></table>
   <table tableName="tab_orderdeatil" ></table>
   ```

   

4. 测试

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580475337.png)





# 3.MyBatis插件

## 3.1 插件--MyBatis拦截器

MyBatis 允许你在已映射语句执⾏过程中的某⼀点进⾏拦截调⽤。

Mybatis采⽤责任链模式，通过动态代理组织多个拦截器（插件），通过这些拦截器可以改变 Mybatis的默认⾏为（诸如SQL重写之类的），由于插件会深⼊到Mybatis的核⼼，因此在编 写⾃⼰的插件前好了解下它的原理，以便写出安全⾼效的插件。

默认情况下，MyBatis 允许使⽤插件来拦截的⽅法调⽤包括：

- 1. Executor(update, query, ﬂushStatements, commit, rollback,getTransaction, close, isClosed)

  2. ParameterHandler(getParameterObject, setParameters)

  3. ResultSetHandler(handleResultSets, handleOutputParameters)

  4. StatementHandler(prepare, parameterize, batch, update, query)

     

**案例：自定义插件进行查询分页**

```java
@Intercepts({
        @Signature(type= StatementHandler.class,method = "prepare",args = {
                Connection.class,Integer.class
        })
})
public class PageHelpers implements Interceptor {
    //核心方法  intercept  相关逻辑都在这里边写
    @Override
    public Object intercept(Invocation invocation) throws Throwable {
        //获取拦截的目标
        StatementHandler  statementHandler = (StatementHandler) invocation.getTarget();
        MetaObject metaObject = SystemMetaObject.forObject(statementHandler);
        String sql = metaObject.getValue("delegate.boundSql.sql").toString();
        String newSql = "select * from (select rownum as rn,a.* from(";
        newSql=newSql+sql;
        newSql=newSql+")a)where rn<=2";
        metaObject.setValue("delegate.boundSql.sql",newSql);
        Object obj = invocation.proceed();//责任链继续。 调用目标方法
        return obj;
    }

    /**
     * 生成代理类：插件原理基本就是一个代理原理（AOP_pageHelper)
     * @param target
     * @return
     */
    @Override
    public Object plugin(Object target) {
        return Plugin.wrap(target,this);
    }
    /**
     * 获取插件的参数的
     * @param properties
     */
    @Override
    public void setProperties(Properties properties) {

    }
}
```

主配置文件

```xml
<plugins>
    <plugin interceptor="com.plugins.PageHelper"></plugin>
</plugins>
```





## 3.2 分页插件

1. 引入插件pom

   ```xml
   <!--pageHelper的jar-->
   <dependency>
     <groupId>com.github.pagehelper</groupId>
     <artifactId>pagehelper</artifactId>
     <version>5.1.4</version>
   </dependency>
   <dependency>
     <groupId>com.github.jsqlparser</groupId>
     <artifactId>jsqlparser</artifactId>
     <version>0.9.1</version>
   </dependency>
   ```

   

2. MyBatis.xml配置插件

   ```xml
   <plugins>
       <plugin interceptor="com.github.pagehelper.PageInterceptor">
       </plugin>
   </plugins>
   ```

   

3. 使用插件

   ```java
   public void pageHelper(){
       SqlSession session = bd.getSession();
       EmpMapper mapper = session.getMapper(EmpMapper.class);
       PageHelper.startPage(1,5);
       List<HashMap> list = mapper.selectAll();
       PageInfo<HashMap> pageInfo = new PageInfo<>(list);
       System.out.println(pageInfo.getPages());//总共多少也
       System.out.println(pageInfo.getTotal());//总共条
       System.out.println(pageInfo.isIsLastPage());//是否最后一页
       System.out.println(pageInfo.isHasNextPage());//是否有下一页
       for (HashMap map : list) {
           System.out.println(map);
       }
   }
   ```





**注意**

junit是用例自动化单元测试的，控制台输入这个人工操作不支持，如果需要支持，需要修改IDEA配置文件

-Deditable.java.test.console=true

![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580475672.png)

![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580475687.png)