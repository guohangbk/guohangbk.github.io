# 1.主配置文件

##	1.1 属性（properties）

属性都是可外部配置且可动态替换的，既可以在典型的 Java 属性文件中配置，亦可通过 properties 元素的子元素来传递。例如：

```xml
db.url=jdbc:oracle:thin:@localhost:1521:orcl
db.driver=oracle.jdbc.OracleDriver
db.username=scott
db.password=root
```

然后其中的属性就可以在整个配置文件中被用来替换需要动态配置的属性值。比如：

```xml
<configuration>
    <properties resource="config/db.properties.properties"/>
    <typeAliases>
        <typeAlias alias="ProEntity" type="com.entity.ProEntity"/>
        <typeAlias alias="EmpEntity" type="com.entity.EmpEntity"/>
    </typeAliases>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${db.driver}"/>
                <property name="url" value="${db.url}"/>
                <property name="username" value="${db.username}"/>
                <property name="password" value="${db.password}"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="com/mapper/EmpMpper.xml"/>
        <mapper resource="com/mapper/ProMapper.xml"/>
    </mappers>
</configuration>

```



## 1.2 设置（settings）

这是 MyBatis 中极为重要的调整设置，它们会改变 MyBatis 的运行时行为。 下表描述了设置中各项的意图、默认值等。

| **设置名**          | **描述**                                                     | **有效值**                                                   | **默认值** |
| ------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ---------- |
| cacheEnabled        | 全局地开启或关闭配置文件中的所有映射器已经配置的任何缓存。   | true \| false                                                | true       |
| lazyLoadingEnabled  | 延迟加载的全局开关。当开启时，所有关联对象都会延迟加载。 特定关联关系中可通过设置 fetchType属性来覆盖该项的开关状态。 | true \| false                                                | false      |
| useGeneratedKeys    | 允许 JDBC 支持自动生成主键，需要驱动支持。 如果设置为 true 则这个设置强制使用自动生成主键，尽管一些驱动不能支持但仍可正常工作（比如 Derby）。 | true \| false                                                | False      |
| autoMappingBehavior | 指定 MyBatis 应如何自动映射列到字段或属性。 NONE 表示取消自动映射；PARTIAL 只会自动映射没有定义嵌套结果集映射的结果集。 FULL 会自动映射任意复杂的结果集（无论是否嵌套）。 | NONE, PARTIAL, FULL                                          | PARTIAL    |
| jdbcTypeForNull     | 当没有为参数提供特定的 JDBC 类型时，为空值指定 JDBC 类型。 某些驱动需要指定列的 JDBC 类型，多数情况直接用一般类型即可，比如 NULL、VARCHAR 或 OTHER。 | JdbcType 常量，常用值：NULL, VARCHAR 或 OTHER。              | OTHER      |
| logImpl             | 指定 MyBatis 所用日志的具体实现，未指定时将自动查找。        | SLF4J \| LOG4J \| LOG4J2 \| JDK_LOGGING \| COMMONS_LOGGING \| STDOUT_LOGGING \| NO_LOGGING | 未设置     |



## 1.3 类型别名（typeAliases）

类型别名是为 Java 类型设置一个短的名字。 它只和 XML 配置有关，存在的意义仅在于用来减少类完全限定名的冗余。例如：

```xml
<typeAliases>
  <typeAlias alias="Author" type="domain.blog.Author"/>
  <typeAlias alias="Blog" type="domain.blog.Blog"/>
  <typeAlias alias="Comment" type="domain.blog.Comment"/>
  <typeAlias alias="Post" type="domain.blog.Post"/>
  <typeAlias alias="Section" type="domain.blog.Section"/>
  <typeAlias alias="Tag" type="domain.blog.Tag"/>
</typeAliases>
```

当这样配置时，``Blog`` 可以用在任何使用 ``domain.blog.Blog`` 的地方。

也可以指定一个包名，MyBatis 会在包名下面搜索需要的 Java Bean，比如：

```xml
<typeAliases>
  <package name="domain.blog"/>
</typeAliases>
```



## 1.4 环境配置（environments）

MyBatis 可以配置成适应多种环境，这种机制有助于将 SQL 映射应用于多种数据库之中， 现实情况下有多种理由需要这么做。例如，开发、测试和生产环境需要有不同的配置；或者想在具有相同 Schema 的多个生产数据库中 使用相同的 SQL 映射。有许多类似的使用场景。

