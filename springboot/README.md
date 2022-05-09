# SpringBoot

[![Lv85Jf.png](https://s1.ax1x.com/2022/04/29/Lv85Jf.png)](https://imgtu.com/i/Lv85Jf)

## 第一个SpringBoot程序

## 原理初探

### pom.xml

- spring-boot-dependencies：核心依赖在父工程中

- 我们在写或者引入一些SpringBoot依赖的时候，不需要指定版本，就因为有这些版本仓库

### 启动器

~~~xml
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.6.7</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>
~~~

- 启动器说白了就是Springboot的启动场景

- 比如spring-boot-strater-web，他就会帮我们自动导入web环境所有的依赖

- springboot会将所有的功能场景都变成一个个的启动器
- 我们要使用什么东西只要寻找它的启动器就行

SpringBoot将所有的功能场景都抽取出来，做成一个个的starter （启动器），只需要在项目中引入这些starter即可，所有相关的依赖都会导入进来 ， 我们要用什么功能就导入什么样的场景启动器即可 ；我们未来也可以自己自定义 starter；

### 主启动类

## 默认的主启动类

```java

//@SpringBootApplication 来标注一个主程序类
//说明这是一个Spring Boot应用
@SpringBootApplication
public class SpringbootApplication {

   public static void main(String[] args) {
     //以为是启动了一个方法，没想到启动了一个服务
      SpringApplication.run(SpringbootApplication.class, args);
   }

}
```



### @SpringBootApplication

作用：标注在某个类上说明这个类是SpringBoot的主配置类 ， SpringBoot就应该运行这个类的main方法来启动SpringBoot应用；

进入这个注解：可以看到上面还有很多其他注解！

```java

@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(
    excludeFilters = {@Filter(
    type = FilterType.CUSTOM,
    classes = {TypeExcludeFilter.class}
), @Filter(
    type = FilterType.CUSTOM,
    classes = {AutoConfigurationExcludeFilter.class}
)}
)
public @interface SpringBootApplication {
    // ......
}
```



### @ComponentScan

这个注解在Spring中很重要 ,它对应XML配置中的元素。

作用：自动扫描并加载符合条件的组件或者bean ， 将这个bean定义加载到IOC容器中



### @SpringBootConfiguration

作用：SpringBoot的配置类 ，标注在某个类上 ， 表示这是一个SpringBoot的配置类；

```java

// 点进去得到下面的 @Component
@Configuration
public @interface SpringBootConfiguration {}

@Component
public @interface Configuration {}
```

这里的 @Configuration，说明这是一个配置类 ，配置类就是对应Spring的xml 配置文件；

里面的 @Component 这就说明，启动类本身也是Spring中的一个组件而已，负责启动应用！

我们回到 SpringBootApplication 注解中继续看。



### @EnableAutoConfiguration

**@EnableAutoConfiguration ：开启自动配置功能**

以前我们需要自己配置的东西，而现在SpringBoot可以自动帮我们配置 ；@EnableAutoConfiguration告诉SpringBoot开启自动配置功能，这样自动配置才能生效；

**@AutoConfigurationPackage ：自动配置包**

```java
@Import({Registrar.class})public @interface AutoConfigurationPackage {}
```

**@import** ：

Spring底层注解@import ， 给容器中导入一个组件

Registrar.class 作用：将主启动类的所在包及包下面所有子包里面的所有组件扫描到Spring容器 ；

自动配置真正实现是从classpath中搜寻所有的META-INF/spring.factories配置文件 ，并将其中对应的 org.springframework.boot.autoconfigure. 包下的配置项，通过反射实例化为对应标注了 @Configuration的JavaConfig形式的IOC容器配置类 ， 然后将这些都汇总成为一个实例并加载到IOC容器中。

### @Import({AutoConfigurationImportSelector.class}) 

：给容器导入组件 ；

AutoConfigurationImportSelector ：自动配置导入选择器，那么它会导入哪些组件的选择器呢？我们点击去这个类看源码：

1、这个类中有一个这样的方法

```java
// 获得候选的配置
protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
    //这里的getSpringFactoriesLoaderFactoryClass（）方法
    //返回的就是我们最开始看的启动自动导入配置文件的注解类；EnableAutoConfiguration
    List<String> configurations = SpringFactoriesLoader.loadFactoryNames(this.getSpringFactoriesLoaderFactoryClass(), this.getBeanClassLoader());
    Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. If you are using a custom packaging, make sure that file is correct.");
    return configurations;
}
```

2、这个方法又调用了  SpringFactoriesLoader 类的静态方法！我们进入SpringFactoriesLoader类loadFactoryNames() 方法

```java
public static List<String> loadFactoryNames(Class<?> factoryClass, @Nullable ClassLoader classLoader) {
    String factoryClassName = factoryClass.getName();
    //这里它又调用了 loadSpringFactories 方法
    return (List)loadSpringFactories(classLoader).getOrDefault(factoryClassName, Collections.emptyList());
}
```

3、我们继续点击查看 loadSpringFactories 方法

```java

private static Map<String, List<String>> loadSpringFactories(@Nullable ClassLoader classLoader) {
    //获得classLoader ， 我们返回可以看到这里得到的就是EnableAutoConfiguration标注的类本身
    MultiValueMap<String, String> result = (MultiValueMap)cache.get(classLoader);
    if (result != null) {
        return result;
    } else {
        try {
            //去获取一个资源 "META-INF/spring.factories"
            Enumeration<URL> urls = classLoader != null ? classLoader.getResources("META-INF/spring.factories") : ClassLoader.getSystemResources("META-INF/spring.factories");
            LinkedMultiValueMap result = new LinkedMultiValueMap();

            //将读取到的资源遍历，封装成为一个Properties
            while(urls.hasMoreElements()) {
                URL url = (URL)urls.nextElement();
                UrlResource resource = new UrlResource(url);
                Properties properties = PropertiesLoaderUtils.loadProperties(resource);
                Iterator var6 = properties.entrySet().iterator();

                while(var6.hasNext()) {
                    Entry<?, ?> entry = (Entry)var6.next();
                    String factoryClassName = ((String)entry.getKey()).trim();
                    String[] var9 = StringUtils.commaDelimitedListToStringArray((String)entry.getValue());
                    int var10 = var9.length;

                    for(int var11 = 0; var11 < var10; ++var11) {
                        String factoryName = var9[var11];
                        result.add(factoryClassName, factoryName.trim());
                    }
                }
            }

            cache.put(classLoader, result);
            return result;
        } catch (IOException var13) {
            throw new IllegalArgumentException("Unable to load factories from location [META-INF/spring.factories]", var13);
        }
    }
}
```

4、发现一个多次出现的文件：spring.factories，其中有很多自动配置的文件；这就是自动配置根源所在！

**结论：**

1. SpringBoot在启动的时候从类路径下的META-INF/spring.factories中获取EnableAutoConfiguration指定的值
2. 将这些值作为自动配置类导入容器 ， 自动配置类就生效 ， 帮我们进行自动配置工作；
3. 整个J2EE的整体解决方案和自动配置都在springboot-autoconfigure的jar包中；
4. 它会给容器中导入非常多的自动配置类 （xxxAutoConfiguration）, 就是给容器中导入这个场景需要的所有组件 ， 并配置好这些组件 ；
5. 有了自动配置类 ， 免去了我们手动编写配置注入功能组件等的工作；

总结：springboot所有自动配置都是在启动的时候扫描并且加载：spring.factories所有的自动配置类都在这里，但是不一定会生效，要判断条件是否成立的根据是是否导入了对应 的start，只要导入了对应的start就有对应的启动器了，有了启动器我们的自动装配就会生效，然后就配置成功

## 主启动类的运行

**SpringApplication.run分析**

分析该方法主要分两部分，一部分是SpringApplication的实例化，二是run方法的执行；

**这个类主要做了以下四件事情：**

1、推断应用的类型是普通的项目还是Web项目

2、查找并加载所有可用初始化器 ， 设置到initializers属性中

3、找出所有的应用程序监听器，设置到listeners属性中

4、推断并设置main方法的定义类，找到运行的主类

## YAML

### 	基础语法

```yaml
# k=v
#普通的key-value
name: kk
#对象
student:
  name: kk
  age: 3
#行内写法
student1: {name: kk ,age: 3}
#数组
pets:
  - cat
  - dog
  - pig
pets1: [cat,dog,pig]
```

## JSR303校验

Springboot中可以用@validated来校验数据，如果数据异常则会统一抛出异常，方便异常中心统一处理。我们这里来写个注解让我们的name只能支持Email格式；

首先需要导入依赖

```xml
    <dependency> 
        <groupId>org.springframework.boot</groupId> 
        <artifactId>spring-boot-starter-validation</artifactId> 
    </dependency>
```

~~~java

@Component //注册bean
@ConfigurationProperties(prefix = "person")
@Validated  //数据校验
public class Person {

    @Email(message="邮箱格式错误") //name必须是邮箱格式
    private String name;
}
~~~

**使用数据校验，可以保证数据的正确性；** 

常见的参数：

### 空检查

- @Null 验证对象是否为null
- @NotNull 验证对象是否不为null, 无法查检长度为0的字符串
- @NotBlank 检查约束字符串是不是Null还有被Trim的长度是否大于0,只对字符串,且会去掉前后空格.
- @NotEmpty 检查约束元素是否为NULL或者是EMPTY.

**Booelan检查**

- @AssertTrue 验证 Boolean 对象是否为 true
- @AssertFalse 验证 Boolean 对象是否为 false

**长度检查**

- @Size(min=, max=) 验证对象（Array,Collection,Map,String）长度是否在给定的范围之内
- @Length(min=, max=) Validates that the annotated string is between min and max included.

### 日期检查

- @Past 验证 Date 和 Calendar 对象是否在当前时间之前，验证成立的话被注释的元素一定是一个过去的日期
- @Future 验证 Date 和 Calendar 对象是否在当前时间之后 ，验证成立的话被注释的元素一定是一个将来的日期
- @Pattern 验证 String 对象是否符合正则表达式的规则，被注释的元素符合制定的正则表达式
  - regexp:正则表达式
  - flags: 指定 Pattern.Flag 的数组，表示正则表达式的相关选项。

### 数值检查

建议使用在Stirng,Integer类型，不建议使用在int类型上，因为表单值为””时无法转换为int，但可以转换为Stirng为””，Integer为null

- @Min 验证 Number 和 String 对象是否大等于指定的值
- @Max 验证 Number 和 String 对象是否小等于指定的值
- @DecimalMax 被标注的值必须不大于约束中指定的最大值. 这个约束的参数是一个通过BigDecimal定义的最大值的字符串表示.小数存在精度
- @DecimalMin 被标注的值必须不小于约束中指定的最小值. 这个约束的参数是一个通过BigDecimal定义的最小值的字符串表示.小数存在精度
- @Digits 验证 Number 和 String 的构成是否合法
- @Digits(integer=,fraction=) 验证字符串是否是符合指定格式的数字，interger指定整数精度，fraction指定小数精度。
- @Range(min=, max=) 被指定的元素必须在合适的范围内
- @Range(min=10000,max=50000,message=”range.bean.wage”)
- @Valid 递归的对关联对象进行校验, 如果关联对象是个集合或者数组,那么对其中的元素进行递归校验,如果是一个map,则对其中的值部分进行校验.(是否进行递归验证)
- @CreditCardNumber信用卡验证
- @Email 验证是否是邮件地址，如果为null,不进行验证，算通过验证。
- @ScriptAssert(lang= ,script=, alias=)
- @URL(protocol=,host=, port=,regexp=, flags=)

## 多环境切换

profile是Spring对不同环境提供不同配置功能的支持，可以通过激活不同的环境版本，实现快速切换环境；

### 多配置文件

我们在主配置文件编写的时候，文件名可以是 application-{profile}.properties/yml , 用来指定多个环境版本；

**例如：**

application-test.properties 代表测试环境配置

application-dev.properties 代表开发环境配置

但是Springboot并不会直接启动这些配置文件，它**默认使用application.properties主配置文件**；

我们需要通过一个配置来选择需要激活的环境：

```properties
#比如在配置文件中指定使用dev环境，我们可以通过设置不同的端口号进行测试；#我们启动SpringBoot，就可以看到已经切换到dev下的配置了；spring.profiles.active=dev
```

### yaml的多文档块

选择激活哪一个环境

```properties
server:
  port: 8081
#选择要激活那个环境块
spring:  
  profiles:    
    active: prod
    
---
server:
  port: 8083
spring:  
  profiles: dev #配置环境的名称

---
server:  
  port: 8084
spring:  
  profiles: prod  #配置环境的名称
```

**注意：如果yml和properties同时都配置了端口，并且没有激活其他环境 ， 默认会使用properties配置文件的！**

springboot 启动会扫描以下位置的application.properties或者application.yml文件作为Spring boot的默认配置文件：

```xml
优先级1：项目路径下的config文件夹配置文件
优先级2：项目路径下配置文件
优先级3：资源路径下的config文件夹配置文件
优先级4：资源路径下配置文件
```

优先级由高到底，高优先级的配置会覆盖低优先级的配置；

### 拓展，运维小技巧

指定位置加载配置文件

我们还可以通过spring.config.location来改变默认的配置文件位置

项目打包好以后，我们可以使用命令行参数的形式，启动项目的时候来指定配置文件的新位置；

这种情况，一般是后期运维做的多，相同配置，外部指定的配置文件优先级最高

命令行代码

例：

~~~cmd
java -jar spring-boot-config.jar --spring.config.location=F:/application.properties
~~~

## 自动配置原理

### 分析

以**HttpEncodingAutoConfiguration（Http编码自动配置）**为例

~~~java

//表示这是一个配置类，和以前编写的配置文件一样，也可以给容器中添加组件；
@Configuration 

//启动指定类的ConfigurationProperties功能；
  //进入这个HttpProperties查看，将配置文件中对应的值和HttpProperties绑定起来；
  //并把HttpProperties加入到ioc容器中
@EnableConfigurationProperties({HttpProperties.class}) 

//Spring底层@Conditional注解
  //根据不同的条件判断，如果满足指定的条件，整个配置类里面的配置就会生效；
  //这里的意思就是判断当前应用是否是web应用，如果是，当前配置类生效
@ConditionalOnWebApplication(
    type = Type.SERVLET
)

//判断当前项目有没有这个类CharacterEncodingFilter；SpringMVC中进行乱码解决的过滤器；
@ConditionalOnClass({CharacterEncodingFilter.class})

//判断配置文件中是否存在某个配置：spring.http.encoding.enabled；
  //如果不存在，判断也是成立的
  //即使我们配置文件中不配置pring.http.encoding.enabled=true，也是默认生效的；
@ConditionalOnProperty(
    prefix = "spring.http.encoding",
    value = {"enabled"},
    matchIfMissing = true
)

public class HttpEncodingAutoConfiguration {
    //他已经和SpringBoot的配置文件映射了
    private final Encoding properties;
    //只有一个有参构造器的情况下，参数的值就会从容器中拿
    public HttpEncodingAutoConfiguration(HttpProperties properties) {
        this.properties = properties.getEncoding();
    }
    
    //给容器中添加一个组件，这个组件的某些值需要从properties中获取
    @Bean
    @ConditionalOnMissingBean //判断容器没有这个组件？
    public CharacterEncodingFilter characterEncodingFilter() {
        CharacterEncodingFilter filter = new OrderedCharacterEncodingFilter();
        filter.setEncoding(this.properties.getCharset().name());
        filter.setForceRequestEncoding(this.properties.shouldForce(org.springframework.boot.autoconfigure.http.HttpProperties.Encoding.Type.REQUEST));
        filter.setForceResponseEncoding(this.properties.shouldForce(org.springframework.boot.autoconfigure.http.HttpProperties.Encoding.Type.RESPONSE));
        return filter;
    }
    //。。。。。。。
}
~~~

**总结 ：根据当前不同的条件判断，决定这个配置类是否生效！**

- 一但这个配置类生效；这个配置类就会给容器中添加各种组件；
- 这些组件的属性是从对应的properties类中获取的，这些类里面的每一个属性又是和配置文件绑定的；
- 所有在配置文件中能配置的属性都是在xxxxProperties类中封装着；
- 配置文件能配置什么就可以参照某个功能对应的这个属性类

~~~java
//从配置文件中获取指定的值和bean的属性进行绑定
@ConfigurationProperties(prefix = "spring.http") 
public class HttpProperties {
    // .....
}
~~~

从spring.http中获取设置的配置

**总结：**

1、SpringBoot启动会加载大量的自动配置类

2、我们看我们需要的功能有没有在SpringBoot默认写好的自动配置类当中；

3、我们再来看这个自动配置类中到底配置了哪些组件；（只要我们要用的组件存在在其中，我们就不需要再手动配置了）

4、给容器中自动配置类添加组件的时候，会从properties类中获取某些属性。我们只需要在配置文件中指定这些属性的值即可；

**xxxxAutoConfigurartion：自动配置类；**给容器中添加组件

**xxxxProperties:封装配置文件中相关属性；**

### 了解：@Conditional

了解完自动装配的原理后，我们来关注一个细节问题，**自动配置类必须在一定的条件下才能生效；**

**@Conditional派生注解（Spring注解版原生的@Conditional作用）**

作用：必须是@Conditional指定的条件成立，才给容器中添加组件，配置配里面的所有内容才生效；

[![OSY9AJ.md.png](https://s1.ax1x.com/2022/04/30/OSY9AJ.md.png)](https://imgtu.com/i/OSY9AJ)

## SpringBoot Web开发

### 静态资源导入

总结：

1.在springboot，我们可以使用以下方式处理静态资源

- webjars  <!--localhost：8080/webjars/-->

- public，static，/**，resources      <!--localhost：8080/-->

  [![OSLExH.png](https://s1.ax1x.com/2022/04/30/OSLExH.png)](https://imgtu.com/i/OSLExH)

2.优先级：resources>static(默认)>public

### 首页和图标定制

源码：

```java
final class WelcomePageRouterFunctionFactory {

   private final String staticPathPattern;

	private final Resource welcomePage;

	private final boolean welcomePageTemplateExists;

	WelcomePageRouterFunctionFactory(TemplateAvailabilityProviders templateAvailabilityProviders,
			ApplicationContext applicationContext, String[] staticLocations, String staticPathPattern) {
		this.staticPathPattern = staticPathPattern;
		this.welcomePage = getWelcomePage(applicationContext, staticLocations);
		this.welcomePageTemplateExists = welcomeTemplateExists(templateAvailabilityProviders, applicationContext);
	}

	private Resource getWelcomePage(ResourceLoader resourceLoader, String[] staticLocations) {
		return Arrays.stream(staticLocations).map((location) -> getIndexHtml(resourceLoader, location))
				.filter(this::isReadable).findFirst().orElse(null);
	}

	private Resource getIndexHtml(ResourceLoader resourceLoader, String location) {
		return resourceLoader.getResource(location + "index.html");
	}
    ...
}
```

写入一个index.html就可以定制首页

**注意：**

虽然写入一个index.html就可以定制首页，但是在template目录下的所有页面，只能通过Controller来跳转，这个需要模板引擎的支持

在2.1.7版本在application.properties中关闭默认图标

```properties
spring.mvc.favicon.enabled = false
```

再导入图片名为favicon.ico即可设置图标

[![O94954.png](https://s1.ax1x.com/2022/05/01/O94954.png)](https://imgtu.com/i/O94954)

### ThyMeleaf

首先导入依赖

~~~xml
<dependency>
      <groupId>org.thymeleaf</groupId>
      <artifactId>thymeleaf-spring5</artifactId>
      <version>3.0.11.RELEASE</version>
      <scope>compile</scope>
    </dependency>
    <dependency>
      <groupId>org.thymeleaf.extras</groupId>
      <artifactId>thymeleaf-extras-java8time</artifactId>
      <version>3.0.4.RELEASE</version>
      <scope>compile</scope>
    </dependency>
~~~

必须在templates目录下并且以.html结尾的文件

网页需要导入约束

~~~html
<html lang="en" xmlns:th="http://www.thymeleaf.org"
      xmlns:sec="http://www.thymeleaf.org/extras/spring-security"
      xmlns:shiro="http://www.pollix.at/thymeleaf/shiro">
~~~

### SpringMVC拓展

~~~java
@Configuration
public class MyMvcConfig implements WebMvcConfigurer {
    //视图跳转
    @Override
    public void addViewControllers(ViewControllerRegistry registry){
        registry.addViewController("/kk").setViewName("KK");
    }
}
~~~

写入修改自己所需要的配置，SpringBoot会自动帮我们导入

**注意：**SpringBoot进行的是导入而不是替换

## 员工管理系统

### 首页实现

~~~java
    public void addViewControllers(ViewControllerRegistry registry){
        registry.addViewController("/").setViewName("index");
        registry.addViewController("/index.html").setViewName("index");
    }
~~~



### 国际化

中英文切换：

写入Properties

[![OCePBR.md.png](https://s1.ax1x.com/2022/05/01/OCePBR.md.png)](https://imgtu.com/i/OCePBR)

页面传入参数

~~~html
			<a class="btn btn-sm" th:href="@{/index.html(l='zh_CN')}">中文</a>
			<a class="btn btn-sm" th:href="@{/index.html(l='en_US')}">English</a>
~~~



书写地区解析器

~~~java
public class MyLocaleResolver implements LocaleResolver {
    //解析请求
    @Override
    public Locale resolveLocale(HttpServletRequest request) {
        String l = request.getParameter("l");;
        Locale locale = Locale.getDefault();
        //如果请求的链接携带了国际化的参数
        if (!StringUtils.isEmpty(l)){
            //zh_CN
            String[] s = l.split("_");
           locale =  new Locale(s[0],s[1]);
        }
        return locale;
    }
    @Override
    public void setLocale(HttpServletRequest request, HttpServletResponse response, Locale locale) {
    }
}
~~~

注册bean

~~~java
    @Bean
    public LocaleResolver localeResolver(){
        return new MyLocaleResolver();
    }
~~~

### 登录功能实现

Controller

~~~java
@Controller
public class LoginController {
    @RequestMapping("/user/login")
    public String login(@RequestParam("username") String username, @RequestParam("password") String password, Model model){
        //具体的业务
        if(!StringUtils.isEmpty(username)&&"123456".equals(password)){
            return "dashboard";
        }else{
            model.addAttribute("msg", "用户名或密码错误");
            return "index";
        }
    }
}
~~~

index页面实现

~~~html
            <!--如果msg不为空显示-->
			<p style="color: red" th:text="${msg}" th:if="${not #strings.isEmpty(msg)}"></p>
~~~

### 登录功能拦截器

LoginHandlerInterceptor.java

~~~java
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LoginHandlerInterceptor()).addPathPatterns("/**").excludePathPatterns("/index.html","/","/user/login","/css/*","/js/**","/img/**","/main.html");
    }
~~~

MyMvcConfig.java

~~~java
public class LoginHandlerInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        //登录成功之后，应该有用户的session
        Object loginUser = request.getSession().getAttribute("loginUser");
        if(loginUser==null){
            request.setAttribute("msg","没有权限，请先登录");
            request.getRequestDispatcher("/index.html").forward(request,response);
            return  false;
        }
        else {
            return true;
        }
        }
}
~~~

### 展示员工列表

如何实现点击后高亮？

传递一个参数后进行判断

~~~html
			<div th:replace="~{commons/commons.html::sidebar(active = 'main.html')}"></div>
~~~



~~~html
<!DOCTYPE html>
<!-- saved from url=(0052)http://getbootstrap.com/docs/4.0/examples/dashboard/ -->
<html lang="en" xmlns:th="http://www.thymeleaf.org"
	  xmlns:sec="http://www.thymeleaf.org/extras/spring-security"
	  xmlns:shiro="http://www.pollix.at/thymeleaf/shiro">

	<head>
		<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
		<meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
		<meta name="description" content="">
		<meta name="author" content="">

		<title>Dashboard Template for Bootstrap</title>
		<!-- Bootstrap core CSS -->
		<link th:href="@{/css/bootstrap.min.css}" rel="stylesheet">

		<!-- Custom styles for this template -->
		<link th:href="@{/css/dashboard.css}"  rel="stylesheet">
		<style type="text/css">
			/* Chart.js */
			
			@-webkit-keyframes chartjs-render-animation {
				from {
					opacity: 0.99
				}
				to {
					opacity: 1
				}
			}
			
			@keyframes chartjs-render-animation {
				from {
					opacity: 0.99
				}
				to {
					opacity: 1
				}
			}
			
			.chartjs-render-monitor {
				-webkit-animation: chartjs-render-animation 0.001s;
				animation: chartjs-render-animation 0.001s;
			}
		</style>
	</head>

	<body>
	<div th:replace="~{commons/commons.html::topbar}"></div>

		<div class="container-fluid">
			<div class="row">
				<div th:replace="~{commons/commons.html::sidebar(active = 'list.html')}"></div>

				<main role="main" class="col-md-9 ml-sm-auto col-lg-10 pt-3 px-4">
					<h2><a class="btn btn-sm btn-success" th:href="@{/emp}">添加员工</a></h2>
					<div class="table-responsive">
						<table class="table table-striped table-sm">
							<thead>
								<tr>
									<th>id</th>
									<th>lastName</th>
									<th>email</th>
									<th>gender</th>
									<th>department</th>
									<th>date</th>
									<th>操作</th>
								</tr>
							</thead>
							<tbody>
								<tr th:each="emp:${emps}">
									<td th:text="${emp.getId()}"></td>
									<td th:text="${emp.getLastName()}"></td>
									<td th:text="${emp.getEmail()}"></td>
									<td th:text="${emp.getGender()==0?'女':'男'}"></td>
									<td th:text="${emp.department.getDepartmentName()}"></td>
									<td th:text="${emp.getDate()}"></td>
									<td>
										<a class="btn btn-sm btn-primary" th:href="@{/emp/}+${emp.getId()}">编辑</a>
										<a class="btn btn-sm btn-primary" th:href="@{/emp/del/}+${emp.getId()}">删除</a>
									</td>
								</tr>
							</tbody>
						</table>
					</div>
				</main>
			</div>
		</div>

		<!-- Bootstrap core JavaScript
    ================================================== -->
		<!-- Placed at the end of the document so the pages load faster -->
		<script type="text/javascript" th:src="@{/js/jquery-3.2.1.slim.min.js}" ></script>
		<script type="text/javascript" th:src="@{js/popper.min.js}" ></script>
		<script type="text/javascript" th:src="@{/js/bootstrap.min.js}" ></script>


		<!-- Icons -->
		<script type="text/javascript" th:src="@{/js/feather.min.js}" ></script>
		<script>
			feather.replace()
		</script>

		<!-- Graphs -->
		<script type="text/javascript" th:src="@{/js/Chart.min.js}" ></script>
		<script>
			var ctx = document.getElementById("myChart");
			var myChart = new Chart(ctx, {
				type: 'line',
				data: {
					labels: ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"],
					datasets: [{
						data: [15339, 21345, 18483, 24003, 23489, 24092, 12034],
						lineTension: 0,
						backgroundColor: 'transparent',
						borderColor: '#007bff',
						borderWidth: 4,
						pointBackgroundColor: '#007bff'
					}]
				},
				options: {
					scales: {
						yAxes: [{
							ticks: {
								beginAtZero: false
							}
						}]
					},
					legend: {
						display: false,
					}
				}
			});
		</script>

	</body>

</html>
~~~

### 添加员工

~~~html
<!DOCTYPE html>
<!-- saved from url=(0052)http://getbootstrap.com/docs/4.0/examples/dashboard/ -->
<html lang="en" xmlns:th="http://www.thymeleaf.org"
      xmlns:sec="http://www.thymeleaf.org/extras/spring-security"
      xmlns:shiro="http://www.pollix.at/thymeleaf/shiro">

<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <meta name="description" content="">
    <meta name="author" content="">

    <title>Dashboard Template for Bootstrap</title>
    <!-- Bootstrap core CSS -->
    <link th:href="@{/css/bootstrap.min.css}" rel="stylesheet">

    <!-- Custom styles for this template -->
    <link th:href="@{/css/dashboard.css}"  rel="stylesheet">
    <style type="text/css">
        /* Chart.js */

        @-webkit-keyframes chartjs-render-animation {
            from {
                opacity: 0.99
            }
            to {
                opacity: 1
            }
        }

        @keyframes chartjs-render-animation {
            from {
                opacity: 0.99
            }
            to {
                opacity: 1
            }
        }

        .chartjs-render-monitor {
            -webkit-animation: chartjs-render-animation 0.001s;
            animation: chartjs-render-animation 0.001s;
        }
    </style>
</head>

<body>
<div th:replace="~{commons/commons.html::topbar}"></div>

<div class="container-fluid">
    <div class="row">
        <div th:replace="~{commons/commons.html::sidebar(active = 'list.html')}"></div>

        <main role="main" class="col-md-9 ml-sm-auto col-lg-10 pt-3 px-4">
            <h2><a class="btn btn-sm btn-success" th:href="@{/emp}"> 添加员工</a></h2>
            <div class="table-responsive">
                <form th:action="@{/emp}" method="post">
                    <div class="form-group">
                        <label>LastName</label>
                        <input type="text" class="form-control" placeholder="KeKe">
                    </div>
                    <div class="form-group">
                        <label>Email</label>
                        <input type="email" class="form-control" placeholder="670038154@qq.com">
                    </div>
                    <div class="form-group">
                        <label>Gender</label><br>
                        <div class="form-check form-check-inline">
                            <input class="form-check-input" type="radio" name="gender" value="1">
                            <label class="form-check-label">男</label>
                        </div>
                        <div class="form-check form-check-inline">
                            <input class="form-check-input" type="radio" name="gender" value="0">
                            <label class="form-check-label">女</label>
                        </div>
                    </div>
                    <div class="form-group">
                        <label>department</label>
                        <select class="form-control" name="department.id">
                            <option th:each="dept:${departments}" th:text="${dept.getDepartmentName()}" th:value="${dept.getId()}"></option>
                        </select>
                    </div>
                    <div class="form-group">
                        <label>Birth</label>
                        <input type="text" class="form-control" placeholder="嘤嘤嘤">
                    </div>
                    <button type="submit" class="btn btn-primary">添加</button>
                </form>
            </div>
        </main>
    </div>
</div>

<!-- Bootstrap core JavaScript
================================================== -->
<!-- Placed at the end of the document so the pages load faster -->
<script type="text/javascript" th:src="@{/js/jquery-3.2.1.slim.min.js}" ></script>
<script type="text/javascript" th:src="@{js/popper.min.js}" ></script>
<script type="text/javascript" th:src="@{/js/bootstrap.min.js}" ></script>


<!-- Icons -->
<script type="text/javascript" th:src="@{/js/feather.min.js}" ></script>
<script>
    feather.replace()
</script>

<!-- Graphs -->
<script type="text/javascript" th:src="@{/js/Chart.min.js}" ></script>
<script>
    var ctx = document.getElementById("myChart");
    var myChart = new Chart(ctx, {
        type: 'line',
        data: {
            labels: ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"],
            datasets: [{
                data: [15339, 21345, 18483, 24003, 23489, 24092, 12034],
                lineTension: 0,
                backgroundColor: 'transparent',
                borderColor: '#007bff',
                borderWidth: 4,
                pointBackgroundColor: '#007bff'
            }]
        },
        options: {
            scales: {
                yAxes: [{
                    ticks: {
                        beginAtZero: false
                    }
                }]
            },
            legend: {
                display: false,
            }
        }
    });
</script>

</body>

</html>
~~~

Controller

~~~java
    @GetMapping("/emp")
    public String toAddpage(Model model){
        Collection<Department> departments = departmentdao.getDepartments();
        model.addAttribute("departments",departments);
        return  "emp/add";
    }
    @PostMapping("/emp")
    public String addEmp(Employee employee){
        System.out.println("save"+employee);
        employeeDao.save(employee);
        return "redirect:/emps";
    }
~~~

**注：使用ThyMeleaf要注意加上th：**

### 修改员工

Controller

~~~java
    @GetMapping("/emp/{id}")
    public String toUpdateEmp(@PathVariable("id")Integer id,Model model){
        Employee employees = employeeDao.getEmployeeById(id);
        model.addAttribute("emps",employees);
        Collection<Department> departments = departmentdao.getDepartments();
        model.addAttribute("departments",departments);
        return "emp/update";
    }
    @PostMapping("/updateEmp")
    public String udpEmp(Employee employee){
        employeeDao.save(employee);
        return "redirect:/emps";
    }
~~~

update.html

~~~html
<!DOCTYPE html>
<!-- saved from url=(0052)http://getbootstrap.com/docs/4.0/examples/dashboard/ -->
<html lang="en" xmlns:th="http://www.thymeleaf.org"
      xmlns:sec="http://www.thymeleaf.org/extras/spring-security"
      xmlns:shiro="http://www.pollix.at/thymeleaf/shiro">

<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <meta name="description" content="">
    <meta name="author" content="">

    <title>Dashboard Template for Bootstrap</title>
    <!-- Bootstrap core CSS -->
    <link th:href="@{/css/bootstrap.min.css}" rel="stylesheet">

    <!-- Custom styles for this template -->
    <link th:href="@{/css/dashboard.css}"  rel="stylesheet">
    <style type="text/css">
        /* Chart.js */

        @-webkit-keyframes chartjs-render-animation {
            from {
                opacity: 0.99
            }
            to {
                opacity: 1
            }
        }

        @keyframes chartjs-render-animation {
            from {
                opacity: 0.99
            }
            to {
                opacity: 1
            }
        }

        .chartjs-render-monitor {
            -webkit-animation: chartjs-render-animation 0.001s;
            animation: chartjs-render-animation 0.001s;
        }
    </style>
</head>

<body>
<div th:replace="~{commons/commons.html::topbar}"></div>

<div class="container-fluid">
    <div class="row">
        <div th:replace="~{commons/commons.html::sidebar(active ='list.html')}"></div>

        <main role="main" class="col-md-9 ml-sm-auto col-lg-10 pt-3 px-4">
            <h2><a class="btn btn-sm btn-success" th:href="@{/emp}"> 添加员工</a></h2>
            <div class="table-responsive">
                <form th:action="@{/updateEmp}" method="post">
                    <input type="hidden" name="id" th:value="${emps.getId()}">
                    <div class="form-group">
                        <label>LastName</label>
                        <input th:value="${emps.getLastName()}" type="text" name="lastName"  class="form-control">
                    </div>
                    <div class="form-group">
                        <label>Email</label>
                        <input th:value="${emps.getEmail()}" type="email" name="email"  class="form-control">
                    </div>
                    <div class="form-group">
                        <label>Gender</label><br>
                        <div class="form-check form-check-inline">
                            <input th:checked="${emps.getGender()==1}" class="form-check-input" type="radio" name="gender" value="1">
                            <label class="form-check-label">男</label>
                        </div>
                        <div class="form-check form-check-inline">
                            <input th:checked="${emps.getGender()==0}" class="form-check-input" type="radio" name="gender" value="0">
                            <label class="form-check-label">女</label>
                        </div>
                    </div>
                    <div class="form-group">
                        <label>department</label>
                        <select class="form-control" name="department.id">
                            <option th:selected="${dept.getId()==emps.getDepartment().getId()}" th:each="dept:${departments}" th:text="${dept.getDepartmentName()}" th:value="${dept.getId()}"></option>
                        </select>
                    </div>
                    <div class="form-group">
                        <label>Birth</label>
                        <input th:value="${#dates.format(emps.getDate(),'yyyy-MM-dd')}" type="text" class="form-control">
                    </div>
                    <button type="submit" class="btn btn-primary">修改</button>
                </form>
            </div>
        </main>
    </div>
</div>


<!-- Bootstrap core JavaScript
================================================== -->
<!-- Placed at the end of the document so the pages load faster -->
<script type="text/javascript" th:src="@{/js/jquery-3.2.1.slim.min.js}" ></script>
<script type="text/javascript" th:src="@{js/popper.min.js}" ></script>
<script type="text/javascript" th:src="@{/js/bootstrap.min.js}" ></script>


<!-- Icons -->
<script type="text/javascript" th:src="@{/js/feather.min.js}" ></script>
<script>
    feather.replace()
</script>

<!-- Graphs -->
<script type="text/javascript" th:src="@{/js/Chart.min.js}" ></script>
<script>
    var ctx = document.getElementById("myChart");
    var myChart = new Chart(ctx, {
        type: 'line',
        data: {
            labels: ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"],
            datasets: [{
                data: [15339, 21345, 18483, 24003, 23489, 24092, 12034],
                lineTension: 0,
                backgroundColor: 'transparent',
                borderColor: '#007bff',
                borderWidth: 4,
                pointBackgroundColor: '#007bff'
            }]
        },
        options: {
            scales: {
                yAxes: [{
                    ticks: {
                        beginAtZero: false
                    }
                }]
            },
            legend: {
                display: false,
            }
        }
    });
</script>

</body>

</html>
~~~

### 删除员工

与修改员工大同小异，不再赘述

## 整合JDBC

数据库连接

~~~java
@Autowired
    DataSource dataSource;
~~~

想笑~~~

默认的数据源是hikari

时区报错加上

~~~java
serverTimezone=UTC
~~~

xxxxTemplate是SpringBoot已经配置好的bean

简易的增删改查

~~~java
@RestController
public class JDBCController {
    @Autowired
    JdbcTemplate jdbcTemplate;
    @Autowired
    DataSource dataSource;
    //查询数据库的所有信息
    @GetMapping("/userList")
    public List<Map<String,Object>> userList(){
        String sql = "select * from [student]";
        List<Map<String, Object>> list_map = jdbcTemplate.queryForList(sql);
        return list_map;
    }
    @GetMapping("/addUser")
    public String addUser(){
        String sql = "insert into [user](id,name,pwd) values(4,'小明',150329)";
        jdbcTemplate.update(sql);
        return "update-ok";
    }
    @GetMapping("/delUser")
    public String delUser(){
        String sql = "delete from [user] where id = 4";
        jdbcTemplate.update(sql);
        return "del-ok";
    }
    @GetMapping("/udpUser/{id}")
    public String udpUser(@PathVariable("id") int id){
        String sql = "update [user] set name = ?,pwd=? where id ="+id;
        Object[] objects = new Object[2];
        objects[0] = "小明2";
        objects[1] = "zzzzzz";
        jdbcTemplate.update(sql,objects);
        return "udp-ok";
    }
}
~~~

[![OVYfZd.png](https://s1.ax1x.com/2022/05/04/OVYfZd.png)](https://imgtu.com/i/OVYfZd)

**注意，SpringBoot的启动类要放在最外面，包含所有的类**

否则会出现404错误

## 整合Druid数据源

~~~yml
spring:
  datasource:
    username: sa
    password: 123
    url: jdbc:sqlserver://localhost;database=mybaits
    driver-class-name: com.microsoft.sqlserver.jdbc.SQLServerDriver
    type: com.alibaba.druid.pool.DruidDataSource
    initialSize: 5
    minIdle: 5
    maxActive: 20
    maxWait: 60000
    timeBetweenEvictionRunsMillis: 60000
    minEvictableIdleTimeMillis: 300000
    validationQuery: SELECT 1
    testWhileIdle: true
    testOnBorrow: false
    testOnReturn: false
    poolPreparedStatements: true
      #配置监控统计拦截的filters，stat:监控统计、log4j：日志记录、wall：防御sql注入
      #如果允许时报错  java.lang.ClassNotFoundException: org.apache.log4j.Priority
      #则导入 log4j 依赖即可，Maven 地址：https://mvnrepository.com/artifact/log4j/log4j
    filters: stat,wall,log4j
    maxPoolPreparedStatementPerConnectionSize: 20
    useGlobalDataSourceStat: true
    connectionProperties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=500
~~~

DruidConfigration

~~~java
package com.kk.config;

import com.alibaba.druid.pool.DruidDataSource;
import com.alibaba.druid.support.http.StatViewServlet;
import com.alibaba.druid.support.http.WebStatFilter;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.boot.web.servlet.FilterRegistrationBean;
import org.springframework.boot.web.servlet.ServletRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.servlet.Filter;
import javax.sql.DataSource;
import java.util.HashMap;
import java.util.Map;

@Configuration
public class DruidConfig {
    @ConfigurationProperties(prefix = "spring.datasource")
    @Bean
    public DataSource dataSource(){
        return new DruidDataSource();
    }
    //后台监控
    @Bean
    public ServletRegistrationBean statViewServlet(){
        ServletRegistrationBean<StatViewServlet> bean = new ServletRegistrationBean<>(new StatViewServlet(), "/druid/*");
        //后台需要有人登录，账号密码配置
        HashMap<String,String> init = new HashMap<>();
        //增加配置
        init.put("loginUsername", "admin");
        init.put("loginPassword", "123456");
        //允许谁可以访问
        init.put("allow", "localhost");
        bean.setInitParameters(init);
        return bean;
    }
    //filter
    @Bean
    public FilterRegistrationBean webStatFilter(){
        FilterRegistrationBean<Filter> bean = new FilterRegistrationBean<>();
        bean.setFilter(new WebStatFilter());
        Map<String, String> initParamters = new HashMap<>();
        initParamters.put("exclusions", "*.js,*.css,/druid/*");
        bean.setInitParameters(initParamters);
        return bean;
    }
}

~~~

## 整合Mybatis框架

总依赖：

~~~xml
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-web</artifactId>
            </dependency>
            <dependency>
                <groupId>com.microsoft.sqlserver</groupId>
                <artifactId>mssql-jdbc</artifactId>
                <version>8.1.0.jre8-preview</version>
            </dependency>
            <dependency>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
                <optional>true</optional>
            </dependency>
            <dependency>
                <groupId>org.mybatis.spring.boot</groupId>
                <artifactId>mybatis-spring-boot-starter</artifactId>
                <version>2.1.1</version>
            </dependency>
            <dependency>
                <groupId>com.alibaba</groupId>
                <artifactId>druid</artifactId>
                <version>1.2.9</version>
            </dependency>
            <dependency>
                <groupId>com.alibaba</groupId>
                <artifactId>druid-spring-boot-starter</artifactId>
                <version>1.2.9</version>
            </dependency>
            <!-- https://mvnrepository.com/artifact/log4j/log4j -->
            <dependency>
                <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
                <version>1.2.17</version>
            </dependency>
    </dependencies>
~~~

对应的Mapper映射文件

~~~xml

<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.kk.mapper.UserMapper">
</mapper>
~~~



资源过滤

~~~xml
<resources>
    <resource>
        <directory>src/main/java</directory>
        <includes>
            <include>**/*.xml</include>
        </includes>
        <filtering>true</filtering>
    </resource>
</resources>
~~~

application.properties

~~~properties
spring.datasource.username=sa
spring.datasource.password=123
spring.datasource.url=jdbc:sqlserver://localhost;database=mybaits
spring.datasource.driver-class-name=com.microsoft.sqlserver.jdbc.SQLServerDriver
mybatis.type-aliases-package=com.kk.springbootmybatis.pojo
mybatis.mapper-locations=classpath:Mybatis/mapper/*.xml
spring.datasource.type=com.alibaba.druid.pool.DruidDataSource
spring.datasource.druid.initial-size=5
spring.datasource.druid.min-idle=5
spring.datasource.druid.max-active=20
spring.datasource.druid.max-wait=60000
spring.datasource.druid.time-between-eviction-runs-millis=300000
spring.datasource.druid.validation-query=SELECT 1
spring.datasource.druid.test-while-idle=true
spring.datasource.druid.test-on-borrow=false
spring.datasource.druid.test-on-return=false
spring.datasource.druid.pool-prepared-statements=true
spring.datasource.druid.filters=stat,wall,log4j
spring.datasource.druid.max-pool-prepared-statement-per-connection-size=20
spring.datasource.druid.use-global-data-source-stat=true
spring.datasource.druid.connection-properties=druid.stat.mergeSql=true;druid.stat.slowSqlMillis=500
~~~

## SpringSecurity

（安全）

在web开发中，安全第一位！过滤器，拦截器

做网站：安全应该在什么时候考虑？在设计之初就应该进行考虑

因为忽略了安全就会：

- 漏洞，隐私泄露
- 架构一旦确定要改变很多代码

Shiro、SpringSecurity：很像~除了类不一样、名字不一样；

认证、授权 （vip1，2，3）

- 功能权限
- 访问权限
- 菜单权限（不同用户登录菜单不同）
- 拦截器、过滤器：会产生大量的原生代码~冗余 

~~~xml
        <dependency>
            <groupId>org.thymeleaf.extras</groupId>
            <artifactId>thymeleaf-extras-springsecurity5</artifactId>
            <version>3.0.4.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>
~~~

需要导入的两个Starter

~~~java
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
         http.authorizeHttpRequests()
                 .antMatchers("/").permitAll()//首页所有人可以进行访问
                 .antMatchers("/level1/**").hasRole("vip1")
                 .antMatchers("/level2/**").hasRole("vip2")
                 .antMatchers("/level3/**").hasRole("vip3");
         //没有权限会跳到登录页面
         http.formLogin();
    }
    //认证

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.inMemoryAuthentication()
                .withUser("kk").password("123456").roles("vip2","vip3")
                .and()
                .withUser("root").password("root").roles("vip1","vip2","vip3")
                .and()
                .withUser("guest").password("123456").roles("vip1");
    }
}
~~~

要是报错PasswordEncode

需要将密码进行加密

在SpringSecurity5.0以上新增了很多加密算法

new XXXpasswordencode（）就可以运行

但是密码一般从数据库中读取

我们所使用的方法不是常规方法不推荐学习
