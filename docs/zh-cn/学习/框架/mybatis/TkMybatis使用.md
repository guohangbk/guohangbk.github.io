# 1.MyBatis进化过程

1. Mybatis：一种操作数据库的框架，提供一种Mapper类，支持让你用java代码进行增删改查的数据库操作，省去了每次都要手写sql语句的麻烦。
2. Mybatis Generator：自动为Mybatis生成简单的增删改查sql语句的工具，省去一大票时间，两者配合使用，开发速度快到飞起。
3. TKmybaits： 替我们生成常用增删改查操作的SQL 语句
4. MybaitsPlus：国人团队苞米豆在Mybatis的基础上开发的框架，在Mybatis基础上扩展了许多功能
5. Mybatis Plus Generator：同样为苞米豆开发，比Mybatis Generator更加强大，支持功能更多，自动生成Entity、Mapper、Service、Controller等



TkMybatis是基于Mybatis框架开发的一个工具，通过调用它提供的方法实现对单表的数据操作，不需要写任何sql语句，这极大地提高了项目开发效率。





# 2.安装通用Mapper

1. **引入pom**

   ```xml
   <!--通用Mapper的依赖-->
   <dependency>
     <groupId>tk.mybatis</groupId>
     <artifactId>mapper</artifactId>
     <version>3.1.2</version>
   </dependency>
   ```

   

2. **在主配置文件中配置插件**

   在主配置文件中的properties下配置插件

   ```xml
   <plugins>
       <!-- 通用Maper -->
       <plugin interceptor="tk.mybatis.mapper.mapperhelper.MapperInterceptor">
           <!--================================================-->
           <!--可配置参数说明(一般无需修改)-->
           <!--UUID生成策略-->
           <!--配置UUID生成策略需要使用OGNL表达式-->
           <!--默认值32位长度:@java.util.UUID@randomUUID().toString().replace("-", "")-->
           <property name="UUID" value="@java.util.UUID@randomUUID().toString()"/>
           <!--序列的获取规则,使用{num}格式化参数，默认值为{0}.nextval，针对Oracle-->
           <!--可选参数一共3个，对应0,1,2,分别为SequenceName，ColumnName,PropertyName-->
           <property name="seqFormat" value="{0}.nextval"/>
           <!--主键自增回写方法执行顺序,默认AFTER,可选值为(BEFORE|AFTER)-->
           <property name="ORDER" value="before"/>
           <!--通用Mapper接口，多个通用接口用逗号隔开-->
           <property name="mappers" value="tk.mybatis.mapper.common.Mapper"/>
       </plugin>
   </plugins>
   ```

   

3. **书写一个接口，继承Mapper接口，泛型需要写实体类**

   TabOrder：按照这个实体的映射关系生成sql

   ```
   public interface TabOrderMapper extends Mapper<TabOrder> {
   }
   ```

   

4. **书写一个实体类**

   ```java
   @Table(name = "TAB_ORDER")
   public class TabOrder {
       @Id
       @GeneratedValue(strategy = GenerationType.IDENTITY,generator = "select seq_classid.nextval||'aaaa' from dual")
       @Column(name = "ORDERID")
       private String orderid;
   
       @Column(name = "CURID")
       private String curid;
   
       @Column(name = "ADDRESS")
       private String address;
   
       @Column(name = "RELATIVENAME")
       private String relativename;
   
       @Column(name = "MONEY")
       private BigDecimal money;
   
       /**
        * @return ORDERID
        */
       public String getOrderid() {
           return orderid;
       }
   
       /**
        * @param orderid
        */
       public void setOrderid(String orderid) {
           this.orderid = orderid == null ? null : orderid.trim();
       }
   
       /**
        * @return CURID
        */
       public String getCurid() {
           return curid;
       }
   
       /**
        * @param curid
        */
       public void setCurid(String curid) {
           this.curid = curid == null ? null : curid.trim();
       }
   
       /**
        * @return ADDRESS
        */
       public String getAddress() {
           return address;
       }
   
       /**
        * @param address
        */
       public void setAddress(String address) {
           this.address = address == null ? null : address.trim();
       }
   
       /**
        * @return RELATIVENAME
        */
       public String getRelativename() {
           return relativename;
       }
   
       /**
        * @param relativename
        */
       public void setRelativename(String relativename) {
           this.relativename = relativename == null ? null : relativename.trim();
       }
   
       /**
        * @return MONEY
        */
       public BigDecimal getMoney() {
           return money;
       }
   
       /**
        * @param money
        */
       public void setMoney(BigDecimal money) {
           this.money = money;
       }
   
   
       @Override
       public String toString() {
           return "TabOrder{" +
                   "orderid='" + orderid + '\'' +
                   ", curid='" + curid + '\'' +
                   ", address='" + address + '\'' +
                   ", relativename='" + relativename + '\'' +
                   ", money=" + money +
                   '}';
       }
   }
   ```

   解释：

   @table：本实体对应哪张表。可以不写，自动将数据库的表进行映射，名称一致或者_转驼峰 tab_order ---- TabOrder

   @Id:主键。可以加给一列也可以加给多列。

   不写也不会报错，默认所有列都为主键。SelectById---where

   @column:普通列——也可以都不写，自动映射列名，属性名和字段名一致

   @Transient：忽略

   

