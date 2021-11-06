Spring中的AOP代理还是离不开Spring的IOC容器，代理的生成，管理及其依赖关系都是由IOC容器负责，Spring默认使用JDK动态代理，所以我们先来看一下代理。





# 1.代理

## 1.1 定义

给某一个对象提供一个代理，并由代理对象控制对原对象的引用，代理分为**静态代理**和**动态代理**。



## 1.2 静态代理

静态代理比较简单，是由程序员编写的代理类，并在程序运行前就编译好的，而不是由程序动态产生代理类，这就是所谓的静态。代理模式可以通过聚合和继承两种方式实现，聚合实现方式中代理类聚合了被代理类，且代理类及被代理类都实现了同一个接口，可实现灵活多变。继承式的实现方式则不够灵活，一般而言我们更倾向于使用聚合实现方式来实现代理，图示就是聚合实现方式。

![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580549026.png)

静态代理：每个类都得有一个代理类，静态代理必须有接口，然后自己的实现类和代理类需要实现同一个接口

```java
public interface PersonService {
    public void findGirlFriend();
}

//目标类
public class PersonServiceImpl implements PersonService {
    @Override
    public void findGirlFriend() {
        System.out.println("加好友");
        System.out.println("聊天");
    }
}

//代理类
public class PersonServiceProxy implements PersonService {
    PersonService personService = new PersonServiceImpl();
    @Override
    public void findGirlFriend() {
        System.out.println("找个姑娘");
        System.out.println("要个微信");
        personService.findGirlFriend();
        System.out.println("再找一个");
    }
}

public class TestMain {
    public static void main1(String[] args) {
        PersonServiceProxy personServiceProxy = new PersonServiceProxy();
        personServiceProxy.findGirlFriend();
    }
}
```

**静态代理的优缺点**：

+ 优点：可以做到在不修改⽬标对象的功能前提下对⽬标功能扩展
+ 缺点：因为代理对象需要与⽬标对象实现⼀样的接⼝，所以会有很多代理类，类太多。同时， ⼀旦接⼝增加⽅法，⽬标对象与代理对象都要维护。

**如何解决静态代理中的缺点呢? **

答案：可以使⽤动态代理⽅式





## 1.3 动态代理

动态代理中，代理类并不是在Java代码中实现，⽽是在运⾏时期⽣成。相⽐静态代理，动态代理可以很⽅便的对委托类的⽅法进⾏统⼀处理，如添加⽅法调⽤次数、添加⽇志功能等等。动态代理分为**jdk动态代理**（基于接⼝）和**cglib动态代理**（基于类的，是可以接⼝，⽗类代理） 。

动态代理的代理类看不⻅，是⾃动⽣成的。

动态代理的原理大概是拦截，执⾏过程中将代码拦截，然后插⼊共性代码。

**动态代理有以下特点**：

1. 代理对象：必须实现接⼝ 
2. 代理对象的⽣成，是利⽤JDK的API，动态的在内存中构建代理对象（需要我们指定创建代理对象/⽬标对象实现的接⼝的类型）



### 1.3.1 JDK动态代理：AOP的代理基于JDK，基于接⼝

**代理步骤：**

1. 定义一个事件管理器类实现invocationHandle接口,并重写invoke（代理类，被代理的方法，方法的参数列表）方法。
2. 实现被代理类及其实现的接口
3. 调用Proxy.newProxyInstance（类加载器，类实现的接口，事务处理器对象），生成一个代理实例。 
4. 通过该代理实例调用方法。 

**相关的类和接⼝：** 

1. java.lang.reﬂect.Proxy：这是 Java 动态代理机制的主类，它提供了⼀组静态⽅法来为⼀组 接⼝动态地⽣成代理类及其对象 

2. java.lang.reﬂect.InvocationHandler：这是调⽤处理器接⼝，它⾃定义了⼀个invoke⽅法， ⽤于集中处理在动态代理类对象上的⽅法调⽤。通常在该⽅法中实现对委托类的代理访问。即 ⽤来指明产⽣的这个代理对象要做什么事情。 

