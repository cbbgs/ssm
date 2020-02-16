# 2.1 Mybatis1（半自动框架）
    MyBatis 是支持普通 SQL 查询，存储过程和高级映射的优秀持久层框架。MyBatis 消除 了几乎所有的 JDBC 代码和参数的手工设置以及对结果集的检索。MyBatis 可以使用简单的 XML 或注解用于配置和原始映射，将接口和 Java 的 POJO（Plain Old Java Objects，普通的 Java 对象）映射成数据库中的记录
       sql与Java编码分离；sql是开发人员分离
## 步骤：
###     一、创建一个数据库对应的类
    
```Java
public class Employee {
       private String name;
       private Integer id;          
       public String getName() {
             return name;
       }
       public void setName(String name) {
             this.name = name;
       }
       public Integer getId() {
             return id;
       }
       public void setId(Integer id) {
             this.id = id;
       }
      
}

```
### 二、导包
    mybatis  connector
    
###   三、创建sqlSessionFactory的xml文件（mybatis-config.xml）
 
```xml
<?xml version="1.0" encoding="UTF-8"?>

 <!DOCTYPE configuration  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"  
"http://mybatis.org/dtd/mybatis-3-config.dtd"> 

 <configuration>  

       <environments default="development">   

             <environment id="development"> 

              <transactionManager type="JDBC"/>  

              <dataSource type="POOLED">      
<!--l连接自己的数据库-->
              <property name="driver" value="com.mysql.jdbc.Driver"/> 

                   <property name="url" 
value="jdbc:mysql://localhost:3306/haha"/>       

                    <property name="username" value="root"/>        

                     <property name="password" value="123"/>      

                  </dataSource> 

           </environment>  

      </environments>  

       <mappers> 

       <!-- 将写好的<u>sql</u>映射文件（EmployeeMapper.xml）注册到全局配置文件中 -->   

          <mapper resource="EmployeeMapper.xml"/>

       </mappers>

  </configuration>


```
### 四、根据三中的xml文件创建一个sqlSessionFactory对象
```Java
//1、根据<u>xml</u>配置文件（全局配置）。创建一个SqlSessionFactory对象
//所根据的xml文件的地址
   String resource = "mybatis-config.xml";
      InputStream inputStream = 
Resources.getResourceAsStream(resource);
             SqlSessionFactory sqlSessionFactory = new 
SqlSessionFactoryBuilder().build(inputStream);
```
### 五、从sqlSessionFactory对象获取SqlSession对象
```Java
//2、获取sqlsession实例，能直接执行以映射的sql语句
             SqlSession session = sqlSessionFactory.openSession();

             try {
             //获取映射的sql 传入的参数是唯一标识
             Employee <u>employe</u> = 
session.selectOne("mybatis.Employee.selectEmp", 1);
             } finally {
                   openSession.close();
             }
```





### 六、映射的sql文件（EmployMdapper.xml
）
```xml
<?xml version="1.0" encoding="UTF-8" ?> 

<!DOCTYPE mapper  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"  
"http://mybatis.org/dtd/mybatis-3-mapper.dtd"> 

<mapper namespace="/Mybatis_001/src/mybatis/EmployeeMapper">

<!-- 名称空间:指为接口的全类名

id：唯一标识

resultType：返回值类型

public Employee getEmplById(Integer id);

 -->

  <select id="SelectEmp" resultType="mybatis.Employee">

    select * from t_user where id = #{id}

      </select>

</mapper>
```
### 接口编程
    (mybatis提供接口可以与xml动态绑定)简化上述过程
    
#### 一、创建一个接口
    
```Java
public interface EmployeeMapper {
       public Employee getEmplById(Integer id);
}

```
####     二、配置sqlsessionfactroy和sqlsessison的xml文件
```xml
<?xml version="1.0" encoding="UTF-8"?>
 <!DOCTYPE configuration  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"  
"http://mybatis.org/dtd/mybatis-3-config.dtd"> 
 <configuration>  
       <environments default="development">   
             <environment id="development"> 
              <transactionManager type="JDBC"/>  
              <dataSource type="POOLED">      
              <property name="driver" value="com.mysql.jdbc.Driver"/> 
                   <property name="url" 
value="jdbc:mysql://localhost:3306/haha"/>       
                    <property name="username" value="root"/>        
                     <property name="password" value="123"/>      
                  </dataSource> 
           </environment>  
      </environments>  
       <mappers> 
       <!-- 将写好的<u>sql</u>映射文件（EmployeeMapper.xml）注册到全局配置文件中 -->   
          <mapper resource="EmployeeMapper.xml"/>
       </mappers>
  </configuration>
```
```xml
<?xml version="1.0" encoding="UTF-8" ?> 
<!DOCTYPE mapper  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"  
"http://mybatis.org/dtd/mybatis-3-mapper.dtd"> 
<mapper namespace="/Mybatis_001/src/mybatis/EmployeeMapper">
<!-- 名称空间:指为接口的全类名
id：唯一标识
resultType：返回值类型
public Employee getEmplById(Integer id);
 -->
<!-- selectEmp -->
  <select id="getEmplById" resultType="mybatis.Employee">
    select * from t_user where id = #{id}   
      </select>
</mapper>
```
####     四、创建SqlSessionFactroy和SqlSession的对象以及获取接口的实现类

