# 2.3 Mybatis 3(逆向工程）
###    导包 [ mybatis-generator](http://www.mybatis.org/generator/index.html)
        mybatis
        mybatis-connetor
        mybatis-generator
        
###         在根目录下 创建mbg.xml文件
```xml

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
  PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
  "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
  <classPathEntry location="/Program Files/IBM/SQLLIB/java/db2java.zip" />

  <context id="DB2Tables" targetRuntime="MyBatis3">
    <jdbcConnection driverClass="COM.ibm.db2.jdbc.app.DB2Driver"
        connectionURL="jdbc:db2:TEST"
        userId="db2admin"
        password="db2admin">
    </jdbcConnection>

    <javaTypeResolver >
      <property name="forceBigDecimals" value="false" />
    </javaTypeResolver>

    <javaModelGenerator targetPackage="test.model" targetProject="\MBGTestProject\src">
      <property name="enableSubPackages" value="true" />
      <property name="trimStrings" value="true" />
    </javaModelGenerator>

    <sqlMapGenerator targetPackage="test.xml"  targetProject="\MBGTestProject\src">
      <property name="enableSubPackages" value="true" />
    </sqlMapGenerator>

    <javaClientGenerator type="XMLMAPPER" targetPackage="test.dao"  targetProject="\MBGTestProject\src">
      <property name="enableSubPackages" value="true" />
    </javaClientGenerator>

    <table schema="DB2ADMIN" tableName="ALLTYPES" domainObjectName="Customer" >
      <property name="useActualColumnNames" value="true"/>
      <generatedKey column="ID" sqlStatement="DB2" identity="true" />
      <columnOverride column="DATE_FIELD" property="startDate" />
      <ignoreColumn column="FRED" />
      <columnOverride column="LONG_VARCHAR_FIELD" jdbcType="VARCHAR" />
    </table>

  </context>
</generatorConfiguration>

```
#### jdbcConnection
    指定目标如何连接到数据库
    
```xml
<!-- jdbcConnection:指定如何连接到目标数据库 -->

    <jdbcConnection driverClass="com.mysql.jdbc.Driver"

        connectionURL="jdbc:mysql://localhost:3306/haha"

        userId="root"

        password="123">

    </jdbcConnection>


```
#### javaTypeResolver
    类型解析器
    
```xml       
    <javaTypeResolver >   
    <!-- javaTypeResolver:类型解析器    

     -->

      <property name="forceBigDecimals" value="false" />

    </javaTypeResolver>


```
#### javaModelGenerator
    指定JavaBean的生产策略;
         targetPackage:目标包名
        targetProject:目标工程
        
        
```xml
 </javaTypeResolver>

       <!-- javaModelGenerator:指定JavaBean的生产策略 

        targetPackage:目标包名

        targetProject:目标工程        
        -->

    <javaModelGenerator targetPackage="bean" targetProject=".\src">

      <property name="enableSubPackages" value="true" />

      <property name="trimStrings" value="true" />

    </javaModelGenerator>


```
#### sqlMapGenerator
    sql映射生成策略
    
```xml
<!-- sqlMapGenerator:sql映射生成策略 -->

    <sqlMapGenerator targetPackage="dao"  targetProject=".\src">

      <property name="enableSubPackages" value="true" />

    </sqlMapGenerator>


```
#### javaClientGenerator
    指定mapper接口所在的位置

```xml
<!--javaClientGenerator:指定mapper接口所在的位置  -->

    <javaClientGenerator type="XMLMAPPER" targetPackage="Dao"  
targetProject=".\src">

      <property name="enableSubPackages" value="true" />

    </javaClientGenerator>


```
#### table
    -指定逆向分析那些表,根据表创建JavaBean
```xml
<!--指定逆向分析那些表,根据表创建JavaBean -->

    <table  tableName="t_user" domainObjectName="Customer" >

     

    </table>


```
#### 测试
```java
public class Test1 {

       //为了方便将得到SqlSessionFactory对象封装成一个方法

             public SqlSessionFactory getSqlSessionFactory() throws 
IOException {

                    String resource = "mybatis-config.xml";

                    InputStream inputStream = 
Resources.getResourceAsStream(resource);

                    return new SqlSessionFactoryBuilder().build(inputStream);         

             }

             

             @Test

             public void testmbg() throws Exception {

                    List<String> warnings = new ArrayList<String>();

                       boolean overwrite = true;

                       File configFile = new File("mbg.xml");

                       ConfigurationParser cp = new 
ConfigurationParser(warnings);

                       Configuration config = 
cp.parseConfiguration(configFile);

                       DefaultShellCallback callback = new 
DefaultShellCallback(overwrite);

                       MyBatisGenerator myBatisGenerator = new 
MyBatisGenerator(config, callback, warnings);

                       myBatisGenerator.generate(null);

                    

             }



}


```

## Mybatis -工作原理
    
##     Mybatis插件开发
### 分页插件 pagehelper（[pagehelper](https://github.com/pagehelper/Mybatis-PageHelper/blob/master/wikis/en/HowToUse.md)）
    1、导入jar包
            pagehelper-5.0.0-rc。jar
     2、在全局配置文件中添加配置信息
            
```xml

< plugins >
    < plugin  interceptor = “ com.github.pagehelper.PageInterceptor ” >
      
        </ plugin >
</ plugins >
```
3、使用方式
            
```Java

//2. use static method startPage

PageHelper.startPage(1, 10);
List<Country> list = countryMapper.selectIf(1);
```

```Java

//获取第1页，10条内容，默认查询总数countPageHelper.startPage(1, 10);
List<Country> list = countryMapper.selectAll();
//用PageInfo对结果进行包装PageInfo page = new PageInfo(list);
//测试PageInfo全部属性//PageInfo包含了非常全面的分页属性
assertEquals(1, page.getPageNum());
assertEquals(10, page.getPageSize());
assertEquals(1, page.getStartRow());
assertEquals(10, page.getEndRow());
assertEquals(183, page.getTotal());
assertEquals(19, page.getPages());
assertEquals(1, page.getFirstPage());
assertEquals(8, page.getLastPage());
assertEquals(true, page.isFirstPage());
assertEquals(false, page.isLastPage());
assertEquals(false, page.isHasPreviousPage());
assertEquals(true, page.isHasNextPage());
```
  
### 批量操作