3. 创建JDK动态代理实现需要使⽤Proxy的newProxyInstance⽅法，该⽅法⽤来⽣成动态代理对象，需要接收三个参数，完整的写法是：

   ```java
   static Object newProxyInstance(ClassLoader loader, Class<?>[] interfaces,InvocationHandler h ) 
   ```

   注意该⽅法在Proxy类中是静态⽅法，且接收的三个参数依次为：

   + ClassLoader loader：类加载器，定义了由哪个ClassLoader对象来对⽣成的代理对象进⾏加载 
   + Class<?>[] interfaces：代理类要实现的全部接⼝（需要代理的接⼝）
   + InvocationHandler h：事件处理，执⾏⽬标对象的⽅法时，会触发事件处理器的⽅法，会把当前执⽬标对象的⽅法作为参数传⼊；

**代码示例**

```java
public class JDKProxy implements InvocationHandler {
    private Object target;//代理目标：根据传入的目标动态生成代理类。
    public Object bind(Object target){
        this.target = target;
        return Proxy.newProxyInstance(target.getClass().getClassLoader(),target.getClass().getInterfaces(),this);
    }

//动态代理的核心：将代码执行过程进行拦截，拦截以后可以在执行前添加代码，也可以在执行后添加代码
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("开启事务");
        Object obj = null;
        try{
            obj = method.invoke(target, args);
        }catch (Exception e){
            System.out.println("回滚事务");
        }
        System.out.println("提交事务");
        System.out.println("关闭事务");
        return obj;
    }
}
```

```java
public class TestMain {
    public static void main1(String[] args) {
        JDKProxy jdkProxy=new JDKProxy();
        PersonService personService= (PersonService) jdkProxy.bind(new PersonServiceImpl());
        personService.updatePerson();
    }
}
```



### 1.3.2 cglib动态代理：⼦类代理

上⾯的静态代理和jdk动态代理模式都是要求⽬标对象是实现⼀个接⼝的⽬标对象，但是有时候⽬标对象只是⼀个单独的对象，并没有实现任何的接⼝，这个时候就可以使⽤以⽬标对象⼦类的⽅式类实现代理，这种⽅法就叫做：Cglib代理 

**Cglib（Code Generation Library）代理**，也叫作⼦类代理，它是在内存中构建⼀个⼦类对象从⽽实现对⽬标对象功能的扩展，是基于asm框架，实现了⽆反射机制进⾏代理，利⽤空间来换取了时间，代理效率⾼于jdk。 

Cglib是⼀个强⼤的⾼性能的代码⽣成包，它可以在运⾏期扩展java类与实现java接⼝。它⼴泛的被许多AOP的框架使⽤，例如Spring AOP和synaop，为他们提供⽅法的interception(拦截) 。

**Cglib⼦类代理实现⽅法：**

1. 需要引⼊cglib的jar⽂件，但是Spring的核⼼包中已经包括了Cglib功能，所以直接引⼊ spring-core-3.2.5.jar即可；
2. 引⼊功能包后，就可以在内存中动态构建⼦类 ；
3. 代理的类不能为ﬁnal，否则报错；
4. ⽬标对象的⽅法如果为ﬁnal/static，那么就不会被拦截，即不会执⾏⽬标对象额外的业务⽅法。

**Cglib的核⼼类：**

1. Enhancer – 主要的增强类

2. Enhancer是CGLIB中常⽤的⼀个类，和Proxy类差不多。和Proxy不同的是，Enhancer既能够代理普通的class，也能够代理接⼝；Enhancer创建⼀个被代理对象的⼦类并且拦截所有的⽅法调⽤（包括从Object中继承的toString和hashCode⽅法），不能够拦截ﬁnal⽅法和操作 ﬁnal类

3. MethodInterceptor – 主要的⽅法拦截类，它是Callback接⼝的⼦接⼝，需要⽤户实现。是通⽤的回调（callback）类型，它经常被AOP⽤来实现拦截（intercept）⽅法的调⽤，这 个接⼝只定义了⼀个⽅法。 

   ```java
   public Object intercept(Object proxy, Method method, Object[] params,
                            MethodProxy methodProxy)   
   ```

   ⽅法参数解释： 

   + proxy：被代理的对象； 
   + method：执⾏的⽅法； 
   + params：⽅法中的参数； 
   + methodProxy：代理的⽅法 

   由于性能的原因，对原始⽅法的调⽤我们使⽤CGLIB的net.sf.cglib.proxy.MethodProxy对象，⽽不是反射中⼀般使⽤java.lang.reﬂect.Method对象。

**代码案例**

