# 1.1 Spring框架：
##     一、Spring   
        Spring 是一个分层的 JavaSE/EEfull-stack(一站式) 轻量级 开源框架。 
    
### 1、 搭建：
#### 一：导入jar包

		 ① spring-Beans  
		②spring-core ③spring-context
		④spring-expression 
		⑤ springsource.org,apache.commons.logging(spring日志包)`
#### 二：创建一个对象
  
  
```java
package SpringUser;
public class User {
       private String name;
       private Integer age;
       public String getName() {
             return name;
       }
       public void setName(String name) {
             this.name = name;
       }
       public Integer getAge() {
             return age;
       }
       public void setAge(Integer age) {
            this.age = age;

       }

}


``` 

#### 三：书写配置注册对象到容器
                位置任意，建议放在src下，配置文件名任意，但是建议用applicationContext.xml。
！[images text](https://github.com/cbbgs/ssm/images/spring%E7%BA%A6%E6%9D%9F1.png)



             
                
                在xml中书写配置：
```xml
        <!-- 将User对象交给Spring容器管理 -->
       <bean name = "user" class = "springUser.User"></bean>


```
 
 

#### 四：代码测试
       在src下创建一个测试类      
```java
public class a_hello {
       @Test
        public void fin() {
              //创建容器对象
             ApplicationContext <u>ac</u> = new 
ClassPathXmlApplicationContext("applicationContext.xml"); 
             //向容器要对象
             User u = (User) ac.getBean("user");
             //打印User对象
             System.out.println(u);

        }
}


```
### 2 、Spring概念
#### 一、IOC 
    inverse of Control 反转控制
    将我创建对象的方式反转了；以前对象的创建以及依赖关系都是我们自己维护的，使用Spring之后，对象的创建以及依赖关系的注入有Spring完成。反转控制就是对象的创建反转给了程序；
    
    
####   二、DI
       Dependency Injection  依赖注入
       实现IOC思想需要DI做支持；
       注入方式：
            set注入
            构造注入
            字段注入（不建议）
       注入类型：
            值类注入      8大数据类型
            引用类注入   将依赖对像注入
####     三、ApplicationContext&BeanFactory
    ①、BeanFactory：
                Spring的最原始接口，针对原始接口的实现类的功能较为单一。
                BeanFactory接口实现类的容器特点是每次在获得对象是才会创建
    ②、ApplicationContext：
                每次容器启动时就会就会创建容器中配置的所有对象
                从类路径下加载配置文件：ClassPathXmlApplicationContext
     ③、结论：web开发中可以使用ApplicationContext,资源匮乏的环境可以使用BeanFactory
                
###   3、配置讲解
####   Bean元素
    使用该元素需要Spring容器的对象
    name属性：给被管理的对象起名字，获得对象根据该名称获得；名称可重复。能使用特殊字符
    class属性：被管理对象的完整类名。
    id属性：与name属性一样。名称不可重复。不能使用特殊字符。
####  Spring创建对象的方式
#####     ①、空參构造方式
    
```xml
          <!-- 将User对象交给Spring容器管理 -->
       <bean name = "user"  class = "spring.User"></bean>
```
#####     ②、（了解）静态工厂
    
#####     ③、(了解)实例工厂
####    Bean元素进阶
  #####      ①、scope属性：
    <bean name = "user"  class = "spring.User" ></bean>
            取值：single（默认值）：单例对象。被标志为单例对象的Spring容器只会有一个实例。  <bean name = "user"  class = "spring.User"  scope = "singleton"></bean>


```java
@Test
       public void fun() {             
             ApplicationContext <u>ac</u> = new 
ClassPathXmlApplicationContext("applicationContext.xml");             
             User u =(User) ac.getBean("user");
             User u1=(User) ac.getBean("user");
             User u2 =(User) ac.getBean("user");
             User <u>u3</u> =(User) ac.getBean("user");
             System.out.println(u1==u2);    //执行的结果为  true.因为都是一个实例对象        
             System.out.println(u);
       }


```
        
                    prototype:多例原型。被表示为多例的对象，每次再获得才会创建，每次创建的多是新的对象 
     <bean name = "user"  class = "spring.User"  scope = "prototype"></bean>
      上例中  System.out.println(u1==u2); 这种情况则为false


                    request（了解）：web环境下，对象与request的生命周期一致
                    session（了解）web环境下，对象与session的生命周期一致

            
   #####    ②、生命周期属性：
                    配置一个方法作为生命周期的初始化方法。Spring会在创建之后立即调用。init-method
                    
                     配置一个方法作为生命周期的销毁方法。Spring会在销毁容器中所有对象之前调用。destr-metho
                
                    <!-- 将User对象交给Spring容器管理 -->

       
```xml
<!-- 将User对象交给Spring容器管理 -->
       <bean name = "user"  class = "spring.User" init-method = "init" 