**不过要记住：尽管可以配置多个环境，但每个 SqlSessionFactory 实例只能选择一种环境。**

所以，如果你想连接两个数据库，就需要创建两个 SqlSessionFactory 实例，每个数据库对应一个。而如果是三个数据库，就需要三个实例，依此类推，记起来很简单：

- 每个数据库对应一个 SqlSessionFactory 实例



## 1.5 映射器（mappers）

MyBatis配置完了就要定义 SQL 映射语句了。 但是首先我们需要告诉 MyBatis 到哪里去找到这些语句。 Java 在自动查找这方面没有提供一个很好的方法，所以最佳的方式是告诉 MyBatis 到哪里去找映射文件。 可以使用相对于类路径的资源引用， 或完全限定资源定位符（包括 file:/// 的 URL），或类名和包名等。例如：

```xml
<!-- 使用相对于类路径的资源引用 -->
<mappers>
  <mapper resource="org/mybatis/builder/AuthorMapper.xml"/>
  <mapper resource="org/mybatis/builder/BlogMapper.xml"/>
  <mapper resource="org/mybatis/builder/PostMapper.xml"/>
</mappers>

```

```xml
<!-- 使用完全限定资源定位符（URL） -->
<mappers>
  <mapper url="file:///var/mappers/AuthorMapper.xml"/>
  <mapper url="file:///var/mappers/BlogMapper.xml"/>
  <mapper url="file:///var/mappers/PostMapper.xml"/>
</mappers>
```

```xml
<!-- 使用映射器接口实现类的完全限定类名 -->
<mappers>
  <mapper class="org.mybatis.builder.AuthorMapper"/>
  <mapper class="org.mybatis.builder.BlogMapper"/>
  <mapper class="org.mybatis.builder.PostMapper"/>
</mappers>
```

```xml
<!-- 将包内的映射器接口实现全部注册为映射器 -->
<mappers>
  <package name="org.mybatis.builder"/>
</mappers>
```

这些配置会告诉了 MyBatis 去哪里找映射文件，剩下的细节就应该是每个 SQL 映射文件了。



#	2.映射文件

MyBatis 的真正强大在于它的映射语句，这是它的魔力所在。由于它的异常强大，映射器的 XML 文件就显得相对简单。如果拿它跟具有相同功能的 JDBC 代码进行对比，你会立即发现省掉了将近 95% 的代码。MyBatis 为聚焦于 SQL 而构建，以尽可能地为你减少麻烦。

SQL 映射文件只有很少的几个顶级元素（按照应被定义的顺序列出）：

- ``cache`` – 对给定命名空间的缓存配置。
- ``cache-ref`` – 对其他命名空间缓存配置的引用。
- ``resultMap`` – 是最复杂也是最强大的元素，用来描述如何从数据库结果集中来加载对象。
- ``sql`` – 可被其他语句引用的可重用语句块。
- ``insert`` – 映射插入语句
- ``update`` – 映射更新语句
- ``delete`` – 映射删除语句
- ``select`` – 映射查询语句



## 2.1 接收参数

1. **int/String单一值**

   ```xml
   //EmpMapper.xml
   <select id="selectEmpByEntity" resultType="com.entity.EmpEntity">
     select * from emp where empno=#{empno}
   </select>
   ```

   ```java
   //EmpMapper.java
   EmpEntity selectEmpByEntity(int empno);
   ```

   

2. **多个值**

   ```xml
   //EmpMapper.xml
   <delete id="delEmp">
       delete from emp where empno=#{empno} and ename=#{ename}
   </delete>
   ```

   ```java
   //EmpMapper.java
   //传多个值进行删除员工(主要看传值方式)
   int delEmp(@Param("empno") Integer empno, @Param("ename") String ename);
   ```

   

3. **实体类**

   可以使用 #{属性名}  获取值，属性名严格区分⼤⼩写，和属性必须完全匹配

   ```xml
   //EmpMapper.xml
   <insert id="insertEmpEntity" parameterType="com.entity.EmpEntity">
       insert into emp(empno,ename)values(#{empno},#{ename})
   </insert>
   ```

   ```java
   //EmpMapper.java
   int insertEmpEntity(EmpEntity emp);
   ```

   