```java
public class CglibProxy implements MethodInterceptor {
    private Object target;//代理目标：根据传入的目标动态生成代理类。
    public Object bind(Object target){
         this.target=target;
         //返回代理类
         Enhancer enhancer =new Enhancer();
         enhancer.setCallback(this);
         enhancer.setSuperclass(target.getClass());
         return enhancer.create();
        }
    @Override
    public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable { 
        System.out.println("开启事物");
        Object obj=null;
        try {
            //method：代理目标的执行方法
            obj= methodProxy.invokeSuper(target,objects);
        }catch (Exception e){
            System.out.println("事物回滚");
        }
        System.out.println("事物提交");
        System.out.println("事物关闭");
        return obj;
        

CglibProxy cglibProxy=new CglibProxy();
//生成的代理是目标的类的子类
PersonServiceImpl personService2 = (PersonServiceImpl) cglibProxy.bind(new PersonServiceImpl()); 
personService2.updatePerson(); 
```







# 2.SpringAOP

## 2.1 什么是AOP？

AOP的全称是Aspect-Oriented Programming，即<font style="color:red">⾯向切⾯编程（也称⾯向⽅⾯编程）</font>。它<font style="color:red">是⾯向对象编程（OOP）的⼀种补充</font>，⽬前已成为⼀种⽐较成熟的编程⽅式。

AOP：通⽤代码提供。通常都会进⾏<font style="color:red">事务处理</font>、<font style="color:red">⽇志记录</font>等操作。

AOP：对JDK动态和cglib的动态代理的⼀层封装 。

在传统的业务处理代码中，通常都会进⾏事务处理、⽇志记录等操作。虽然使⽤ OOP可以通过组合或者继承的⽅式来达到代码的重⽤，但如果要实现某个功能（如⽇志记录），同样的代码仍然会分散到各个⽅法中。这样，如果想要关闭某个功能，或者对其进⾏修改，就必须要修改所有的相关⽅法。这不但增加了开发⼈员的⼯作量，⽽且提⾼了代码的出错率。

为了解决这⼀问题，AOP思想随之产⽣。<font style="color:red">AOP采取横向抽取机制，将分散在各个⽅法中的重复代码提取出来，然后在程序编译或运⾏时，再将这些提取出来的代码应⽤到需要执⾏的地⽅</font>。这种采⽤横向抽取机制的⽅式，采⽤传统的OOP思想显然是⽆法办到的，因为OOP只能实现⽗⼦关系的纵向的重⽤。虽然AOP是⼀种新的编程思想，但却不是OOP的替代品，它只是OOP的延伸和补充。

![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580550673.png)

**AOP的使⽤，使开发⼈员在编写业务逻辑时可以专⼼于核⼼业务，⽽不⽤过多的关注于其他业务逻辑的实现，这不但提⾼了开发效率，⽽且增强了代码的可维护性。**



## 2.2 AOP中相关术语

AOP解决了⼀系列类共享相同的增强处理。在AOP中把增强的处理成为advice，把与业务⽆关的增强处理提取放⼊到⼀个类中，这个类成为Aspect切⾯类，需要引⼊增强处理的⽅法的地⽅称为切⼊点。

![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580550775.png)

1. 切⾯ (Aspect)：切⾯是系统中抽象出来的的某⼀个功能模块。——没有实现，概念 
2. 通知 (Advice)：通知是切⾯的具体实现，是放置“切⾯代码”的类。
3. 连接点 (Joinpoint)：程序执⾏过程中某个特定的点，如调⽤某⽅法或者处理异常时。在 SpringAOP中，连接点总是代表某个⽅法的执⾏。即那些需要处理的位置 。
4. 切⼊点 (Pointcut)：多个连接点组成⼀个切⼊点，可以使⽤切⼊点表达式来表示。
5. ⽬标对象 (Target Object)：包含⼀个连接点的对象，即被拦截的对象。 
6. AOP代理 (AOP Proxy)：AOP框架产⽣的对象，是通知和⽬标对象的结合体。代理对象是 对象动态⽣成的。
7. 织⼊ (Weaving)：将切⼊点和通知结合的过程称为织⼊，<font style="color:red">织⼊之后产⽣代理</font>。



## 2.3 基于AspectJ实现AOP

AspectJ是⼀个基于Java语⾔的AOP框架，它提供了强⼤的AOP功能。Spring 2.0以 后，Spring AOP引⼊了对AspectJ的⽀持，并允许直接使⽤AspectJ进⾏编程，⽽ Spring⾃身的AOP API也尽量与AspectJ保持⼀致。新版本的Spring框架，也建议使⽤ AspectJ来开发AOP。