destroy-method = "destory" ></bean>


```


```Java
public void init() {//在user中创建两指定方法
             System.out.println("init");
       }
       public void destory() {
             System.out.println("destory");
       }


```
#### Spring的分模块配置
    
```xml
<!-- 导入其他Spring的配置文件 -->
<import resource=" /spring001/src/applicationContext.xml"/>

```
### 4、Spring的属性注入
####     一、set方法注入（最重要）
    
```
<!-- set方式注入 -->
       <bean name = "user"  class = "spring.User" >
       <!-- 为USer对象中名为name的属性注入Tom作为值 -->
             <property name="nameg" value = "tom"></property>
             <property name="age" value = "18"></property>              
       </bean>


```
    
```xml


<!-- 构造方式注入 -->

       <bean name = "user"  class = "spring.User" >

       <!-- 为USer对象中名为name的属性注入Tom作为值 -->

             <property name="nameg" value = "tom"></property>

             <property name="age" value = "18"></property>

             <property name="car" ref = "car"></property>       
       
       </bean>

       <!-- 将car对象配置到容器中 -->

       <bean name = "Car"  class = "spring.Car" >

       <!-- 为USer对象中名为name的属性注入Tom作为值 -->

             <property name="name" value = "兰博基里"></property>

             <property name="color" value = "red"></property>

       </bean>

       


```
####     二、构造方法注入
    
```
<bean name = "car" class = "spring.Car">
       <property name="name" value = "兰博"></property>
       <property name="color" value = "华N"></property>      
       </bean>
       <!-- 构造函数注入 -->
       <bean name  = "user" class = "spring.User">
       <constructor-arg name = " name" value = "tom"></constructor-arg>
       <constructor-arg name = "car" ref = "car"></constructor-arg>


```
####     三、p名称空间注入(了解)
####     四、spl注入（了解）
####     五、复杂类型注入


### Spring注解开发代替xml配置

####     一、为主配置文件引入新的命名空间（导入context约束）
     配置context 和配置bean一样，① spring-Beans  ②spring-core ③spring-context ④spring-expression ⑤ springsource.org,apache.commons.logging(spring日志包)`⑥aop包
####    二、 开启使用注解代理配置文件
    
```  xml
     <!-- 指定扫描包下的所有类中的注释 -->
     <context:component-scan base-package="spring002">   
</context:component-scan>

```
#### 三、在类中完成配置注解
    
```Java
@Component("user")        //注解
//相当于<bean name = "user" <u>calss</u> = "spring002"></bean>
public class User {
       private String name;
       private Integer age;
       public String getName() {
             return name;
       }
       public void setName(String name) {
             this.name = name;
       }
       public Integer getAge() {
             return age;
       }
       public void setAge(Integer age) {
             this.age = age;
       }
       
}


```
        将对像注册到注解
        @Service("user")//service层
        @Controller("user")//web
        @Repository("user")//<u>dao</u>层
        @Scope(scopeName = "prototype")//指定对象为作用范围为单例
       注意：
      1、注入加在字段上时，通过Field赋值,破坏了封装行
        @Value（“Tom”）//给对象注入
         private String  name；
    2、 注入加在这里时，通过set方法赋值    
     @Value（“Tom”）//给对象注入 推荐使用
     public void seyName(String name){
          this.name = name;
     }
     
     
     
```java
@Value("同")
       public void setName(String name) {
             this.name = name;
       }
@Value(value = "12")
       public void setAge(Integer age) {

             this.age = age;
       }


```
    @Autowired//自动装配
    //如果匹配到多个类型一致的对象时，将无法选择哪一个
    private Car car;
    
    @Resource(name = "car2")//手动注入，指定注入哪一个名称的对象
     

    （当有多个类型对象时，要手动配置）
    推荐用这种
    
```xml
 <!-- 指定扫描包下的所有类中的注释 -->
     <context:component-scan base-package="spring002"></context:component-scan>
     <!-- 第二个相同的对象的配置注入   与@rResource(name = "car2")  指定注入那个对象-->
     <bean name = " " class = "spring002.Car">
       <property name="name" value = "car1"></property>
       <property name="color" value = "blue"></property>
     </bean>
```
      
    @Posconstruct   //生命周期初始化方法
    @PreDestory   //生命周期销毁方法
    
###    STS插件
    Spring装好插件的ecplise
 ### Spring与junit整合测试
