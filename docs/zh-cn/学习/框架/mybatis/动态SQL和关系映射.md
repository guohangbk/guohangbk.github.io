# 1.动态SQL

MyBatis 的强大特性之一便是它的动态 SQL。如果你有使用 JDBC 或其它类似框架的经验，你就能体会到根据不同条件拼接 SQL 语句的痛苦。例如拼接时要确保不能忘记添加必要的空格，还要注意去掉列表最后一个列名的逗号。利用动态 SQL 这一特性可以彻底摆脱这种痛苦。

虽然在以前使用动态 SQL 并非一件易事，但正是 MyBatis 提供了可以被用在任意 SQL 映射语句中的强大的动态 SQL 语言得以改进这种情形。

## 1.1 if-分支

动态 SQL 通常要做的事情是根据条件包含 where 子句的一部分。

```xml
//测试类需要输入ename的模糊匹配   sal做等值匹配  奖金做范围匹配
//第一种检索方式
<select id="selectEmp" resultType="HashMap" parameterType="HashMap">
    select ename,sal,dname from emp inner join dept on emp.deptno=dept.deptno where 1=1
        <!--ename:map中的key对应-->
         <if test="ename!=null">
            and ename like '%'||#{ename}||'%'
        </if>
        <if test="sal!=null">
            and sal=#{sal}
        </if>
        <if test="commbegin!=null">
            and comm>#{commbegin}
        </if>
        <if test="commend!=null">
            and comm &lt; #{commend}
        </if>
</select>
```

```xml
//第二种检索方式
<select id="selectEmp" resultType="HashMap" parameterType="HashMap">
    select ename,sal,dname from emp inner join dept on emp.deptno=dept.deptno
        <!--第二种检索方式and ename like '%${ename}%'-->
        <if test="ename!=null">
            and ename like '%${ename}%'
        </if>
        <if test="sal!=null">
            and sal=#{sal}
        </if>
        <if test="commbegin!=null">
            and comm>#{commbegin}
        </if>
        <if test="commend!=null">
            and comm &lt; #{commend}
        </if>
</select>
```

**# 和$区别：#是？占位，而$直接取值。如果是字符串，那么#{}类似？，然后拼接的时候自动带着''，$:取字符串只获取了值**



## 1.2 choose(when,otherwise)-多重分支

有时我们不想应用到所有的条件语句，而只想从中择其一项。针对这种情况，MyBatis 提供了 choose 元素，它有点像 Java 中的 switch 语句。

```xml
//deptno:如果传递的type为1  deptno=20  type=2  deptno=30  否则查deptno=40
<select id="selectEmp" resultType="HashMap" parameterType="HashMap">
    select ename,sal,dname from emp inner join dept on
     emp.deptno=dept.deptno
    <choose>
        <when test="type=1">
            and a.deptno=20
        </when>
        <when test="type=2">
            and a.deptno=30
        </when>
       <otherwise>
           and a.deptno=40
       </otherwise>
    </choose>
</select>
```



## 1.3 trim(where,set)-前后补全、前后覆盖

*where* 元素只会在至少有一个子元素的条件返回 SQL 子句的情况下才去插入“WHERE”子句。而且，若语句的开头为“AND”或“OR”，*where* 元素也会将它们去除。

如果 *where* 元素没有按正常套路出牌，我们可以通过自定义 trim 元素来定制 *where* 元素的功能。比如，和 *where* 元素等价的自定义 trim 元素为：

```xml
<trim prefix="WHERE" prefixOverrides="AND |OR ">
  ...
</trim>
```

*prefixOverrides* 属性会忽略通过管道分隔的文本序列（注意此例中的空格也是必要的）。它的作用是移除所有指定在 *prefixOverrides* 属性中的内容，并且插入 *prefix* 属性中指定的内容。

类似的用于动态更新语句的解决方案叫做 *set*。*set* 元素可以用于动态包含需要更新的列，而舍去其它的。比如：

```xml
<update id="updateAuthorIfNecessary">
  update Author
    <set>
      <if test="username != null">username=#{username},</if>
      <if test="password != null">password=#{password},</if>
      <if test="email != null">email=#{email},</if>
      <if test="bio != null">bio=#{bio}</if>
    </set>
  where id=#{id}
</update>
```

这里，*set* 元素会动态前置 SET 关键字，同时也会删掉无关的逗号，因为用了条件语句之后很可能就会在生成的 SQL 语句的后面留下这些逗号。

与*set* 元素等价的自定义 trim 元素的代码

```xml
<trim prefix="SET" suffixOverrides=",">
  ...
</trim>
```



**案例**

