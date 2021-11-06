# 1.Mybatis简介

MyBatis 是一款优秀的持久层框架，它支持定制化 SQL、存储过程以及高级映射。MyBatis 避免了几乎所有的 JDBC 代码和手动设置参数以及获取结果集。MyBatis 可以使用简单的 XML 或注解来配置和映射原生类型、接口和 Java 的 POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。



ORM：对象关系映射

+ O对象——java对象   
+ R关系——数据库表和表间关系 
+ M映射——java和数据库表的映射关系 

对象-关系映射（Object-Relational Mapping，简称ORM），对象和关系数据是业务实体的两种表现形式，业务实体在内存中表现为对象，在数据库中表现为关系数据。内存中的对象之间存在关联和继承关系，⽽在数据库中，关系数据⽆法 包含：核⼼配置⽂件——数据源，加载映射⽂件，setting配置，插件等等..... 启动⼯程必须加载主配置⽂件，并且⼀般⼀张表都有⼀个映射⽂件。





#	2.MyBatis的优缺点

**优点：**

- 简单易学：本身就很小且简单。没有任何第三方依赖，最简单安装只要两个jar文件+配置几个sql映射文件易于学习，易于使用，通过文档和源代码，可以比较完全的掌握它的设计思路和实现。 
- 灵活：mybatis不会对应用程序或者数据库的现有设计强加任何影响。 sql写在xml里，便于统一管理和优化。通过sql基本上可以实现我们不使用数据访问框架可以实现的所有功能，或许更多。 
- 解除sql与程序代码的耦合：通过提供DAL层，将业务逻辑和数据访问逻辑分离，使系统的设计更清晰，更易维护，更易单元测试。sql和代码的分离，提高了可维护性。 
- 提供映射标签，支持对象与数据库的orm字段关系映射 
- 提供对象关系映射标签，支持对象关系组建维护 
- 提供xml标签，支持编写动态sql。 

**缺点：**

- 编写SQL语句时工作量很大，尤其是字段多、关联表多时，更是如此。 
- SQL语句依赖于数据库，导致数据库移植性差，不能更换数据库。 
- 框架还是比较简陋，功能尚有缺失，虽然简化了数据绑定代码，但是整个底层数据库查询实际还是要自己写的，工作量也比较大，而且不太容易适应快速数据库修改。 
- 二级缓存机制不佳 





# 3.常用类

启动时⽤的⼏个类：

SqlSessionFactoryBuilder 加载核⼼配置⽂件，然后创建⼯⼚类：依赖流（配置⽂件：数据源）

SqlSessionFactory：⼯⼚类提供会话。对应的是数据源，⼀个项⽬⼀个数据源，所以⼀个项⽬⼀般情况下就⼀个SqlSessionFactory

SqlSession：⼀次会话（⼀次连接）需要创建⼀个，连接⼀次可以执⾏多个sql。如果有事务，需要提交；执⾏完毕之后，关闭session

**MyBatis的主要成员**

- 1. Conﬁguration     MyBatis所有的配置信息都保存在Conﬁguration对象之中，配置⽂件中的⼤部分配 置都会存储到该类中
  2. SqlSession       作为MyBatis⼯作的主要顶层API，表示和数据库交互时的会话，完成必要数据库增删 改查功能
  3. Executor        MyBatis执⾏器，是MyBatis 调度的核⼼，负责SQL语句的⽣成和查询缓存的维护 
  4. StatementHandler 封装了JDBC Statement操作，负责对JDBC statement 的操作，如设置参数等 
  5. ParameterHandler  负责对⽤户传递的参数转换成JDBC Statement 所对应的数据类型 
  6. ResultSetHandler  负责将JDBC返回的ResultSet结果集对象转换成List类型的集合
  7. TypeHandler      负责java数据类型和jdbc数据类型(也可以说是数据表列类型)之间的映射和转换 
  8. MappedStatement  MappedStatement维护⼀条<select|update|delete|insert>节点的封装 
  9. SqlSource        负责根据⽤户传递的parameterObject，动态地⽣成SQL语句，将信息封装到 BoundSql对象中，并返回
  10. BoundSql        表示动态⽣成的SQL语句以及相应的参数信息