**实现AOP有三种⽅式： **

1. 基于XML的声明式AspectJ 
2. 基于注解的声明式AspectJ
3. 基于JAVA类的

**AOP实现： **

1. 基于JDK的动态代理 
2. 基于Cglib的动态代理



### 2.3.1 基于XML的声明式AspectJ 

1. pom.xml中添加依赖

   ```xml
   <dependency>
     <groupId>org.springframework</groupId>
     <artifactId>spring-aspects</artifactId>
     <version>4.1.3.RELEASE</version>
   </dependency>
   ```

   

2. 编写切面类

   ```java
   //通知类:事物处理  以及日志处理
   public class CommonAdvice {
       //通知
       public void before(){
           System.out.println("前置通知");
       }
   
       //最终通知：有没有异常都执行
       public void after(){
           System.out.println("最终通知");
       }
   
       //异常通知，必须抛出异常方可执行
       public void afterThrow(Exception e){
           System.out.println(e.getMessage());
           System.out.println("异常通知");
       }
   
       //后置通知，只有正常执行完成后方可执行
       public void afterReturn(JoinPoint joinPoint){
           //获取参数
           System.out.println(joinPoint.getArgs());
           System.out.println("后置通知");
       }
   
       //环绕通知
       //必须调用目标方法，否则目标方法不执行，并且环绕通知必须有返回值，否则目标方法执行的返回值就丢失
       public Object arround(ProceedingJoinPoint joinPoint) throws Throwable {
           System.out.println("环绕开始");
           Object obj = null;
           try {
               obj = joinPoint.proceed(joinPoint.getArgs());
   
           } catch (Throwable throwable) {
               throwable.printStackTrace();
               System.out.println("回滚...");
               throw throwable;
           }
           System.out.println("环绕结束");
           return obj;
       }
   
   }
   ```

   