```xml
<select id="selectEmp" resultType="HashMap" parameterType="HashMap">
    select ename,sal,dname from emp inner join dept on emp.deptno=dept.deptno
<!--前补全where  前覆盖：and  会在最前边补一个where 然后将where后紧邻着的那一个
    and给去掉，如果没有就不管-->
    <!--<trim prefix="where" prefixOverrides="and">该行等价于where-->
   <!--where标签是固定的：固定的前补全where 前覆盖：and or-->     
    <where>
        <!--第一种模糊检索方式and ename like '%'||#{ename}||'%'-->
        <!--第二种检索方式and ename like '%${ename}%'-->
        <if test="ename!=null">
            and ename like '%${ename}%'
        </if>
        <if test="sal!=null">
            and sal=#{sal}
        </if>
        <if test="commbegin!=null">
            and comm>#{commbegin}
        </if>
        <if test="commend!=null">
            and comm &lt; #{commend}
        </if>
        order by ${colname}
    </where>
    <!--</trim>-->
</select>
```

```xml
<update id="updateEmp" parameterType="HashMap">
    update emp
    <trim prefix="set" suffixOverrides=",">  //此处可以直接写set
        <if test="ename!=null">ename=#{ename},</if>
        <if test="sal!=null">sal=#{sal},</if>
        <if test="comm!=null">comm=#{comm},</if>
        <if test="deptno!=null">deptno=#{deptno},</if>
    </trim>
    where empno=#{empno}
</update>
```



## 1.4 foreach-循环

动态 SQL 的另外一个常用的操作需求是对一个集合进行遍历，通常是在构建 IN 条件语句的时候。比如：

```xml
<select id="selectPostIn" resultType="domain.blog.Post">
  SELECT *
  FROM POST P
  WHERE ID in
  <foreach item="item" index="index" collection="list"
      open="(" separator="," close=")">
        #{item}
  </foreach>
</select>
```

*foreach*元素的功能非常强大，它允许你指定一个集合，声明可以在元素体内使用的集合项（item）和索引（index）变量。它也允许你指定开头与结尾的字符串以及在迭代结果之间放置分隔符。这个元素是很智能的，因此它不会偶然地附加多余的分隔符。

你可以将任何可迭代对象（如 List、Set 等）、Map 对象或者数组对象传递给 *foreach* 作为集合参数。当使用可迭代对象或者数组时，index 是当前迭代的次数，item 的值是本次迭代获取的元素。当使用 Map 对象（或者 Map.Entry 对象的集合）时，index 是键，item 是值。



**案例**

```xml
map.put("deptnos",new int[]{10,20});
List list=empMapper.selectEmp(map);
-----
<if test="deptnos!=null">
   and a.deptno in
    <!--for(int deptno:deptnos)  (20,30,40) open以什么打开 前补全
    close最后补全  separator以什么分割--> 
   <foreach collection="deptnos" item="dno" open="(" close=")" 
       separator=","> 
      #{dno}
 </foreach>
</if>
```



**案例：批量新增**

接收参数为数组和List很类似，这个是接收参数为List的代码演示

```xml
<insert id="insertMoreDept">
    insert into dept(deptno,dname,loc)
    <foreach collection="list" item="dept" separator="union">
        select #{dept.deptno},#{dept.dname},#{dept.loc} from dual
    </foreach>
</insert>
//以下不是案例代码。上边循环完以后，类似于这个
select 1,'a','a' from dual1 
union1 
select 2,'a','a' from dual1 
union1 
select 3,'a','a' from dual
```

```java
//EmpMapper.java
//使用List批量新增
int insertMoreDept(List<Map> list);

//Test
public void insertMoreDept(){
    SqlSession session = bd.getSession();
    EmpMapper mapper = session.getMapper(EmpMapper.class);
    ArrayList List = new ArrayList();
    HashMap Map = new HashMap();
    Map.put("deptno",1);
    Map.put("dname","a");
    Map.put("loc","a2");
    List.add(Map);

    Map = new HashMap();
    Map.put("deptno",2);
    Map.put("dname","b");
    Map.put("loc","b2");
    List.add(Map);

    Map = new HashMap();
    Map.put("deptno",3);
    Map.put("dname","c");
    Map.put("loc","c2");
    List.add(Map);
    int i = mapper.insertMoreDept(List);
    session.commit();
    session.close();
    System.out.println(i);
}
```







# 2.1对多和多对1映射

现实生活中有很多1对多（多对1）的关系模型。比如，一个人可以有0到多套房子，0到多辆汽车；一个父亲有0到多个孩子等等。这种关系被称作1对多关系。反过来，房子与人，汽车与人的关系，以及孩子与父亲的关系就是多对一的关系。这里需要注意一点的是，多对一关系的一个前提是：一套确定的房子只能属于某个确定的人（不能属于多人）；一个确定的孩子也只能属于某个确定的父亲。