```Java
       //为了方便将得到SqlSessionFactory对象封装成一个方法
       public SqlSessionFactory getSqlSessionFactory() throws IOException {
             String resource = "mybatis-config.xml";
             InputStream inputStream = 
Resources.getResourceAsStream(resource);
             return new SqlSessionFactoryBuilder().build(inputStream);        
       }
       @Test 
       public void fun() throws Exception {
             //1、得到sqlSessionFactory对象
             SqlSessionFactory sqlSessionFactory = getSqlSessionFactory();
             //获取sqlSession对象
             SqlSession  opSession = sqlSessionFactory.openSession();
             //获取接口的实现类
             try {  
             EmployeeMapper mapper = 
opSession.getMapper(EmployeeMapper.class);
             Employee <u>employeeMapper</u>  = mapper.getEmplById(1);
             } finally {
                    opSession.close();
             }
             }             
       }


```
        sqlsession代表和数据库的一次会话；用完必须关闭
        Sqlsession是非线程安全的。每次使用都要去获取新的对象；
        mapper接口没有实现类，但是mybatis会为这个接口生成一个代理对象（将接口与xml绑定）
     EmployeeMapper mapper = opSession.getMapper(EmployeeMapper.class);
      两个配置文；全局配置文件和sql映射文件（里面保存了每一个sql的映射信息）
      
      
##       Mybatis全局配置文件
###     dtd约束文件
###   属性
####      properties
    1、mybatis可以使用properties来引入外部properties配置文件（由.properties结尾的文件）
    url:网络路径或者磁盘路径
    resource：引入类路径下的资源
     <properties resource = "dbconfig.properties">       <       </properties>
     
     
 dbconfig.properties文件 
```

jdbc.driver = com.mysql.jdbc.Driver
jdbc.url =  jdbc:mysql://ocalhost>:3306/haha
jdbc.username = root
jdbc.passworld = 123


```

然后就可以将


```
<property name="driver" value="com.mysql.jdbc.Driver"/> 
                   <property name="url" 
value="jdbc:mysql://localhost:3306/haha"/>       
                    <property name="username" value="root"/>        
                     <property name="password" value="123"/>      
```
这段代码变成
   

```
       <property name="driver" value="${jdbc.driver}"/> 
                   <property name="url" value="${jdbc.url}"/>       

                    <property name="username" value="${jdbc.username}"/>        

                     <property name="password" value="1${jdbc.passworld}"/>  


```
#### settings
    settings 包含很多设置项
    setting设置每一项设置
            name：设置的名称
            value：设置项取值
            
      
####       typeAliases（别名处理器）  
         typeAliases别名处理器，可以为Java类型起别名
         typeAlias：为某个Java类型起别名
                 type：指定起别名的类型名称；默认别名为类名小写
                    alias：指定新别名
                    
                    
          批量起别名：
                package:为某个包下的所有类起别名
                    name:指定包名
                    
                在批量起别名的情况下使用@Aliase注解为某个类型指定别名
                
                
#### environments

    environments：mybatis可以配置多种环境，default--指定使用某种环境，可以快速切换
        environment：配置一个具体的环境信息必须有两个标签（下面的），id--代表当前环境的唯一标识
            transactionManager：事务管理器
                type：事务管理器的类型，type指全类名
            dataSource ：数据源
                type：数据源类型；NUPOOLED   POOLED   JNDI type全类名
####      mappers

    mappers将sql映射注册到全局配置中
        mapper:注册一个映射
             url:网络路径或者磁盘路径
            resource：引入类路径下的资源
    
                    
```Java
public interface EmployeeMapper {       
       @Select("select * from t_user where id = #{id}")
       public Employee getEmplById(Integer id);
}
```
```xml
       <mappers>      
       <!-- 将写好的<u>sql</u>映射文件（EmployeeMapper.xml）注册到全局配置文件中 -->   
          <mapper resource="EmployeeMapper.xml"/>    
       </mappers>
```
            
            
            
            
            
            class:引用（注册）接口
                1、有sql映射文件，映射文件名必须和接口同名，并且与接口放在同一目录下
                2、没有sql映射文件，所有的sql都是利用注解写在接口上；
                
                