5. **主配置引入**

   ```xml
   <mappers>
       <mapper class="com.mapper.TabOrderMapper"/>
   </mappers>
   ```

   

6. **测试**

   ```java
   @Test
   public void test(){
       BaseDao baseDao=new BaseDao();
       SqlSession sqlSession=baseDao.getSession();
       TabOrderMapper orderMapper=sqlSession.getMapper(TabOrderMapper.class);
       TabOrder t=new TabOrder();
       List list=orderMapper.select(t);
   }
   ```

   



# 3.通用mapper使用

## 3.1 查询

```java
    public void selectTest(){
        SqlSession session = bd.getSession();
        TabOrderMapper mapper = session.getMapper(TabOrderMapper.class);
        TabOrder tabOrder = new TabOrder();
        tabOrder.setAddress("a");
        tabOrder.setOrderid("b");
        List<TabOrder> select = mapper.select(tabOrder);
    }

    //复杂检索，自定义检索条件
    public void selectByExample(){
        SqlSession session = bd.getSession();
        TabOrderMapper mapper = session.getMapper(TabOrderMapper.class);
        Example example = new Example(TabOrder.class);
        Example.Criteria criteria = example.createCriteria();
        //设置where条件和值
        criteria.andEqualTo("relativename","小马儿");
        List list = new ArrayList();
        list.add("3");
        list.add("4");
        criteria.andIn("curid",list);
        //使用or的话需要重新创建一个criteria
        Example.Criteria criteria2 = example.createCriteria();
        criteria2.andEqualTo("money",20000);
        example.or(criteria2);
        mapper.selectByExample(example);
    }

    //查询主键
    public void selectByPrimaryKey(){
        SqlSession session = bd.getSession();
        TabOrderMapper mapper = session.getMapper(TabOrderMapper.class);
        //单一主键
        mapper.selectByPrimaryKey("1600305670");
        //复合主键，需要设置其他对应的属性为ID
/*        TabOrder tabOrder = new TabOrder();
        tabOrder.setOrderid("1600305670");
        tabOrder.setRelativename("小马儿");
        mapper.selectByPrimaryKey(tabOrder);*/
    }

    //查询结果的条数
    public void selectCount(){
        SqlSession session = bd.getSession();
        TabOrderMapper mapper = session.getMapper(TabOrderMapper.class);
        TabOrder tabOrder = new TabOrder();
        tabOrder.setMoney(new BigDecimal(22994.00));
        int i = mapper.selectCount(tabOrder);
        System.out.println(i);
    }

    //查询结果并返回实体，只有一个结果的时候可以直接用
    public void selectOne(){
        SqlSession session = bd.getSession();
        TabOrderMapper mapper = session.getMapper(TabOrderMapper.class);
        TabOrder tabOrder = new TabOrder();
        tabOrder.setCurid("3");
        TabOrder order = mapper.selectOne(tabOrder);
        System.out.println(order);
    }
```



## 3.2 新增

