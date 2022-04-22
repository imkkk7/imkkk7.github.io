# **SpringMVC**

## **概述**

SpringMVC是一个Java开源框架，是Spring Framework的一个独立模块。

- MVC框架，在项目中开辟MVC层次架构
- 对控制器中的功能包装简化扩展践行工厂模式，功能架构在工厂之上

## MVC架构

- Model：模型

即业务模型，负责完成业务中的数据通信处理，对应项目中的Service和Dao

- View     ：视图

渲染数据，生成页面。对应项目中的Jsp

- Controller：控制器

直接对接请求，控制MVC流程，调度模型，选择视图。对应项目中的Servlet

使用MVC架构，各层次内部职责单一，层次之间耦合度低。有利于项目的维护。



需要导入的依赖

~~~xml
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>5.3.18</version>
    </dependency>
~~~

springmvc-servlet.xml

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/mvc
       https://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <!-- 自动扫描包，让指定包下的注解生效,由IOC容器统一管理 -->
    <context:component-scan base-package="com.kk.controller"/>
    <!-- 让Spring MVC不处理静态资源 -->
    <mvc:default-servlet-handler />
    <!--
    支持mvc注解驱动
        在spring中一般采用@RequestMapping注解来完成映射关系
        要想使@RequestMapping注解生效
        必须向上下文中注册DefaultAnnotationHandlerMapping
        和一个AnnotationMethodHandlerAdapter实例
        这两个实例分别在类级别和方法级别处理。
        而annotation-driven配置帮助我们自动完成上述两个实例的注入。
     -->
    <mvc:annotation-driven />

    <!-- 视图解析器 -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"
          id="internalResourceViewResolver">
        <!-- 前缀 -->
        <property name="prefix" value="/" />
        <!-- 后缀 -->
        <property name="suffix" value=".jsp" />
    </bean>

</beans>
~~~

web.xml

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

  <!--1.注册servlet-->
  <servlet>
    <servlet-name>SpringMVC</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <!--通过初始化参数指定SpringMVC配置文件的位置，进行关联-->
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:springmvc-servlet.xml</param-value>
    </init-param>
    <!-- 启动顺序，数字越小，启动越早 -->
    <load-on-startup>1</load-on-startup>
  </servlet>

  <!--所有请求都会被springmvc拦截 -->
  <servlet-mapping>
    <servlet-name>SpringMVC</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>

</web-app>
~~~

Controller

~~~java
@Controller
@RequestMapping("/HelloController")
public class HelloController {

    //真实访问地址 : 项目名/HelloController/hello
    @RequestMapping("/hello")
    public String sayHello(Model model){
        //向模型中添加属性msg与值，可以在JSP页面中取出并渲染
        model.addAttribute("msg","hello,SpringMVC");
        //web-inf/jsp/hello.jsp
        return "WEB-INF/jsp/hello";
    }
}
~~~

## springmvc原理

1.DispatcherServlet(前端控制器)根据请求调用HandlerMapping(处理器映射器),HandlerMapping根据请求查找Handler

2.HandlerExecution表示具体的Handler，其主要作用是根据url查找控制器返回给DispatcherServlet

3.DispatcherServlet根据Handler找到能够执行该处理器的处理器适配器(HandlerAdaptor)

4.HandlerAdaptor会调用具体的Controller

5.Controller 将处理结果和要跳转的视图封装到对象ModelAndView并将其返回给HandlerAdaptor

6.HandlerAdaptor将ModelAndView转交给DispatcherServlet,至此，业务处理完毕

7.DispatcherServlet调用ViewResolver(视图解析器)来解析HandlerAdapter传递的逻辑视图名

8.ViewResolver将解析的逻辑视图名传给DispatcherServlet

9.DispatcherServlet根据视图解析器解析的试图结果，调用具体的视图

10.将视图呈现出来