4. **Map**

   ```XML
   //EmpMapper.xml
   <insert id="insertEmp" parameterType="HashMap">
       insert into emp(empno,ename)values(#{a},#{b})
   </insert>
   ```

   ```JAVA
   //EmpMapper.java
   int insertEmp(HashMap map);
   
   //Test.java
   HashMap map=new HashMap<>();
   map.put("a",25);
   map.put("b","dd");
   int aa=empMapper.insertEmp(map);
   ```

   

5. **List**

   当需要数据批量操作的时候，我们可以在java中进行遍历然后将数据依次送到数据库中操作，但是这样接收参数会使java与数据库频繁连接，不仅耗时而且还会增加数据库的压力。这个时候我们可以使用List进行批量数据操作，直接将List传入，然后在sql中使用foreach进行遍历操作数据，这样是对数据库进行了一次连接的多次操作。

   ```xml
   <!-- 插入物品信息 -->
   <insert id="addRItem" parameterType="java.util.List">
       insert into lzf_rental_item_detailsl
       (   
           id,
           rentalInfoId,
           itemName,
           number,
           remark
       )
       values
       <foreach collection="list" item="item" index= "index" separator =",">
       (
           #{item.id},
           #{item.rentalInfoId},
           #{item.itemName},
           #{item.number},
           #{item.remark}
       )
       </foreach>
   </insert>
   ```

   此处也可以将List封装进map中传参，然后在foreach中的collection属性中填写map的key。

6. **数组**

   大致同上



## 2.2 返回值

1. **int/String**

   如果只查询单行单列的话，只有一个数据，可以只返回一个数据，直接书写对应的数据类型即可。

   ```xml
   //EmpMapper.xml
   <select id="selectEmpByID3" resultType="String">
     select ename from Emp where empno=#{empno}
   </select>
   ```

   ```java
   //EmpMapper.java
   String selectEmpByID3(Integer empno);
   ```

   

2. **实体类**

   可以返回0条或者1条查询数据

   ```xml
   //EmpMapper.xml
   <!--如果没有起别名，resultType必须用全类名，起了别名之后，可以使用别名-->
   <select id="selectEmpByEntity" resultType="com.entity.EmpEntity">
     select * from emp where empno=#{empno}
   </select>
   ```

   ```java
   //EmpMapper.java
   //实体类形式通过empno查询某个员工
   EmpEntity selectEmpByEntity(int empno);
   ```

   

3. **HashMap**

   可以返回0条或者1条查询数据

   ```xml
   //EmpMapper.xml
   <select id="selectEmpByID" resultType="HashMap">
     select * from Emp where empno=#{empno}
   </select>
   ```

   ```java
   //EmpMapper.java
   HashMap selectEmpByID(Integer empno);
   ```

   

4. **List<实体或者HashMap>**

   可以返回0条、一条或者多条查询数据

   ```xml
   //EmpMapper.xml
   <select id="selectEmp" resultType="HashMap">
     select * from emp
   </select>
   
   //EmpMapper.java
   //ListMap形式查询所有员工
   List<HashMap> selectEmp();
   -------------------------------------------------------------
   //EmpMapper.xml
   <select id="selectEmp2" resultType="EmpEntity">
     select * from emp
   </select>
   
   //EmpMapper.java
   List<EmpEntity> selectEmp2();
   ```

   

5. **手动映射实体类**

   虽然MyBatis可以自动映射，帮我们自动对应查询结果，但是在一些情况下还是对应不上的。比如我们在查询语句中使用了别名，但是我们的代码并没有使用这个别名，这个时候自动映射就不能对应数据了，需要我们手动映射，将结果和属性对应上。

   ```xml
   //autoMapping:自动映射，不写读取的是全局的。写了以自己不主。
   //一般开启自动映射，只书写对应不上的。
   <resultMap id="empMapper" type="com.entity.EmpEntity" autoMapping="true">
      <!--唯一列：表字段和类属性,提高性能 column:结果列名-->
      <id column="empnn" property="empno"/>
      <!--如果列名和属性名一致，可以不显示映射，自动可以映射，resultMap 中只书写对应不上的即可--> 
      <!--普通列-->
      <!--<result column="ename" property="ename"/>-->
   </resultMap>
   ```

   ```java
   <select id="selectEmpByID_x" resultMap="empMapper">
     select empno as empnn,ename from Emp where empno=#{empno}
   </select>
   ```

   

## 2.3 select详解

