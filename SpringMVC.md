#  SpringMVC

    Springmvc 通过一套MVC注解，让POJO成为 处理请求的控制器，而无须实现任何接口；
    
    
####  第一步
    创建一个项目，这里我们来创建一个maven的项目，通过maven来帮我们管理相关的jar文件，当然你也可以创建一个web项目，不过这样的话，就需要你手动的去下载Spring MVC相关的jar包了。
#### 第二步
    
    导入相关Jar包，这里使用maven来帮我们管理，下面是pom.xml文件中的内容,目前只是写了一个非常简单的Spring MVC，所以只使用maven导入了spring-webmvc相关的包即可
    
```xml

 <dependency> 
    <groupId>javax.servlet</groupId> 
    <artifactId>javax.servlet-api</artifactId> 
    <version>3.1.0</version>
 </dependency> 
 <dependency> 
    <groupId>org.springframework</groupId> 
    <artifactId>spring-webmvc</artifactId> 
    <version>5.0.4.RELEASE</version>
 </dependency>
```
    
#### 第三步    
    注册Spring MVC的中央控制器DispatcherServlet，打开web.xml文件，在里面加入下面内容：

   
```xml
 <!-- 注册spring MVC中央控制器 -->

<servlet>

    <servlet-name>springMVC</servlet-name>
    <!-- spring MVC中的核心控制器 -->    
<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>

    <init-param>

        <param-name>contextConfigLocation</param-name>

        <param-value>classpath:springmvc.xml</param-value>

    </init-param>

    <load-on-startup>1</load-on-startup>

</servlet>

<servlet-mapping>

    <servlet-name>springMVC</servlet-name>

    <url-pattern>*.do</url-pattern>

</servlet-mapping>

```
####  第四步
    
maven项目中有个src/main/resources目录，在该目录下创建Spring MVC配置文件springmvc.xml，该xml配置文件可以任意命名，需要跟第三步中的init-param中的param-value保持一致即可。


```xml
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"

    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"

    xmlns:p="http://www.springframework.org/schema/p"

    xmlns:context="http://www.springframework.org/schema/context"

    xsi:schemaLocation="

        http://www.springframework.org/schema/beans

        http://www.springframework.org/schema/beans/spring-beans.xsd

        http://www.springframework.org/schema/context

        http://www.springframework.org/schema/context/spring-context.xsd">





</beans>   


```
#### 第五步
    
    创建一个类去实现org.springframework.web.servlet.mvc.Controller接口，通常我们称这样的类为Controller，它的作用有些类似之前学习的servlet，或者可以认为在Spring MVC里面，就是使用了Controller来代替了servlet，它提供了比servlet更加丰富的功能。

```xml
ackage com.monkey1024.controller;



import javax.servlet.http.HttpServletRequest;

import javax.servlet.http.HttpServletResponse;



import org.springframework.web.servlet.ModelAndView;

import org.springframework.web.servlet.mvc.Controller;



public class HelloSpringMVC implements Controller{



    @Override

    public ModelAndView handleRequest(HttpServletRequest arg0, HttpServletResponse 
arg1) throws Exception {

        ModelAndView mv = new ModelAndView();

        mv.addObject("hello", "hello first spring mvc");

        mv.setViewName("/WEB-INF/jsp/first.jsp");

        return mv;

    }

}


```
####  第六步
    在springmvc.xml配置文件中注册第五步创建的Controller，添加下面内容：
    
```xml

<bean id="/hello.do" class="com.monkey1024.controller.HelloSpringMVC" />
```
#### 第七步
    
    在WEB-INF目录中创建一个目录jsp，在这个jsp目录下创建一个first.jsp的文件
    
    
```xml
<%@ page language="java" contentType="text/html; charset=utf-8"

    pageEncoding="utf-8"%>

<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" 
"http://www.w3.org/TR/html4/loose.dtd">

<html>

<head>

<title>Insert title here</title>

</head>

<body>

${hello}

</body>

</html>


```
#### 第八步
    启动tomcat，然后在浏览器地址栏中输入：http://localhost:8080/01-first/hello.do如果你看到网页中显示hello first spring mvc，那就说明第一个Spring MVC程序编写成功啦！
##     视图解析器
    