3. Spring配置文件

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans
           xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xmlns:context="http://www.springframework.org/schema/context"
           xmlns:aop="http://www.springframework.org/schema/aop"
           xsi:schemaLocation="http://www.springframework.org/schema/beans
      http://www.springframework.org/schema/beans/spring-beans-3.1.xsd
      http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
      http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">
       <context:component-scan base-package="com.aop"/>
       <aop:aspectj-autoproxy/>
   
       <bean id="personImpl" class="com.aop.PersonServiceImpl"></bean>
       <bean id="advice" class="com.aop.CommonAdvice"/>
       <aop:config>
           <!--声明切入点 expression：规则 *:任意返回值 com.service.*任意类.*任意方法 (..忽略接参)-->
           <aop:pointcut id="pt" expression="execution(* com.aop.*.*(..))"/>
           <!--aspect:切面的意思  ref="advice"切面和通知类-->
           <aop:aspect id="as" ref="advice">
               <aop:before method="before" pointcut-ref="pt"/>
               <aop:after-returning method="afterReturn" pointcut-ref="pt"/>
               <aop:after method="after" pointcut-ref="pt"/>
               <!--异常通知  mehotd是方法名  throwing是异常名 -->
               <aop:after-throwing method="afterThrow" pointcut-ref="pt" throwing="e"/>
               <aop:around method="arround" pointcut-ref="pt"/>
           </aop:aspect>
       </aop:config>
   
   </beans>
   ```

   + 配置切面

     在Spring的配置⽂件中，配置切⾯使⽤的是aop:aspect元素，该元素会将⼀个已定义好 的Spring Bean转换成切⾯Bean，所以要在配置⽂件中先定义⼀个普通的Spring Bean。 配置aop:aspect元素时，通常会指定id和ref两个属性。

     ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580557465.png)

   + 配置切入点

     当aop:pointcut元素作为aop:conﬁg元素的⼦元素定义时，表示该切⼊点是全局切⼊ 点，它可被多个切⾯所共享；当aop:pointcut元素作为aop:aspect元素的⼦元素时，表 示该切⼊点只对当前切⾯有效。  在定义aop:pointcut元素时，通常会指定id和expression两个属性。

     ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1580557496.png)

   + 切入点表达式

     execution(* com.aaa.service.impl. . (..))是定义的切⼊点表达式，该切⼊点表达式的 意思是匹配com.aaa.service.impl包中任意类的任意⽅法的执⾏。 

     1. execution是切⼊点指示符 

     2. 通配符（ ）⽤于匹配任何⽅法，类名或者参数类型。 双点号(..) ⽤于表示0 个或者多个 参数类型 

     3. ⽅法返回类型：如v oid ，i n t ，S t rin g 等。使⽤ 表示所有类型 

     4. ⽅法名称：可以是全名。也可以部分匹配，如get* 表示所有以get开头的⽅法 

     5. ⽅法声明抛出的异常：throws java.lang.IOException

   + 配置通知

     使⽤aop:aspect的⼦元素可以配置5种常⽤通知，这5个⼦元素不⽀持使⽤⼦元素， 但在使⽤时可以指定⼀些属性，其常⽤属性及其描述如下：

     ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/20200201195058.png)

     

### 2.3.2 基于注解的声明式AspectJ

1. ⽬标类加上注解 @Service @Component("personImpl") 

   ```java
   @Service
   @Component("personImpl")
   public class PersonServiceImpl implements PersonService {
   
       @Override
       public void findGirlFriend() {
           System.out.println("加好友");
           System.out.println("聊天");
       }
   
       @Override
       public void updatePerson() {
           System.out.println("修改....");
       }
   }
   ```

   

2. 通知类加上注解@Component@Aspect ，⽅法上边直接写通知

   ```java
   @Component
   @Aspect
   //通知类:事物处理  以及日志处理
   public class CommonAdvice {
       //通知
       @Before("execution(* com.aop.*.*(..))")
       public void before(){
           System.out.println("前置通知");
       }
   
       //最终通知：有没有异常都执行
       @After("execution(* com.aop.*.*(..))")
       public void after(){
           System.out.println("最终通知");
       }
   
       //异常通知，必须抛出异常方可执行
       @AfterThrowing(value = "execution(* com.aop.*.*(..))",throwing = "e")
       public void afterThrow(Exception e){
           System.out.println(e.getMessage());
           System.out.println("异常通知");
       }
   
       //后置通知，只有正常执行完成后方可执行
       @AfterReturning("execution(* com.aop.*.*(..))")
       public void afterReturn(JoinPoint joinPoint){
           //获取参数
           System.out.println(joinPoint.getArgs());
           System.out.println("后置通知");
       }
   
       //环绕通知
       //必须调用目标方法，否则目标方法不执行，并且环绕通知必须有返回值，否则目标方法执行的返回值就丢失
       @Around("execution(* com.aop.*.*(..))")
       public Object arround(ProceedingJoinPoint joinPoint) throws Throwable {
           System.out.println("环绕开始");
           Object obj = null;
           try {
               obj = joinPoint.proceed(joinPoint.getArgs());
   
           } catch (Throwable throwable) {
               throwable.printStackTrace();
               System.out.println("回滚...");
               throw throwable;
           }
           System.out.println("环绕结束");
           return obj;
       }
   
   }
   ```

   AspectJ框架为AOP的实现提供了⼀套注解，⽤以取代Spring配置⽂件中为实现AOP 功能所配置的臃肿代码。AspectJ的注解及其描述如下所示：

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/20200201195341.png)

3. 配置⽂件配置，包扫描的时候不要忘记了applicationContext.xml的log增强类

   ```xml
   <context:component-scan base-package="com.aop"/>
   <aop:aspectj-autoproxy/>
   ```

   

4. 测试类

   ```java
   public class TestMain {
       public static void main(String[] args) {
           ClassPathXmlApplicationContext classPathXmlApplicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
           PersonService personService = (PersonService) classPathXmlApplicationContext.getBean("personImpl");
           personService.findGirlFriend();
       }
   }
   ```

   

### 2.3.3 基于JAVA类的

基于Java的AOP和基于注解的很类似，就是将配置文件换到了Java类中，而且配置文件可以使用注解代替。

```java
//configClass.java
@Configuration//java配置类
@EnableAspectJAutoProxy//开启注解版的AOP功能
@ComponentScan("com.aop")//该注解默认会扫描该类所在的包下所有的配置类，相当于<context:component-scan base-package="com.aop"/>
public class configClass {

}
```

```java
//testMain.java
public class TestMain {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext(configClass.class);
        PersonService personService = (PersonService) applicationContext.getBean("personImpl");
        personService.findGirlFriend();
    }
}
```