关系模型：父亲 vs 孩子（Father vs Son）。

关系映射：**one-to-many**

关系模型：孩子 vs 父亲（Son vs Father）。

关系映射：**many-to-one**

<font style="color:red">association是用于一对一和多对一，而collection是用于一对多的关系。</font>

不管是一对多还是多对一，都需要往实体里写一个属性进行关联。比如员工实体里需要存部门Dept属性，部门实体需要存emps属性。



## 2.1 多对一

员⼯表对部⻔表：多对1-------在多的⼀端存的1的实体

**实体**

```java
public class Emp {
    private Integer empno;
    private String ename;
    private Integer sal;
    private Integer comm;
    private Dept dept;  //这个属性为对应的1的实体
}
```

**配置**

```xml
<!--多对一，员工对部门-->
<resultMap id="empDept" type="com.entity.Emp" autoMapping="true">
    <id property="empno" column="empno"/>
    <!--多对一映射的，其实就是对Type中的实体中存储的实体做映射的-->
    <association property="dept" javaType="com.entity.Dept" autoMapping="true">
        <id property="deptno" column="deptno"/>
    </association>
</resultMap>
<select id="selectEmpDept" resultMap="empDept">
    select a.*,b.dname from emp a join dept b on a.deptno=b.deptno
</select>

//多对一
List<Emp> selectEmpDept();
```



## 2.2 一对多

部⻔表对员⼯表：1对多—在1的⼀端存多的列表

**实体**

```java
public class Dept {
    private Integer deptno;
    private String dname;
    private List<Emp> emps;  //这个属性为对应的多的列表
}
```

**配置**

```xml
<!--一对多，部门对员工-->
<resultMap id="deptEmp" type="com.entity.Dept" autoMapping="true">
    <id property="deptno" column="deptno"/>
    <!--ofType指向List中泛型的数据类型-->
    <collection property="emps" ofType="com.entity.Emp" autoMapping="true">
        <id property="empno" column="empno"/>
    </collection>
</resultMap>
<select id="selectDeptEmp" resultMap="deptEmp">
    select a.*,b.dname from emp a join dept b on a.deptno=b.deptno
</select>

//一对多
List<Dept> selectDeptEmp();
```

**懒加载**

```xml
//默认的积极加载：1+n——查询一次部门出来n条数据：n条sql去查询对应的员工信息
//fetchType="lazy"——懒加载：什么时候调用什么时候执行 
//eager:积极加载——需要或者 不需要第二条sql都会执行

<resultMap id="deptMore" type="com.entity.Dept">
    <id property="deptno" column="deptno"/>
    <!--默认是积极加载，一般用懒加载lazy-->
    <collection property="emps" ofType="com.entity.Emp" autoMapping="true" 
    select="selectEmpForDept" column="deptno" fetchType="lazy">
        <id property="empno" column="empno"/>
    </collection>
</resultMap>
//部门表数据，点击详情的时候才需要调用员工表数据
<select id="selectDept_More" resultMap="deptMore">
    select * from dept
</select>
<select id="selectEmpForDept" resultType="com.entity.Emp">
    select * from emp where deptno=#{deptno}
</select>

//一对多的第二种方式
List<Dept> selectDept_More();

//Test
public void selectDept_More(){
    SqlSession session = bd.getSession();
    EmpMapper mapper = session.getMapper(EmpMapper.class);
    List<Dept> depts = mapper.selectDept_More();
    for (Dept dept:depts) {
        System.out.println(dept);
    }
}
```





# 3.调用存储过程

如果调⽤存储过程：有⼊参有出参——默认为⼊，mode=OUT为出

```xml
 #{department, mode=OUT, jdbcType=CURSOR, javaType=ResultSet,
 resultMap=departmentResultMap}
```

**案例**

```xml
//映射文件
<!--调用存储过程-->
<resultMap id="empResult" type="com.entity.Emp">
    <id property="empno" column="empno"/>
</resultMap>
<select id="selectEmpProcedure" parameterType="HashMap" statementType="CALLABLE">
    call GETEMP(#{empinfos,mode=OUT,jdbcType=CURSOR,javaType=ResultSet,resultMap=empResult})
</select>


//java类
//调用存储过程
void selectEmpProcedure(HashMap map);

//test类
//调用存储过程
public void selectEmpProcedure(){
    SqlSession session = bd.getSession();
    EmpMapper mapper = session.getMapper(EmpMapper.class);
    HashMap hashMap = new HashMap();
    hashMap.put("empinfos", null);
    mapper.selectEmpProcedure(hashMap);
    System.out.println(hashMap);
}
```