```xml
<!-- 视图解释类 -->

<bean

    class="org.springframework.web.servlet.view.InternalResourceViewResolver">

    <property name="prefix" value="/WEB-INF/jsp/" />

    <property name="suffix" value=".jsp" />

</bean>


```
### SpringMVC执行流程
 ![cc97c3344aea268b6e59ba326f2cd9f0.png](en-resource://database/402:1)
    
    
  ### url-pattern的写法
    
    设置url-pattern为*.do之前我们在web.xml文件中配置DispatcherServlet的时候，将url-pattern配置为*.do的方式，其实除了这种方式之外你还可以配置为其他任意方式：
    
    *.action *.abc *.123
    
    
    只要你的请求url中包含配置的url-pattern，该url就可以到达DispatcherServlet。当然这里业内通常都将url-pattern配置为*.do的方式，所以你最好也这么去做。

##### 设置url-pattern为/*
    
    如果将url-pattern设置为/*之后，web项目中的jsp都不能访问了会报出404的错误，这是因为DispatcherServlet会将向JSP页面的跳转请求也当作是一个普通的 Controller 请求，会对其进行处理，而此时是找不到与其相应的controller。
    
   ##### 设置url-pattern为/            
        
    如果将url-pattern设置为/之后，只要是在web.xml文件中找不到匹配的URL，它们的访问请求都将交给DispatcherServlet处理，静态资源：css文件,js文件,图片也会被拦截并交给DispatcherServlet处理。该配置方式不会拦截.jsp文件和.jspx文件，因为这个在tomcat中的conf目录里面的web.xml文件中已经添加的相应的处理方式了，他会交给org.apache.jasper.servlet.JspServlet来处理。即我们可以正常访问系统中的jsp文件。现在restful风格的URL越来越流行，这种/的配置方式使用的也越来越多了。
    
    
####  解决静态资源404的问题
    
    
    方式一：
    打开你的tomcat中conf/web.xml,在这个文件中有一个叫做DefaultServlet的配置,当系统找不到处理某次url请求该交由谁处理的时候，就会交给这个servlet处理。我们可以通过使用这个DefaultServlet来处理静态资源，在你的系统中的web.xml文件中添加下面配置，要添加在DispatcherServlet的前面，这样系统就会将带有下面后缀名的请求交给defaultservlet来处理：
    
```
   <servlet-mapping>

        <servlet-name>default</servlet-name>

        <url-pattern>*.jpg</url-pattern>

    </servlet-mapping>

    <servlet-mapping>

        <servlet-name>default</servlet-name>

        <url-pattern>*.js</url-pattern>

    </servlet-mapping>

    <servlet-mapping>

        <servlet-name>default</servlet-name>

        <url-pattern>*.css</url-pattern>

    </servlet-mapping>


```

    方式二：
    使用mvc:default-servlet-handler在springmvc.xml文件中添加下面配置即可，该方式会对所有的请求进行处理，然后交由相应的servlet，这种方式其实最终也是由DefaultServlet来处理：
    <mvc:default-servlet-handler/>
    
    
    
    方式三        
    使用mvc:resources在spring mvc中提供了mvc:resources标签用来解决静态资源无法访问的问题，只需要在springmvc.xml的配置文件中添加下面内容即可，这样会交给spring mvc的ResourceHttpRequestHandler类来处理：  

    <mvc:resources mapping="/images/**" location="/images/" />
    
    mapping ：表示该资源的请求；注意后面的两个**
    loaction: 静态资源所在目录
    
###    使用注解编写Spring MVC程序

    
    在实际开发中，我们会创建很多Controller来满足业务方面的需求，这样就会导致一个问题，需要在springmvc.xml配置文件中配置大量的bean导致该配置文件变的臃肿起来，为了解决这个问题，spring MVC提供了一系列的注解，通过设置注解，可以使springmvc.xml配置文件变的简洁。使用注解编写spring MVC程序整体的步骤跟之前的差不多（不要忘记在web.xml文件中添加DispatherServlet），只不过有一些地方需要修改，在之前的spring MVC程序中添加下面内容。注册扫描器
    在配置文件中我们只需要注册一个组件扫描器即可，其中base-package写上你的包名即可，下面这种写法表示会扫描com.monkey1024下的所有包和类，该组件扫描器是spring中的内容，具体咱在spring中再讲解。
    <!-- 注册组件扫描器 -->
    <context:component-scan base-package="com.monkey1024.*"/>

    <!-- 视图解析器 -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/jsp/"/>
        <property name="suffix" value=".jsp"/>
    </bean>注意：如果在你的springmvc.xml文件中配置了静态资源即使用了mvc:resources标签的话，那么需要在你的配置文件中配置注解驱动，添加下面内容：<mvc:annotation-driven/>如果没有添加注解驱动的话，你访问controller的时候会出现404错误。定义处理器
    在com.monkey1024.controller包下创建一个TestController类：package com.monkey1024.controller;

    import org.springframework.stereotype.Controller;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.servlet.ModelAndView;

    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;

    @Controller()

    public class TestController {

    @RequestMapping("/test/test1.do")
    public ModelAndView test1(HttpServletRequest request, HttpServletResponse response) throws Exception{

        ModelAndView mv = new ModelAndView();
        mv.addObject("hello", "test1");
        mv.setViewName("test1");
        return mv;
    }


    @RequestMapping({"test/test2.do", "test/hello.do"})
    public ModelAndView test2(HttpServletRequest request, HttpServletResponse response) throws Exception{

        ModelAndView mv = new ModelAndView();
        mv.addObject("hello", "test2");
        mv.setViewName("test2");
        return mv;
    }

    }
            这里jsp的创建就省略了，启动tomcat，spring MVC会根据你输入的url来调用相应的方法。处理器中的注解在上面的示例中，我们创建了一个TestController类，这类没有实现任何接口，只是在类名和方法上添加了两个注解：@Controller：表示当前类为一个Controller@RequestMapping：表示当前方法为Controller中的方法，该方法要对 value 属性所指定的 URL进行处理与响应，被注解的方法的名称是可以随意命名的。当有多个请求可以匹配该方法时，可以写上一个String类型的数组，如上示例中的test2方法。
    @RequestMapping注解还可以定义在类上面，在上面的示例中，test1方法和test2方法中的url路径中都包含了/test，此时我们把这些相同的url抽取出来，放到类上的注解@RequestMapping中，此时可以称之为命名空间。package com.monkey1024.controller;

    import org.springframework.stereotype.Controller;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.servlet.ModelAndView;

    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;

    @Controller()
    @RequestMapping("/test")//类名上的注解，命名空间namespacepublic class TestController02 {

    @RequestMapping("/test1.do")
    public ModelAndView test1(HttpServletRequest request, HttpServletResponse response) throws Exception{

        ModelAndView mv = new ModelAndView();
        mv.addObject("hello", "test1");
        mv.setViewName("test1");
        return mv;
    }


    @RequestMapping({"/test2.do", "/hello.do"})
    public ModelAndView test2(HttpServletRequest request, HttpServletResponse response) throws Exception{

        ModelAndView mv = new ModelAndView();
        mv.addObject("hello", "test2");
        mv.setViewName("test2");
        return mv;
    }

    }
    
    
   #### RequestMappering
    
    在RequestMapping中我们可以使用*号表示通配符从而匹配一系列的请求：                             @RequestMapping("/test*.do")
    表示请求的url中只要是以test开头就可以被当前方法处理。                                               @RequestMapping("/*test.do")
    表示请求的url中只要是以test结尾就可以被当前方法处理。                                      ·       @RequestMapping("monkey/*/test.do")
    表示在test.do的前面，只能有两级路径，第一级必须是monkey，而第二级随意，例 如：monkey/1024/test.do。这种称为路径级数的精确匹配。                                            @RequestMapping("monkey/**/test.do")
    表示在test.do的资源名称前面，必须以monkey路径开头，而其它级的路径包含几级，各级又叫什么名称，均随意，例如：monkey/1/0/2/4/test.do。 这种称为路径级数的可变匹配。
    
    
 请求的提交方式
    
    在@RequestMapping中有一个method属性，改属性可以设置接收请求的提交方式：@RequestMapping(value="/test.do",method = RequestMethod.POST)
    public ModelAndView test(HttpServletRequest request, HttpServletResponse response) throws Exception {
    ModelAndView mv = new ModelAndView();
    mv.addObject("hello", "test");
    mv.setViewName("test1");
    return mv;
    }
    上面的注解表示，只有当/test.do的请求的方式是post的时候才会执行当前方法，对于其他请求方式不进行处理。如果不写method属性的话，无论你是使用get或者post或者其他方式，它都会进行处理。
    上面的RequestMethod是一个枚举类型，里面包含了大部分的提交方式。示例，创建一个Controller，里面添加连个方法分别只处理get和post方式的请求：package com.monkey1024.controller;

    import org.springframework.stereotype.Controller;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.bind.annotation.RequestMethod;
    import org.springframework.web.servlet.ModelAndView;

    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;


    @Controller//表示当前类是一个controller@RequestMapping("/test")//表示是一个命名空间namespacepublic class TestController03 {


    @RequestMapping(value="/requestGet.do",method = RequestMethod.GET)
    public ModelAndView reqGet(HttpServletRequest request, HttpServletResponse response) throws Exception {
        ModelAndView mv = new ModelAndView();
        mv.addObject("method", "get请求");
        mv.setViewName("get");
        return mv;
    }


    @RequestMapping(value="/requestPost.do",method = RequestMethod.POST)
    public ModelAndView reqPost(HttpServletRequest request, HttpServletResponse response) throws Exception {
        ModelAndView mv = new ModelAndView();
        mv.addObject("hello", "post请求");
        mv.setViewName("post");
        return mv;
    }

    }
    index.jsp:<%@ page contentType="text/html;charset=UTF-8" language="java" %><html><body><form action="/test/requestGet.do" method="get">

    <input type="submit" value="get请求"></form><br><form action="/test/requestPost.do" method="post">

    <input type="submit" value="post请求"></form></body></html>请求中携带的参数在RequestMapping中还有一个属性是params，通过这个属性我们可以指定请求中必须携带的参数。@RequestMapping(value="/test.do" ,  params={"name" , "age"}) 
    要求请求中必须携带请求参数 name 与 age@RequestMapping(value="/test.do" , params={"!name" , "age"}) 
    要求请求中必须携带请求参数 age，但必须不能携带参数 name@RequestMapping(value="/test.do" , params={"name=jack" , "age=23"}) 
        要求请求中必须携带请求参数 name，且其值必须为jack；必须携带参数 age，其值必须为 23@RequestMapping(value="/test.do" , params="name!=jack") 
    要求请求中必须携带请求参数name，且其值必须不能为jack