查询语句是 MyBatis 中最常用的元素之一，光能把数据存到数据库中价值并不大，只有还能重新取出来才有用，多数应用也都是查询比修改要频繁。对每个插入、更新或删除操作，通常间隔多个查询操作。

select 元素允许配置很多属性来配置每条语句的作用细节。

**部分属性介绍：**

| id            | 在命名空间中唯一的标识符，可以被用来引用这条语句。           |
| ------------- | ------------------------------------------------------------ |
| parameterType | 将会传入这条语句的参数类的完全限定名或别名。这个属性是可选的，因为 MyBatis 可以通过类型处理器（TypeHandler） 推断出具体传入语句的参数，默认值为未设置（unset）。 |
| resultType    | 从这条语句中返回的期望类型的类的完全限定名或别名。 注意如果返回的是集合，那应该设置为集合包含的类型，而不是集合本身。可以使用 resultType 或 resultMap，但不能同时使用。 |
| resultMap     | 外部 resultMap 的命名引用。结果集的映射是 MyBatis 最强大的特性，如果你对其理解透彻，许多复杂映射的情形都能迎刃而解。可以使用 resultMap 或 resultType，但不能同时使用。 |
| statementType | STATEMENT，PREPARED 或 CALLABLE 中的一个。这会让 MyBatis 分别使用 Statement，PreparedStatement 或 CallableStatement，默认值：PREPARED。 |



## 2.4 insert、update、delete详解

数据变更语句 insert，update 和 delete 的实现非常接近：

| 属性             | 描述                                                         |
| ---------------- | ------------------------------------------------------------ |
| id               | 命名空间中的唯一标识符，可被用来代表这条语句。               |
| parameterType    | 将要传入语句的参数的完全限定类名或别名。这个属性是可选的，因为 MyBatis 可以通过类型处理器推断出具体传入语句的参数，默认值为未设置（unset）。 |
| statementType    | STATEMENT，PREPARED 或 CALLABLE 的一个。这会让 MyBatis 分别使用 Statement，PreparedStatement 或 CallableStatement，默认值：PREPARED。 |
| useGeneratedKeys | （仅对 insert 和 update 有用）这会令 MyBatis 使用 JDBC 的 getGeneratedKeys 方法来取出由数据库内部生成的主键（比如：像 MySQL 和 SQL Server 这样的关系数据库管理系统的自动递增字段），默认值：false。 |
| keyProperty      | （仅对 insert 和 update 有用）唯一标记一个属性，MyBatis 会通过 getGeneratedKeys 的返回值或者通过 insert 语句的 selectKey 子元素设置它的键值，默认值：未设置（unset）。如果希望得到多个生成的列，也可以是逗号分隔的属性名称列表。 |
| keyColumn        | （仅对 insert 和 update 有用）通过生成的键值设置表中的列名，这个设置仅在某些数据库（像 PostgreSQL）是必须的，当主键列不是表中的第一列的时候需要设置。如果希望使用多个生成的列，也可以设置为逗号分隔的属性名称列表。 |



## 2.5 新增返回主键

在Oracle中是没有主键自增和返回主键的方法的，所以我们可以使用自定义序列来实现同样效果。先查询出来序列下一个值，然后再将查出来的值放到新增语句中插入。

```xml
//EmpMapper.xml
<insert id="insertEmp" parameterType="HashMap">
    <!--keyProperty：代表属性 实体属性或者map的key，会将查询出来的主键放到a属性或 者key为a列中-->
    <!--resultType：返回值数据类型-->
    <!--order:新增之前执行还是新增之后-->
    <selectKey keyProperty="a" order="BEFORE" resultType="int">
      select seq_classid.nextval from dual
    </selectKey>
    insert into emp(empno,ename) values(#{a},#{b})
</insert>

//EmpMapper.java
//使用HashMap添加员工
int insertEmp(HashMap map);
```

新增返回主键：其实是将主键重新放回到传递的map中或者实体属性中

```java
@Test
public void insertEmp(){
    SqlSession session = bd.getSession();
    EmpMapper mapper = session.getMapper(EmpMapper.class);
    HashMap hashMap = new HashMap();
    hashMap.put("b","KOBE");
    int i = mapper.insertEmp(hashMap);
    session.commit();
    session.close();
   System.out.println(i);
   System.out.println(hashMap);
}
```

运行截图

![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580459486.png)

