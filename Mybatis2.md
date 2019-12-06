# 2.2 Mybatis2(半自动框架)
##         Mybatis映射文件
###  参数值的获取
    #（ ） ：可以获取map中的值或者pojo对象属性值
    $（ ）：可以获取map中的值或者pojo对象属性值
        区别：
        #{}是以预编译的形式，将参数设置到sql语句句中；preparedStatement;防止sql注入；
        ${ }:取出的值直接拼装sql语句中；会有安全问题； 
    #{ }：更丰富的用法
        jdbcType：我们在数据为null的时候有些数据库可能不被识别mybatis对null的默认处理。比如Oracle（报错）
        由于全局配置中，jabcTypeForNull=OTHER;Oracle不支持
        解决：
                1、#{email，jdbcType=OTHER};
                2、jdbcTYpeFORNull=NUll；
                
                
                
 ### select返回List
     
```java
public List getEmplByNameLike(String name);


```
```xml
<!-- <u>resultype</u>:如果返回值是一个集合,要写集合的中的元素类型 -->>

      <select id="getEmplByNameLike" resultType="mybatis.Employee">

      select * from t_user where name like#{name}

      </select>
```
### select记录封装map
    
```java
//返回一条的记录的map，key就是列名，值是对应的值

public Map<String ,Object> getEmpByIdReturnMap(Integer id);


```
```xml
<select id="getEmpByIdReturnMap" resultType="map">

      select * from t_user where id like#{id}

      </select>


```
        

```Java
    //多条记录封装一个map Map<Integer,Employee>
    //告诉mybatis哪一个是主键
    @MapKey（“id”）
    public Map<Integer,Employee> getEmpByNameLikeReturnMap(String name);


```
 
```xml
  <select id="getEmpByNameLikeReturnMap" resultType="mybatis.Employee">

       select * from t_user where name like#{name}

      </select>


```

#### resultMap
     resultmap：自定义结结果集映射规则；
```xml
<select id="getEmployeeById" resultMap="Emplyee">

             select * from t_user where id = #{id}

       </select>


```
            自定义某个Javabean的封装规则
                type：自定义规则的Java类型
                id：唯一id方便引用
                
```xml
<resultMap type="mybatisresultmap.Employee" id="myemp">
               、//id：定义主键会底层优化
               //column：指哪一列
               //property：对应的Javabean属性
               
             <id column="id" property="id"/>
                //普通定义列封装规则
             <result column="name" property="name"/>

       </resultMap>


```       

 ## 动态sql
    if：
            查询员工看，要求携带了哪个字段查询的条件就带上这个字段的值
            
            
            
```xml


       <select id="getEmpsByCongitionIF" 
resultType="mybatisresultmap.Employee">

       <!-- test :判断表达式（OGNL）

             从参数取值进行判断

             遇见特殊符号应该去写转义字符

        -->>

             select * from t_user where 

             <if test="id!=null">

                    id=#{id}

             </if>

             <if test="name!=null and !=' ' ">

                    and name = #{name}

             </if>

       

       </select>


```
                查询的时候如果某些条件没带，可能sql的拼装会有问题：
          1、给wherea后面加上1=1，以后的条件都and xxxx
          2、mybatis使用where标签将所有的查询条件包括在内。mybatis 就还将where标签中拼装sql，多出来的and或者or都会被去掉
          但是后面多出来的and或者or不能被解绝；
          3、trim标签：标签体中是整个字符串拼串后的结果
                prefix:给拼串后的整个字符串加一个前缀
          
         
```xml
  where 1=1

            <if test="id!=null">

                          id=#{id}

                    </if>


```
 
          
```xml
<where>

       </select>

             select * from t_user where 

             <if test="id!=null">

                    id=#{id}

             </if>

             <if test="name!=null and !=' ' ">

                    and name = #{name}

             </if>

       </where>


```


choose:
        如果带了id就查id，带了什么就查什么；
        
        
```xml
<select id="getEmpsByCongitionIF" resultType="mybatisresultmap.Employee">

       select * from t_user where 
<where>

       <choose>

             <when test="id!=null" >

                    id=#{id}                  

             </when>

             

             <when test="name!=null">

                    name = #{name}

             </when>      

       </choose>

</where>


```


    foreach:
                collection:指定遍历的集合；
                            list类型封装的参数会特殊处理封装在map中，map的key就叫list
                 item：将遍历出的元素赋值给指定的变量
                 sepaarator：每个元素之间的分隔符
                 open：遍历出的所有结果拼接一个开始的字符
                 close：遍历出的所有结果拼接一个结束字符
                 #{ 变量名}就能取出变量的值也就是当前遍历的元素
                 index：索引。遍历的时候就是索引
                        遍历的map的时候index表示的就是map的key，item就是map的value；
             两个内置参数：
                    不只是方法传递过来的参数可以被用来判断，取值。。。。。。
                    mybatis还有两个默认内置参数：
                        _parameter:代表整个参数
                            单个参数：_parameter就是这个参数
                            多个参数：参数就会被封装成一个map；_parameter就代表这个map
                        _databaseId:如果配置了DatabaseIDProovide标签
                                _databaseID就代表当前数据库的别名
                                
                                
    bind:
            可以将OGNL表达式的值绑定在一个变量中，方便引用这个变量的值
   