![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580474307.png)





#	4.MyBatis使用过程

1. 创建Maven项目以后，在main中创建两个文件夹，一个是java，一个resources

2. 创建完以后，右击选择Mark Directory as ，java选择的是Sources Root，resources选择的是Resources Root

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580392750.png)

3. 配置pom.xml文件，添加MyBatis和其他需要的依赖进去。

   ```xml
   <dependency>
     <groupId>org.mybatis</groupId>
     <artifactId>mybatis</artifactId>
     <version>3.5.1</version>
   </dependency>
   <dependency>
     <groupId>com.oracle</groupId>
     <artifactId>ojdbc7</artifactId>
     <version>7.0</version>
   </dependency>
   ```

   

4. 处理连接数据库、加载映射⽂件——**主配置**⽂件，整个项⽬有⼀个就⾏

   ```xml
    <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE configuration
           PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-config.dtd">
   <configuration>
   <!--environments环境：数据库的连接环境，可以配置多个  default指向默认的数据 源-->
       <environments default="development">
           <!--environment代表一个数据源环境：transactionManager事务管理   dataSource数据源-->
           <environment id="development">
               <!--事务管理：JDBC——java管理事务  Managed：容器管理-->
               <transactionManager type="JDBC"/>
               <!--数据源管理：type——pooled、unpooled、jndi：远程连接，
               在容器中配置的数据源，在本地通过jndi连接-->
               <dataSource type="POOLED">
                   <property name="driver" value="oracle.jdbc.OracleDriver"/>
                   <property name="url" value="jdbc:oracle:thin:@localhost:1521:orcl"/>
                   <property name="username" value="scott"/>
                   <property name="password" value="root"/>
               </dataSource>
           </environment>
       </environments>
       <mappers>
           <mapper resource="com/mapper/EmpMapper.xml"/>
       </mappers>
   </configuration>
   ```

   

5. 处理映射关系——sql语句⾃⼰书写，返回结果映射——映射⽂件，⼀张表对应⼀个⽂件

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE mapper
           PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
   <!--namespace:命名空间的意思——用来定位   namespace名字.标签的id名-->
   <mapper namespace="com.mapper.EmpMapper">
   <!--select是一个查询标签  id是一个唯一性标志   resultType是返回值的数据类型->
       <select id="selectEmp" resultType="HashMap">
         select * from emp
       </select>
   </mapper>
   ```

   **层级关系**

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580392948.png)

6. 调⽤sql即可

   ```java
   public class TestMain {
       public static void main(String[] args) {
           try {
               //Resources  加载配置文件
               Reader reader = Resources.getResourceAsReader("config/MyBatis_config.xml");
               //sqlSessionFactoryBuilder  根据主配置文件，创建sqlSessionFactory
               SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
               SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(reader);
               //sqlSession 对应每一个sql。sqlSession是一个会话，在其中会启动一个事务，执行sql用sqlSession
               SqlSession sqlSession = sqlSessionFactory.openSession();
               //通过namespace.id名找到对应标签
               List list = sqlSession.selectList("com.mapper.EmpMapper.selectEmp");
               System.out.println(list);
   
           } catch (IOException e) {
               e.printStackTrace();
           }
       }
   }
   ```





# 5.Mybatis多种形式的使用

## 5.1 映射文件配合接口

1. 在主配置文件中配置xml文件的映射

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580393143.png)

2. sql映射文件

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE mapper
           PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
   <mapper namespace="com.mapper.EmpMapper">
       <select id="selectEmp" resultType="HashMap">
         select * from emp
       </select>
       <!--实体类-->
       <select id="Emp" resultType="com.entity.EmpEntity">
         select * from emp
       </select>
       <!--新增员工-->
       <insert id="addEmp" parameterType="HashMap">
           insert into emp(empno,ename) values(#{empno},#{ename})
       </insert>
       <!--删除员工-->
       <delete id="delEmp" parameterType="int">
           delete from emp where empno=#{empno}
       </delete>
       <!--修改员工信息-->
       <update id="updEmp" parameterType="HashMap">
           update emp set sal=#{sal},comm=#{comm} where empno=#{empno}
       </update>
       <!--查询员工-->
       <select id="queryEmp" parameterType="int" resultType="HashMap">
           select * from emp where empno=#{empno}
       </select>
   </mapper>
   ```

   