####     一、导包
    ① spring-Beans  ②spring-core ③spring-context ④spring-expression ⑤ springsource.org,apache.commons.logging(spring日志包)`⑥aop包⑦test


#### 二、  注解      
    @RunWith（SpringJUnit4ClassRunner.calss）//帮我们把容器创建好
    @ContextConfiguration(calsspath:applicationContext.xml)//指定创建容器时使用哪个配置文件
    
    @RunWith(SpringJUnit4ClassRunner.class)//帮我们把容器创建好

    @ContextConfiguration("classpath:enen.xml")//指定创建容器时使用哪个配置文件
    
```Java
public class Test1 {
       @Resource(name = "user")//将User对象注入到u变量中
       private User u;
       @Test
       public void fun() {      
             System.out.println(u);            
       }
       将这两句代替       

            ApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext.xml");  
             User u =(User) ac.getBean("user2");


```
### Spring中的aop
    aop思想：横向重复，纵向抽取。
    Spring中的aop：Spring能够为容器管理的对象生成动态代理
    Spring实现aop的原理：①动态代理：被代理对象必须实现接口才能产生代理对象 ② cglib代理：第三方代理技术，cglib代理，可以对任何类产生代理，代理的原理是对目标对象进行继承代理。但是被finall修饰的类无法代理。
    aop名词解释：
                Joinpoint(连接点):目标对象中所有可以增强的代码                           Pointcut(切入点):目标对象，一增强的代码
             Advice(通知/增强):增强的代码
             target（目标对象）：被代理对象
             Weaving(织入):将通知织入切入点
             Proxy（代理）:将通知织入到目标对象之后，形成代理对象
             aspect（切面）：切入点＋通知
   ####   步骤
#####         一、导包：
        ① spring-Beans  ②spring-core ③spring-context ④spring-expressionspringsource.org,apache.commons.logging(spring日志包)`⑥aop包⑦test⑧aspects   ⑨weaver 和aopalliance文件的包
#####         二、准备目标对象
        
```Java
//Userservice为自己编写的的接口
public class UserServiceImpl implements UserService {
       @Override
       public void save() {
             // TODO Auto-generated method stub
             System.out.println("save");
       }
       @Override
       public void delete() {

             // TODO Auto-generated method stub
             System.out.println("delete");
       }
       @Override
       public void update() {
             // TODO Auto-generated method stub
             System.out.println("update");
       }
       @Override
       public void find() {
             // TODO Auto-generated method stub
             System.out.println("find");

       }



}



```
        
        
#####         三、准备通知
```javq
//通知类
public class MyAdvcice {
          //前置通知：目标方法之前通知      
       public void before() {
             System.out.println("before" );
       }
             //后置通知：（如果出现异常不会调用）在目标方法运行之后调用
       public void afterReturning() {
             System.out.println("afterReturning" );
       }
       /*

        * //环绕通知：在目标方法之前和之后都运行 public void 
around(ProceedingJionPoint <u>pjp</u>) {

        * System.out.println("around" ); }

        */
             //异常拦截通知：如果出现异常，就会调用

       public void afterException() {
             System.out.println("afterException" );             

       }
             //后置通知：（无论出现异常都会调用）在目标方法之后调用
       public void after() {
             System.out.println("after" );
       }

}


```
    
#####     四、配置织入，将通知织入到对象中
    
    
```xml

<!-- 准备工作: 导入aop(约束)命名空间 -->
<!-- 1.配置目标对象 -->
	<bean name="userService" class="cn.itcast.service.UserServiceImpl" ></bean>
<!-- 2.配置通知对象 -->
	<bean name="myAdvice" class="cn.itcast.d_springaop.MyAdvice" ></bean>
<!-- 3.配置将通知织入目标对象 -->
	<aop:config>
		<!-- 配置切入点 
			public void cn.itcast.service.UserServiceImpl.save() 
			void cn.itcast.service.UserServiceImpl.save()
			* cn.itcast.service.UserServiceImpl.save()
			* cn.itcast.service.UserServiceImpl.*()
			
			* cn.itcast.service.*ServiceImpl.*(..)
			* cn.itcast.service..*ServiceImpl.*(..)
		-->
		<aop:pointcut expression="execution(* cn.itcast.service.*ServiceImpl.*(..))" id="pc"/>
		<aop:aspect ref="myAdvice" >
			<!-- 指定名为before方法作为前置通知 -->
			<aop:before method="before" pointcut-ref="pc" />
			<!-- 后置 -->
			<aop:after-returning method="afterReturning" pointcut-ref="pc" />
			<!-- 环绕通知 -->
			<aop:around method="around" pointcut-ref="pc" />
			<!-- 异常拦截通知 -->
			<aop:after-throwing method="afterException" pointcut-ref="pc"/>
			<!-- 后置 -->
			<aop:after method="after" pointcut-ref="pc"/>
		</aop:aspect>
	</aop:config>
</beans>
```

### Spring整合JDBC
####    Spring中提供了一个可以操作数据库的对象、对象封装了jbdc技术
    JDBCTemplateIDBC模板对象
    
####   一、导包
     spring-Beans  ②spring-core ③spring-context ④spring-expression ⑤ springsource.org,apache.commons.logging(spring日志包)⑥  aop包     ⑦test ⑧ c3p0链接池 ⑨JDBC驱动⑩Spring-jdbc  、spring-tx
     
####      二、准备数据库测试
    
```Java   
传统连接方式

//准备连接池

             ComboPooledDataSource dataSource  = new ComboPooledDataSource();

             dataSource.setDriverClass("com.mysql.jdbc.Driver");

             dataSource.setJdbcUrl("jdbc:mysql:///haha");

             dataSource.setUser("root");

             dataSource.setPassword("123");

             //创建<u>jdbc</u> 模板

             JdbcTemplate jt = new JdbcTemplate();

             jt.setDataSource(dataSource);

             //书写<u>sql</u>并执行

             String sql = "insert into t_user values(null,'chebnbin')";

             jt.update(sql);

             


```
    UserImplel extends JDBCDaoSupport:根据连接池创建JDBC模板
            不需要手动准备JDBC模板，从父类方法获得即可
           
### Spring中的aop事务
    事务：“原子性 :强调事务的不可分割. 一致性 :事务的执行的前后数据的完整性保持一致. 隔离性 :一个事务执行的过程中,不应该受到其他事务的干扰 持久性 :事务一旦结束,数据就持久到数据库 ”
####      Spring封装了事务管理代码
        打开事务
        提交事务
        回滚事务
        Spring提供了一个接口： PlatformTransactionManag
        在Spring中玩事务管理，最为核心的对象就是TranscanctionManager
        spring 管理事务的事务属性介绍：事务的隔离级别，是否只读，事务事务传播行为（
        PROPAGATION_REQUIRED  支持当前事务，如果不存在 就新建一个(默认) 
        PROPAGATION_SUPPORTS  支持当前事务，如果不存在，就不使用事务 PROPAGATION_MANDATORY 支持当前事务，如果不存在，抛出异常  
        * 保证没有在同一个事务中 
        * PROPAGATION_REQUIRES_NEW 如果有事务存在，挂起当前事务，创建一个新的事务
        PROPAGATION_NOT_SUPPORTED 以非事务方式运行，如果有事务存在，挂起当前事务 ）
        
        
        
####         Spring管理事务方式
#####     编码式（了解）
#####  xml配置aop管理
    导包 +  导入约束（tx）
    beans：最基本
    context：读取propertise配置
    aop：aop配置
    tx:配置事务
    
```xml
<!-- 事务模板对象 -->
<bean name="transactionTemplate" class="org.springframework.transaction.support.TransactionTemplate" >
	<property name="transactionManager" ref="transactionManager" ></property>
</bean>

<!-- 配置事务通知 -->
<tx:advice id="txAdvice" transaction-manager="transactionManager" >
	<tx:attributes>
		<!-- 以方法为单位,指定方法应用什么事务属性
			isolation:隔离级别
			propagation:传播行为
			read-only:是否只读
		 -->
		<tx:method name="save*" isolation="REPEATABLE_READ" propagation="REQUIRED" read-only="false" />
		<tx:method name="persist*" isolation="REPEATABLE_READ" propagation="REQUIRED" read-only="false" />
		<tx:method name="update*" isolation="REPEATABLE_READ" propagation="REQUIRED" read-only="false" />
		<tx:method name="modify*" isolation="REPEATABLE_READ" propagation="REQUIRED" read-only="false" />
		<tx:method name="delete*" isolation="REPEATABLE_READ" propagation="REQUIRED" read-only="false" />
		<tx:method name="remove*" isolation="REPEATABLE_READ" propagation="REQUIRED" read-only="false" />
		<tx:method name="get*" isolation="REPEATABLE_READ" propagation="REQUIRED" read-only="true" />
		<tx:method name="find*" isolation="REPEATABLE_READ" propagation="REQUIRED" read-only="true" />
		<tx:method name="transfer" isolation="REPEATABLE_READ" propagation="REQUIRED" read-only="false" />
	</tx:attributes>
</tx:advice>


<!-- 配置织入 -->
<aop:config  >
	<!-- 配置切点表达式 -->
	<aop:pointcut expression="execution(* cn.itcast.service.*ServiceImpl.*(..))" id="txPc"/>
	<!-- 配置切面 : 通知+切点
		 	advice-ref:通知的名称
		 	pointcut-ref:切点的名称
	 -->
	<aop:advisor advice-ref="txAdvice" pointcut-ref="txPc" />
</aop:config>
```
#####  注解方式
    导包和约束一样
    
```xml

<!-- 开启使用注解管理aop事务 -->
<tx:annotation-driven/>

```
        
```Java
@Transactional(isolation=Isolation.REPEATABLE_READ,propagation=Propagation.REQUIRED,readOnly=false)
```