```Java
public interface EmployeeMapper {       
       @Select("select * from t_user where id = #{id}")
       public Employee getEmplById(Integer id);
}
```
```xml
       <mappers> 
          <mapper class = " mybatis.EmployeeMapper"/>
       </mappers>


```
## Mybatis的映射文件
    实现数据库的增删改查
#####     创建一个接口
    
```Java
public interface EmployeeMapper {      

       //@Select("select * from t_user where id = #{id}")

       public Employee getEmplById(Integer id);

       public void addEmp(Employee employee);

       public void Update(Employee employee);

       public void deleteEmpById(Integer id);



}


```
#### 创建全局配置文件和sql的映射文件
    配置文件同上
    sql映射文件如下
    
```xml
<?xml version="1.0" encoding="UTF-8" ?> 

<!DOCTYPE mapper  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"  
"http://mybatis.org/dtd/mybatis-3-mapper.dtd"> 

<mapper namespace="/Mybatis_001/src/mybatis/EmployeeMapper">

<!-- 名称空间:指为接口的全类名

id：唯一标识

resultType：返回值类型

public Employee getEmplById(Integer id);

 -->

<!-- selectEmp -->

  <select id="getEmplById" resultType="mybatis.Employee">
<!--查-->
    select * from t_user where id = #{id}

      </select>
//增
      <insert id="addEmp">

      insert into t_user(name)

      value(#{name})

      </insert>
//改
      <update id="updateEmp">

      update t_user set name=#{name}

      </update>
//删
      <delete id="deleteEmp">

      delete fromt_user where name=#{name}

      </delete>

</mapper>


```
####     创建sqlSessionFactory和SqlSession的实现类
        
```Java
       //为了方便将得到SqlSessionFactory对象封装成一个方法

       public SqlSessionFactory <u>getSqlSessionFactory() </u><u>throws</u><u> IOException</u> {

                    String resource = "mybatis-config.xml";

                    InputStream inputStream = 
Resources.getResourceAsStream(resource);

                    return new SqlSessionFactoryBuilder().build(inputStream);         

             }

       

       @Test 

       public void fun4() throws IOException {

             SqlSessionFactory sqlSessionFactory = getSqlSessionFactory();

             SqlSession  openSession = sqlSessionFactory.openSession();

             try {

                    EmployeeMapper mapper  =  openSession.getMapper(EmployeeMapper.class);

                    Employee employee2 = new Employee("chenbin",2);

                    mapper.addEmp(employee2);

             } finally {

                    openSession.close();

                    

             }

       }


```
#### useGeneratedKeys
       <insert id="addEmp" useGeneratedKeys="true">
       mysql 支持自增主键，自增主键的获取也是利用statement.getGeneratedKeys（）；
       
##      mybatis的参数处理
    单个参数：mybatis不会做特殊处理   #{参数}：取出参数值
    
    多个参数：mybatis会做特殊处理、
        多个参数会被封装成一个map
            key:param1。。。。param10
            value:传入参数值
            #{ }就是map中获取指定的key的值
           select * from t_user where id = #{param1} and name  = #{param1} 
#####            命名参数：明确指定封装参数时map的key
    @param（id）
    多个参数会被封装成一个map，
        key:使用@param注解指定的值
        在接口中使用注解
        public Employee getEmployeeAndName(@param("id")Integer id,@param("name") String name) ;
        value：参数值
      #{ 指定的key}取出对应的参数值
      select * from t_user where id = #{name} and name  = #{id} 
      
##### pojo      
             如果参数很多，参数正好使是我们业务逻辑数据模型，我们可以直接传入pojo;
      #{ 属性名}：取出传入的pojo的属性值
#####   map    
      如果数据不是业务模型，没有对应的pojo,我们可以传入
      map
      接口中
      public Employee getEmployeeByMap(Map<String,object> map)
      
      
```Java
  @Test 

       public void fun4() throws IOException {

             SqlSessionFactory sqlSessionFactory = getSqlSessionFactory();

             SqlSession  openSession = sqlSessionFactory.openSession();

             try {

                    EmployeeMapper mapper  =  openSession.getMapper(EmployeeMapper.class);
Map<String ,Object> map = new HashMap<>();
map.put("id",1);
map.put("name",Tom);
                  
             } finally {

                    openSession.close();

                    

             }

       }

```
    在sql映射中
     select * from t_user where id = #{id} and name = #{name
     }
      
      
#####      TO
    如果多个参数不是业务模型中的数据，但是经常使用，推荐编写一个TO数据传输对象
    Page      
    {
    int index；
    int size;
    }