```
//全表新增
public void insert(){
    SqlSession session = bd.getSession();
    TabOrderMapper mapper = session.getMapper(TabOrderMapper.class);
    TabOrder tabOrder = new TabOrder();
    tabOrder.setOrderid("1770646235");
    tabOrder.setCurid("6");
    tabOrder.setRelativename("张三");
    tabOrder.setAddress("河南");
    tabOrder.setMoney(new BigDecimal(25000));
    //全表新增
    mapper.insert(tabOrder);
    //选择性新增：属性值为空，insert语句不要它。如果没有default默认，最后入库的null
    mapper.insertSelective(tabOrder);
}


//新增返回主键
public void insertGenerate(){
    SqlSession session = bd.getSession();
    TabOrderMapper mapper = session.getMapper(TabOrderMapper.class);
    TabOrder tabOrder = new TabOrder();
    tabOrder.setAddress("aaa");
    tabOrder.setCurid("123");
    mapper.insert(tabOrder);
    mapper.insertSelective(tabOrder);
}
```

设置主键的三种方法

**A. 序列**

@Id//代表是主键 默认所有列都为主键，那么byPrimarayKey的拼接都会拼接所有列 //设置主键策略 @GeneratedValue(strategy = GenerationType.IDENTITY,generator = "select seq_classid.nextval||'aaaa' from dual") @Column(name="orderid") private String orderid;

**B. UUID:依赖java，生成随机数**

@Id//代表是主键 默认所有列都为主键，那么byPrimarayKey的拼接都会拼接所有列 //设置主键策略 @GeneratedValue(generator = "UUID") @Column(name="orderid") private String orderid;

**C. 自增**

@GeneratedValue(strategy = GenerationType.IDENTITY)



## 3.3 修改

```java
//修改
public void update(){
    SqlSession session = bd.getSession();
    TabOrderMapper orderMapper=session.getMapper(TabOrderMapper.class);
    TabOrder t=new TabOrder();
    t.setAddress("dddd");
    t.setCurid("ddd");
    //根据主键修改
    t.setOrderid("a");
    //orderMapper.updateByPrimaryKey(t);
    //根据主键选择修改
    orderMapper.updateByPrimaryKeySelective(t);
    //自定义检索条件修改
    Example example=new Example(TabOrder.class);
    Example.Criteria criteria=example.createCriteria();
    criteria.andLike("address","%E%");
    //orderMapper.updateByExample(t,example);
    //自定义检索条件  选择性修改
    orderMapper.updateByExampleSelective(t,example);
}
```



## 3.4 删除

```java
//删除
public  void delete(){
    SqlSession session = bd.getSession();
    TabOrderMapper mapper = session.getMapper(TabOrderMapper.class);
    TabOrder t = new TabOrder();
    t.setCurid("ddd");
    t.setAddress("ccc");
    //所有非空属性都做为检索条件，如果对象为空，那么全表删除 ——delete 表名  where 条件
    mapper.delete(t);
    //根据主键删除--必须保证实体中主键有值，其他有没有都不影响
    t.setOrderid("asd");
    mapper.deleteByPrimaryKey(t);
    //自定义检索条件删除
    Example example = new Example(TabOrder.class);
    Example.Criteria criteria = example.createCriteria();
    criteria.andLike("address","%E%");
    mapper.deleteByExample(example);
}
```





# 4.通用Mapper整合逆向工程

1. **pom中引入逆向工程插件**

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
     <dependency>
       <groupId>tk.mybatis</groupId>
       <artifactId>mapper</artifactId>
       <version>3.4.4</version>
     </dependency>
   </dependencies>
   </plugin>
   ```

   

2. 反向工程添加

   在generatorConfig.xml中添加通用Mapper的插件，可以生成带注解的实体类

   ```java
   <property name="javaFileEncoding" value="UTF-8"/>
   <!--  通用 Mapper 插件，可以生成带注解的实体类 -->
   <plugin type="tk.mybatis.mapper.generator.MapperPlugin">
       <property name="mappers" value="tk.mybatis.mapper.common.Mapper"/>
       <property name="caseSensitive" value="true"/>
       <property name="forceAnnotation" value="true"/>
       <property name="beginningDelimiter" value="`"/>
       <property name="endingDelimiter" value="`"/>
   </plugin>
   ```

   如果在工程中添加pom以后报红，可以在侧边栏的Maven中使用下载或刷新，也可以直接使用逆向工程插件，使用的时候会加载插件。

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580477508.png)

   