3. 接口文件

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580393195.png)

4. 测试方法

   ```java
   public class TestMain {
       public static void main(String[] args) {
           try {
               //Resources  加载配置文件
               Reader reader = Resources.getResourceAsReader("config/MyBatis_config.xml");
               //sqlSessionFactoryBuilder  根据主配置文件，创建sqlSessionFactory
               SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
               SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(reader);
               //sqlSession 对应每一个sql。sqlSession是一个会话，在其中会启动一个事务，执行sql用sqlSession
               SqlSession sqlSession = sqlSessionFactory.openSession();
              
               //通过接口的方式
               EmpMapper empMapper = sqlSession.getMapper(EmpMapper.class);
               List list = empMapper.selectEmp();
               System.out.println(list);
   
           } catch (IOException e) {
               e.printStackTrace();
           }
       }
   }
   ```

   

## 5.2 接口配合注解

1. 在主配置文件中配置接口的类文件路径。sql映射文件可以不需要配置，因为全部使用的是注解方式。存在映射文件一般用resource，只要	接口一般用class。

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580393244.png)

2. 接口Class类

   ```java
   public interface EmpMapper {
   
       //查询所有员工
       @Select("select * from emp")
       List<HashMap> queryAllEmp();
   
       //查询指定员工
       @Select("select * from emp where empno=#{empno}")
       List<HashMap> queryEmp(int empno);
   
       //添加新员工
       @Insert("insert into emp(empno,ename) values(#{empno},#{ename})")
       int addEmp(HashMap map);
   
       //修改员工信息
       @Update("update emp set sal=#{sal},comm=#{comm} where empno=#{empno}")
       int updateEmp(HashMap map);
   
       //删除指定员工
       @Delete("delete from emp where empno=#{empno}")
       int delEmp(int empno);
   }
   ```

   

3. 测试类

   ```java
   public class TestMain {
       public static void main(String[] args) throws IOException {
           //加载主配置文件
           Reader reader = Resources.getResourceAsReader("config/MyBatis_config.xml");
           SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
           SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(reader);
           SqlSession sqlSession = sqlSessionFactory.openSession();
           EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
   
           /*
           //查询所有员工
           List<HashMap> list = mapper.queryAllEmp();
           System.out.println(list);
   
           //查询指定员工
           List<HashMap> queryEmp = mapper.queryEmp(7900);
           System.out.println(queryEmp);
           */
   
           /*
           //添加员工
           HashMap addmap = new HashMap();
           addmap.put("empno",8000);
           addmap.put("ename","zhangsan");
           int addEmpres = mapper.addEmp(addmap);
           sqlSession.commit();
           sqlSession.close();
           System.out.println("添加"+addEmpres+"条数据成功");
           */
   
           /*
           //修改员工
           HashMap updmap = new HashMap();
           updmap.put("empno",8000);
           updmap.put("sal",2000);
           updmap.put("comm",800);
           int updres = mapper.updateEmp(updmap);
           sqlSession.commit();
           sqlSession.close();
           System.out.println("修改"+updres+"条员工信息成功！");
           */
   
           //删除员工
           int delres = mapper.delEmp(8000);
           sqlSession.commit();
           sqlSession.close();
           System.out.println("删除"+delres+"条员工信息成功！");
       }
   }
   ```

   

   

# 6.Mybatis注意事项

+ 增删改没有return，默认返回值数据类型是int，指的是影响条数。有parameterType ，表示接收参数的类型。 
+ 注解或者使用接口的方式，都可以通过  #{如果接受参数为Map，此处为key}  传值
+ 在使用增删改以后，需要用sqlSession进行commit提交和close关闭，否则sql不生效。
+ 在进行删除以后，进行其他操作不生效的话，可以看看PLSQL的提交是否正常（oracle情况下）。