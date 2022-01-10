---
title: Spring随笔-不完整
date: 2022-01-10 14:01:39
description: 由于重装系统了,所以很多截图都没了.太可惜了有机会在整理一下吧
tags:
      - Java
      - Spring
      - 后端
top_img: /img/Spring.jpeg
cover: /img/Spring.jpeg
categories: Spring开发随笔
keywords:
      - 配置文件
      - thymeleaf
      - 国际化配置
      - 注解类
      - 实例

---



Q@PropertySource

​	修改加载资源路径

@ConfigurationProperties(prefix="在全局中用什么名字来代表")

​	告诉SpringBoot将本类中的所有属性和配置文件进行绑定

@EnableAutoConfiguration:

​	利用EnableAutoConfigurationSelector给容器中导入一些组件

​	List<String>configurations = getCandidateConfiguration();获取候选的配置

```
SpringFactoriesLoader.loadFactoryNames()
扫描所有jar包下的META-INF/spring.factories
把扫描到的文件的内容包装成properties对象
从properties中获取EnableAutoConfiguration.class类对应的值然后把他们添加到容器中
```

将类路径下的META-INF/spring.factories里面的配置加入到EnableAutoConfiguration容器中

@Component

​	添加到容器中（只有添加到容器中才能和@ConfigurationProperties一起使用）

@ImportResource(locations = {"classpath:文件名"})

​	导入Spring的配置文件，让配置文件里面的内容生效，自己写的spring配置文件系统不识别，需要用这个导入才行

@Conditional根据不同的条件进行判断(如果@Conditional~~~也是判断的效果)

@Configuration指明一个类是配置类

@Bean将方法的返回值添加到容器中容器组件的id默认是方法名



# 占位符

​	配置文件中可以使用${random.value}、${random.int[(一个值)|[一个范围如123，456]]}、${random.long}

​	属性占位符

​	app.name = a

​	app.nname = ${app.name}

# Profile

### 	多Profile文件

​		主配置文件可以写成application-{Profile这里泛指文件环境名}.properties

​		默认使用application.properties的配置

### 	激活Profile文件

​		1.在application.properties中添加spring.profile.active = "Profile上面那个环境名"

​		2.用命令行配置--spring.profiles.active=环境

​		3.虚拟机配置-Dspring.profiles.active = 环境

### 	yml多文档块

​		在一个文件中用

```
---
```

​		来分割文档块

​		

​		profiles是指定不同的环境

​		active:环境.

​			可以开启不同的环境

# 配置文件的加载位置  

  ```
  -file:./config
  -file:./
  -classpath:/config/
  -classpath:/
  加载顺序按照这个来依次加载
  spring.config.location改变默认的配置文件位置
  在项目打包好的情况下使用命令行启动的时候添加文件位置会和原来默认位置的配置文件进行互补配置
  ```

  ```
命令行参数都是--

## 	外部配置加载顺序

			按照从高到低的优先级所有会形成互补配置

  ```

1.命令行 ,如:--server.port = 8087
优先加载带profile,在判断加载包外的到包内的
2.jar包外的application-{profile}.properties或application.yml(带spring.profile)配置文件
3.jar包内的application-{profile}.properties或application.yml(带spring.profile)配置文件
4.jar包外的application.properties或application.yml(不带spring.profile)配置文件
5.jar包内的application.properties或application.yml(不带spring.profile)配置文件
6.@Configuration注解类上的@PropertySource
7.通过SpringApplication.setDefaultProperties指定的默认属性

  ```
# springboot自动加载

​	![1595494900657](C:\Users\lll\AppData\Local\Temp\1595494900657.png)

*****AutoConfiguration:自动配置类

****Properties:封装配置文件中的相关属性

![1595495801212](C:\Users\lll\AppData\Local\Temp\1595495801212.png)

配置文件下设置debug = true

可以显示都那些类成功自动配置

# 日志

```java
Logger logger = LoggerFactory.getLogger(getClass());
	    logger.trace("这是trace日志");
        logger.debug("这是debug日志");
        logger.info("这是info");
        logger.warn("这是warn");
        logger.error("这是error");
设置不同等级的信息
logging.level.com.example.threedemo.threedemo = trace配置显示的等级
logging.file=spring.log将日志写入这个文件(可以指定路径)
logging.path = /spring/log指定目录

//格式建议百度搜索样式
logging.pattern.console=控制台输出的格式
logging.pattern.file=文件中日志输出的格式
  ```

日志配置文件采用的时.xml的格式放到resource下

logback.xml会被框架识别

如果采用logback-spring.xml会被springboot识别

​	<springProfile  name = "环境名">

​		<pattern>格式</pattern>

​	</springProfile>

​	这样会在特定环境下使用这个格式,如果环境名前面加上!则会在非该环境下使用

日志转换

​	pom.xml里面添加

​	***AutoConfiguration类:给我们的容器中自动配置组件

​	***Properties类:封装配置文件内容 就是那些类进行绑定

### SpringBoot对静态资源的规则

```java
@Override
public void addResourceHandlers(ResourceHandlerRegistry registry) {
   if (!this.resourceProperties.isAddMappings()) {
      logger.debug("Default resource handling disabled");
      return;
   }
   Duration cachePeriod = this.resourceProperties.getCache().getPeriod();
   CacheControl cacheControl = this.resourceProperties.getCache().getCachecontrol().toHttpCacheControl();
   if (!registry.hasMappingForPattern("/webjars/**")) {
      customizeResourceHandlerRegistration(registry.addResourceHandler("/webjars/**")
            .addResourceLocations("classpath:/META-INF/resources/webjars/")
            .setCachePeriod(getSeconds(cachePeriod)).setCacheControl(cacheControl));
   }
   String staticPathPattern = this.mvcProperties.getStaticPathPattern();
   if (!registry.hasMappingForPattern(staticPathPattern)) {
      customizeResourceHandlerRegistration(registry.addResourceHandler(staticPathPattern)
            .addResourceLocations(getResourceLocations(this.resourceProperties.getStaticLocations()))
            .setCachePeriod(getSeconds(cachePeriod)).setCacheControl(cacheControl));
   }
}

private Integer getSeconds(Duration cachePeriod) {
   return (cachePeriod != null) ? (int) cachePeriod.getSeconds() : null;
}
```

​	上面这段代码在WebMvcAutoConfiguration类里面的addResourceHandlers方法中记录了从那里获取静态资源

​	由于上面的代码路径是classpath:/META-INF/resources/webjars/所以以后的访问

​	http://localhost:端口/webjars/**(所有东西)都去这个路径下面找

​	访问时需要引入jQuery的依赖去<https://www.webjars.org/>找访问时只需要填写resource下面的名称即可

```java
@ConfigurationProperties(prefix = "spring.resources", ignoreUnknownFields = false)
public class ResourceProperties 
//可以在这里设置和静态资源有关的参数
"classpath:/META-INF/resources/",     
"classpath:/resources/", 
"classpath:/static/",
"classpath:/public/" 
"/"如果找不到资源去这些文件夹下面找
但是所有的欢迎页静态资源下的所有的index.html被"/**"映射
所以localhost:8080会找index.html
**/favicon.ico网页的图标
修改默认路径spring.resource的路径
```

## 模板引擎

如JSP、Velocity、Freemarker、Thymeleaf

Thymeleaf

语法简单，功能更强大



用来切换版本

启用Thymeleaf后就可以自动在classpath：//templates/查找html文件

如发送

```java
@RequestMapping("/success")

public String success(){

	return "success";

}

```

Thymeleaf语法

1.使用前在html中导入Thymeleaf的命名空间

```html
<html xmlns:th = "http://www.thymeleaf.org">
语法提示
```

2.使用语法

在标签里面加属性来调用语法如

```html
<div th:text = "${hello}"></div>
```

语法规则

1.

th:任意属性如th:id、th:class

th：insert、th：replace相当于include

th：each遍历

th：if、unless、switch、case判断

th：object、with 设置变量

th：attr、attrprepend、attrapped可以修改任意属性

th：属性

th：text修改标签里面的文本内容、utext不转义特殊字符

th：fragment声明片段

2.表达式

 ```properties
Simple expressions:(表达式语法)
    Variable Expressions: ${...}获取变量值OGNL
    	1.获取属性
    	2.内置属性 
    		#ctx : the context object.
            #vars: the context variables.
            #locale : the context locale.
            #request : (only in Web Contexts) the HttpServletRequest object.
            #response : (only in Web Contexts) the HttpServletResponse object.
            #session : (only in Web Contexts) the HttpSession object.
            #servletContext : (only in Web Contexts) the ServletContext object.
		如Established locale country: <span th:text="${#locale.country}">US</span>.
		3.内置的工具对象
		#execInfo : information about the template being processed.
        #messages : methods for obtaining externalized messages inside variables expressions, in the same way as they
        would be obtained using #{…} syntax.
        #uris : methods for escaping parts of URLs/URIs
        Page 20 of 106
        #conversions : methods for executing the configured conversion service (if any).
        #dates : methods for java.util.Date objects: formatting, component extraction, etc.
        #calendars : analogous to #dates , but for java.util.Calendar objects.
        #numbers : methods for formatting numeric objects.
        #strings : methods for String objects: contains, startsWith, prepending/appending, etc.
        #objects : methods for objects in general.
        #bools : methods for boolean evaluation.
        #arrays : methods for arrays.
        #lists : methods for lists.
        #sets : methods for sets.
        #maps : methods for maps.
        #aggregates : methods for creating aggregates on arrays or collections.
        #ids : methods for dealing with id attributes that might be repeated (for example, as a result of an iteration).
    Selection Variable Expressions: *{...}变量选择表达式和${}功能上一样
    	配合th:object="${session.user}"进行使用后可以直接*{firstName}调用th:object的值
    	如
    	<div th:object="${session.user}">
        <p>Name: <span th:text="*{firstName}">Sebastian</span>.</p>
        <p>Surname: <span th:text="*{lastName}">Pepper</span>.</p>
        <p>Nationality: <span th:text="*{nationality}">Saturn</span>.</p>
        </div>

    Message Expressions: #{...}获取国际化内容
    Link URL Expressions: @{...}定义url链接
            <!-- Will produce 'http://localhost:8080/gtvg/order/details?orderId=3' (plus rewriting) -->
        <a href="details.html"
        th:href="@{http://localhost:8080/gtvg/order/details(orderId=${o.id})}">view</a>
        <!-- Will produce '/gtvg/order/details?orderId=3' (plus rewriting) -->
        <a href="details.html" th:href="@{/order/details(orderId=${o.id})}">view</a>
        <!-- Will produce '/gtvg/order/3/details' (plus rewriting) -->
        <a href="details.html" th:href="@{/order/{orderId}/details(orderId=${o.id})}">view</a
        
        
    Fragment Expressions: ~{...}引用表达式
    
Literals:(字面量)
    Text literals: 'one text' , 'Another one!' ,…
    Number literals: 0 , 34 , 3.0 , 12.3 ,…
    Boolean literals: true , false
    Null literal: null
    Literal tokens: one , sometext , main ,…
Text operations:(文本操作)
    String concatenation: +
    Literal substitutions: |The name is ${name}|
Arithmetic operations:(数学运算)
    Binary operators: + , - , * , / , %
	Minus sign :(unary operator): -	
Boolean operations:(布尔运算)
    Binary operators: and , or
    Boolean negation (unary operator): ! , not
Comparisons and equality:(比较运算)
    Comparators: > , < , >= , <= ( gt , lt , ge , le )
    Equality operators: == , != ( eq , ne )
Conditional operators:(条件运算)
    If-then: (if) ? (then)
    If-then-else: (if) ? (then) : (else)
    Default: (value) ?: (defaultvalue)
Special tokens:(没有操作)

	No-Operation:_
 ```

标签内容里面写th的话用[[thy语法]]



## springboot对springMVC的自动配置

##### 	1.ContentNegotiatingViewResolver和BeanNameViewResolver的组件

​	自动配置了ViewResolver(视图解析器:根据方法返回值并得到视图对象(View),视图对象决定如何渲染(转发?重定向?))

​	ContentNegotiatingViewResolver:用来组合所有的视图解析器

```
我们可以给容器中添加一个视图解析器;自动的将其组合进来
public static class myViewResolver implements ViewResolver{
        @Override
        public View resolveViewName(String s, Locale locale) throws Exception {
            return null;
        }
    }
```

##### 	2.静态资源文件夹(Webjars)

##### 	3.自动注册了Converter,GenericConverter,Formatter

​	Converter:对页面提供的数据能够自动转换(数据一一对应)

​	GenericConverter:

​	Formatter:格式化器(按照一定的格式来转化如2017.12.17==Date类型)

​	`自己添加格式化器和转换器只需要放在容器中即可和上面的视图转换器应该差不多`

​	

```
HttpMessageConverter用来彖传请求和响应的;例如一个User对象转换为json
	想要添加HttpMessageConverter只需要将自己的组件注册到容器中(@Bean,@Component)
```



MessageCodeResolver定义错误代码生成规则

ConfigurationWebBindingInitalizer:可以自己创建一个来替换默认的

用来初始化WebDataBinder的(请求的数据和那个组件进行绑定)

!

使用WebMvcConfigurationSupport扩展spring MVC的功能

```java
package com.example.fourdemo.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.ViewControllerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurationSupport;
@Configuration
public class MyMvcSupport extends WebMvcConfigurationSupport {

        @Override
        protected void addViewControllers(ViewControllerRegistry registry) {
//            super.addViewControllers(registry);
            //浏览器发送atguigu请求
            registry.addViewController("/index").setViewName("/hello");
        }

}
```

```
如果用了要自己配置所有的springMVC 可以添加@EnableWebMvc来直接全面接管springMVC(所有的自动配置都失效)

不推荐
```



如果用server.context-path修改路径想要自动添加修改html的链接路径要用th:属性 = "@{}"进行修改



# 页面国际化



配置国际化文件SpringBoot自动配置好国际化资源组件

配置文件可以直接放在类路径下叫做message.properties

spring.message.basename=i18n.login(按照上面的图为例)设置properties

##### 还记得#{}这个就是获取国际化信息的

对称标签的text用

没有对称标签的text用

文件如果加载失败会显示这样结果

***LocaleResolver获取区域信息类

实现切换功能就要用一个类来继承LocaleResolver这样可以接收到区域信息的请求



```java
public class MyLocaleResolver implements LocaleResolver {
    @Override
    public Locale resolveLocale(HttpServletRequest request) {
        String l = request.getParameter("l");
        //获得网页默认的语言
        Locale locale = Locale.getDefault();
        //判断请求是否为空
        if(!l.isEmpty())
        {
            String []split = l.split("_");
            //第一个参数是语言信息第二个国家信息
            locale = new Locale(split[0],split[1]);
        }
        return locale;
    }

    @Override
    public void setLocale(HttpServletRequest request, HttpServletResponse response, Locale locale) {

    }
}
```

创建完类还要添加到组件中这样才能替换自动配置的组件

注意组件名必须为localeResolver



## 请求处理

@DeleteMapping|@PutMapping|@GetMapping|@PostMapping|@RequestMapping

@RequestParam：将请求参数绑定到你控制器的方法参数上（是springmvc中接收普通参数的注解）

对于@PostMapping可以用@RequestParam来指定形参想要接收到的数据,形参里面放一个Map可以返回设置返回数据

```java
@PostMapping(value = "/user/login")
    public String login(@RequestParam("username") String username,
                        @RequestParam("password") String password,
                        Map<String,Object> map
    ){
        if(!StringUtils.isEmpty(username)&&username.equals("123456")&&password.equals("123456"))
        {
            return "dashboard";
        }
        else {
            map.put("message","用户名或密码错误");
            return "index";
        }
    }
```

而html来处理返回的数据可以先用th:if来判断是否存在数据然后用th:text来设置值如果th:if不生效则p不会生效

```html
<p style="color: red" th:text="${message}" th:if="${not #strings.isEmpty(message)}"></p>
```

##### 为了防止表单重复提交可以使用重定向"redirect:/";但是还需要一个拦截器来防止随便复制地址就能登陆的问题



### thymeleaf公共片段抽取(就是将页面重复的元素抽取出来)



```html
<--th:fragment = "name"将元素抽取为名为name的片段如:-->
<div th:fragment="copy">
    sadfkjahsdfkjahsdfk
   </div>
引用公共片段
<--1.th:insert="~{templatename(网页名称) :: selector(选择器)(后边加个括号可以写创建的参数如图1)}"或"~{templatename::fragmentname}"-->会在一个标签内部插入一个元素
<--2.th:replace = "~{templatename(网页名称) :: selector(选择器)}"或"~{templatename::fragmentname}"-->将声明引入的标签替换成引用的元素
<--3.th:include="~{templatename(网页名称) :: selector(选择器)}"或"~{templatename::fragmentname}"-->把引入的内容插入到标签中不引入最外层标签
    
行内写法
    [[~{}]];
```



```html
循环读取出元素数据
<tbody>
    <tr th:each="emp:${emps}">
        <td th:text="${emp.getId()}"></td>
        <td th:text="${emp.getLastName()}"></td>
        <td th:text="${emp.getEmail()}"></td>
        <td th:text="${emp.getGender()==0?'女':'男'}"></td>
        <td th:text="${emp.getDepartment()}"></td>
        <!--									利用dates的系统静态方法来格式化日期-->
        <td th:text="${#dates.format(emp.birth,'yyyy-mm-dd HH:mm')}"></td>
    </tr>
</tbody>
```

##### 对于Post请求方法想要获得参数必须将表单名字一一进行对应,并且如果是对象参数的话,对象属性要和表单名字一一对应才能实现自动传参的功能



##### 修改日期的格式spring.mvc.date-format = yyyy/mm/dd(例子)





## springBoot的错误处理机制

##### 默认效果:

​	1.PC端返回一个错误页面

​	2.客户端,返回一个json数据

##### 定制错误响应

​	系统自带的ErrorMvcAutoConfiguration错误处理的自动配置添加了下面这些组件

​		1.DefaultErrorAttributes帮助我们在页面共享信息

​		2.BasicErrorController处理默认的error请求

​		3.ErrorPageCustomizer系统出现错误后来到error请求进行处理

​		4.DefaultErrorViewResolver默认springboot会找到某个页面

​	出错步骤:

​		发生4xx或者5xx错误先启动ErrorPageCustomizer会生效,然后来到/error请求



​	定制错误响应：

​		1.如何定制错误页面

​			（1）有模板引擎（模板引擎一般在template下）的情况下error/404.html

​			（2）没有模板引擎放到静态资源下

​			（3）以上都没有、默认来到springboot的空白页面

​			可以使用4xx和5xx会自动匹配这种类型的错误，如果有精确到404等这样的页面就先寻找这样的页面

​		2.页面共享的能获取的信息：

​			timestamp：时间戳

​			status：状态码

​			error：错误提示

​			exception：异常

​			message：异常消息

​			errors：jsr303数据校验错误都在这里

```java
浏览器和其他的都是json返回

    @ResponseBody
    @ExceptionHandler(MyException.class)
    public Map<String,Object> handleException(Exception e){
        Map<String,Object> map = new HashMap<>();
        map.put("code","user.notexist");
        map.put("message",e.getMessage());
        return map;
    }



自己定制错误页面的json
	
这句代码意思是实现一个自己的异常处理器然后设置状态码并进行页面的跳转但是不显示自己的map数据
    @ExceptionHandler(MyException.class)
    public String handleException(Exception e, HttpServletRequest request){
        //设置自己的状态码
        request.setAttribute("javax.servlet.error.status_code",400);
        Map<String,Object> map = new HashMap<>();
        map.put("code","user.notexist");
        map.put("message",e.getMessage());
        return "forward:/error";
    }
由于这个代码
@ConditionalOnMissingBean(value = ErrorAttributes.class)
所以当容器中没有ErrorAttributes时就会自动添加一个ErrorAttributes
```

# 配置嵌入式Servlet容器

springBoot默认使用Tomcat作为嵌入式的servlet容器

1、如何定制和修改servlet容器的相关配置

​	修改和server相关的配置

```properties
server.port = 8081
//通用的servlet容器配置
server.xxx
//如Tomcat设置
server.tomcat.xxx
```

​	编写一个WebServerFactoryCustomizer<ConfigurableWebServerFactory>:嵌入式的servlet容器制定器来修改servlet容器的配置

```java
@Bean
    public WebServerFactoryCustomizer<ConfigurableWebServerFactory> webServerFactoryCustomizer(){
        return new WebServerFactoryCustomizer<ConfigurableWebServerFactory>() {
            @Override
            public void customize(ConfigurableWebServerFactory factory) {
                factory.setPort(8081);
            }
        };
    }
```





​	注册Spring的三大组件

​		1.ServletRegistrationBean

```java
//注册Servlet
public class MyServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {


        doPost(req,resp);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        
        resp.getWriter().write("Hello");
    }
}
//注册到@Bean中
 @Bean
    public ServletRegistrationBean myServlet(){
        ServletRegistrationBean<Servlet> servletServletRegistrationBean = new ServletRegistrationBean<>(new MyServlet(),"/MyServlet");

        return servletServletRegistrationBean;

    }
```

​		2.FilterRegistrationBean

```java
//注册Filter
public class MyFilter implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {

    }

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {

    }

    @Override
    public void destroy() {

    }
}

//添加到Bean中
@Bean
    public FilterRegistrationBean filterRegistrationBean(){
        FilterRegistrationBean<Filter> filterFilterRegistrationBean = new FilterRegistrationBean<>();

        filterFilterRegistrationBean.setUrlPatterns(Arrays.asList("/hello","/myServlet"));
        filterFilterRegistrationBean.setFilter(new MyFilter());
        return filterFilterRegistrationBean;

    }
```

​		3.ServletListenerRegistrationBean

```java
//注册
public class MyListener implements ServletContextListener{
    @Override
    public void contextInitialized(ServletContextEvent sce) {
        System.out.println("Initialized");
    }

    @Override
    public void contextDestroyed(ServletContextEvent sce) {
        System.out.println("Destroyed");
    }

}

//添加到Bean中
@Bean
    public ServletListenerRegistrationBean<MyListener> myRegistration(){
        ServletListenerRegistrationBean<MyListener> registrationBean = new ServletListenerRegistrationBean<MyListener>(new MyListener());
        return registrationBean;
    }
```

## /*或拦截jsp请求但是/不会

可以通过server.servletPath来修改springMVC前端控制器默认拦截的请求路径

2、能否支持其他的servlet容器

​	在pom.xml里面添加要依赖的容器即可

​	默认支持Tomcat

​			Jetty

​			Undertow

嵌入式Servlet容器自动配置原理;

```java
//这个类表示决定采用哪种servlet容器重点在于ConditionalOnClass

@Configuration(proxyBeanMethods = false)
@ConditionalOnWebApplication
@EnableConfigurationProperties(ServerProperties.class)
public class EmbeddedWebServerFactoryCustomizerAutoConfiguration {

	/**
	 * Nested configuration if Tomcat is being used.
	 */
	@Configuration(proxyBeanMethods = false)
	@ConditionalOnClass({ Tomcat.class, UpgradeProtocol.class })
	public static class TomcatWebServerFactoryCustomizerConfiguration {

		@Bean
		public TomcatWebServerFactoryCustomizer tomcatWebServerFactoryCustomizer(Environment environment,
				ServerProperties serverProperties) {
			return new TomcatWebServerFactoryCustomizer(environment, serverProperties);
		}

	}

	/**
	 * Nested configuration if Jetty is being used.
	 */
	@Configuration(proxyBeanMethods = false)
	@ConditionalOnClass({ Server.class, Loader.class, WebAppContext.class })
	public static class JettyWebServerFactoryCustomizerConfiguration {

		@Bean
		public JettyWebServerFactoryCustomizer jettyWebServerFactoryCustomizer(Environment environment,
				ServerProperties serverProperties) {
			return new JettyWebServerFactoryCustomizer(environment, serverProperties);
		}

	}

	/**
	 * Nested configuration if Undertow is being used.
	 */
	@Configuration(proxyBeanMethods = false)
	@ConditionalOnClass({ Undertow.class, SslClientAuthMode.class })
	public static class UndertowWebServerFactoryCustomizerConfiguration {

		@Bean
		public UndertowWebServerFactoryCustomizer undertowWebServerFactoryCustomizer(Environment environment,
				ServerProperties serverProperties) {
			return new UndertowWebServerFactoryCustomizer(environment, serverProperties);
		}

	}

	/**
	 * Nested configuration if Netty is being used.
	 */
	@Configuration(proxyBeanMethods = false)
	@ConditionalOnClass(HttpServer.class)
	public static class NettyWebServerFactoryCustomizerConfiguration {

		@Bean
		public NettyWebServerFactoryCustomizer nettyWebServerFactoryCustomizer(Environment environment,
				ServerProperties serverProperties) {
			return new NettyWebServerFactoryCustomizer(environment, serverProperties);
		}

	}

}
```



# 注解类



#### @Order

springsecurity中注解去调整自定义过滤器在过滤器链中的位置。



#### @Mapper

将结构托管给springboot并变成实现类

```java
@Mapper
public interface UserDAO {
   //代码
}

```

#### @MapperScan

指定要变成实现类的接口所在的包，然后包下面的所有接口在编译之后都会生成相应的实现类

```java
@SpringBootApplication
@MapperScan("com.winter.dao")
public class SpringbootMybatisDemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringbootMybatisDemoApplication.class, args);
    }
}

```



#### @Controller(name="")

```
/**
 * 给组件重新定义一个名字如果不为空的话
 */
```



#### @PathVariable

```
@PathVariable("xxx")
通过 @PathVariable 可以将URL中占位符参数{xxx}绑定到处理器类的方法形参中@PathVariable(“xxx“) 
 
@RequestMapping(value=”user/{id}/{name}”)
请求路径：http://localhost:8080/hello/show5/1/james
```



@ModelAttribute

> `@ModelAttribute`注解注释的方法会在此controller每个方法执行前被执行

```java
@Controller
public class HelloWorldController {

    @ModelAttribute
    public void populateModel(@RequestParam String abc, Model model) {
       model.addAttribute("attributeName", abc);
    }

    @RequestMapping(value = "/helloWorld")
    public String helloWorld() {
       return "helloWorld";
    }
}
```



#### @Resource

```
@Resource和@Autowired注解都是用来实现依赖注入的。只是@AutoWried按by type自动注入，而@Resource默认按byName自动注入。

@Resource有两个重要属性，分别是name和type

spring将name属性解析为bean的名字，而type属性则被解析为bean的类型。所以如果使用name属性，则使用byName的自动注入策略，如果使用type属性则使用byType的自动注入策略。如果都没有指定，则通过反射机制使用byName自动注入策略。
```

#### @Target({ElementType.TYPE})说明该注解只能用在接口或者类和enum上

#### @Retention(RetentionPolicy.RUNTIME)说明是运行注解

#### @Documented将注解添加到doc中

#### @Inherited:可以被继承

#### @SpringBootConfiguration:springboot的配置注解

#### @EnableAutoConfiguration:启用自动注解功能

#### @ComponentScan:组件的自动扫描



#### @AutoConfigureOrder

​	代表启动的加载顺序参数越小越早加载

​	@AutoConfigureOrder(Ordered.HIGHEST_PRECEDENCE)



#### @AutoConfigureAfter

在...之后加载例如

@AutoConfigureAfter(ServletWebServerFactoryAutoConfiguration.class)这样就表示当使用了该注释要在ServletWebServerFactoryAutoConfiguration.class之后加载该类



#### @EnableConfigurationProperties

https://www.jianshu.com/p/7f54da1cb2eb

 @EnableConfigurationProperties 相当于把使用 @ConfigurationProperties 的类进行了一次注入

当`@EnableConfigurationProperties`注解应用到你的`@Configuration`时， 任何被`@ConfigurationProperties`注解的beans将自动被Environment属性配置



说白了根据配置文件来初始化这个类

例如@EnableConfigurationProperties(WebMvcProperties.class)

而WebMvcProperties源码为

```java
@ConfigurationProperties(prefix = "spring.mvc")
public class WebMvcProperties {
```

该类会根据配置文件的spring.mvc来进行装配



### @Conditional

#### @ConditionalOnWebApplication(type = Type.SERVLET)

通过源码可以查看

```java
@Target({ ElementType.TYPE, ElementType.METHOD })
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Conditional(OnWebApplicationCondition.class)
public @interface ConditionalOnWebApplication {
    
	/**
	 * The required type of the web application.
	 * @return the required web application type
	 */
	Type type() default Type.ANY;

	/**
	 * Available application types.
	 */
	enum Type {

		/**
		 * Any web application will match.
		 */
		ANY,

		/**
		 * Only servlet-based web application will match.
		 */
		SERVLET,

		/**
		 * Only reactive-based web application will match.
		 */
		REACTIVE

	}

}
```

实际上就是一个Conditional就是当OnWebApplicationCondition加载时才会生效

@Conditional:只有满足一些列条件之后创建一个bean。

而内部是一个枚举遍历代表有着三种环境默认是ANY,而匹配web application才会是SERVLET



#### @ConditionalOnClass

判断当前classpath是否存在指定类，若存在则实例化被注释的类

例如@ConditionalOnClass(DispatcherServlet.class)



#### @ConditionalOnMissingBean

检测bean是否注册如果注册就不会注册重复的bean，如果不存在检测的bean则该注解进行注解的类有效



# extend SpringBootServletInitializer 

```java
@Override
    protected SpringApplicationBuilder configure(SpringApplicationBuilder application) {
        return application.sources(XXXApplication】.class);
    }
```

springboot项目，若打包成war包，使用外置的tomcat启动

1、需要继承 org.springframework.boot.context.web.SpringBootServletInitializer类

2、然后重写configure(SpringApplicationBuilder application)方法
因为我们的项目是打成war包，然后部署到tomcat的~(还延续了mvc的方式)







# 整理文章

### springboot 启动类Application 扫盲（继承SpringBootServletInitializer作用）

https://blog.csdn.net/pmdream/article/details/96434028



### Spring Boot快速入门

https://blog.didispace.com/spring-boot-learning-1/



### springboot配置文件

https://blog.didispace.com/springbootproperties/

在命令行运行时，连续的两个减号`--`就是对`application.properties`中的属性值进行赋值的标识。所以，`java -jar xxx.jar --server.port=8888`命令，等价于我们在`application.properties`中添加属性`server.port=8888`，该设置在样例工程中可见，读者可通过删除该值或使用命令行来设置该值来验证。



### springboot对MVC自动装配



https://docs.spring.io/spring-boot/docs/2.5.4/reference/htmlsingle/#features.spring-application

①自动配置了视图解析器
②静态资源文件处理
③自动注册了大量的转换器和格式化器
④提供HttpMessageConverter对请求参数和返回结果进行处理
⑤自动注册了MessageCodesResolver
⑥默认欢迎页配置
⑦可配置的Web初始化绑定器



对于自动装配类WebMvcAutoConfiguration

```java
@Configuration(proxyBeanMethods = false)
@ConditionalOnWebApplication(type = Type.SERVLET)
@ConditionalOnClass({ Servlet.class, DispatcherServlet.class, WebMvcConfigurer.class })
@ConditionalOnMissingBean(WebMvcConfigurationSupport.class)
@AutoConfigureOrder(Ordered.HIGHEST_PRECEDENCE + 10)
@AutoConfigureAfter({ DispatcherServletAutoConfiguration.class, TaskExecutionAutoConfiguration.class,
		ValidationAutoConfiguration.class })
public class WebMvcAutoConfiguration {
```

该类是被注册为一个配置文件,并且在SERVLET环境满足Servlet、DispatcherServlet、WebMvcConfigurer类被装配且不存在WebMvcConfigurationSupport类的情况下下才会生效而且要在DispatcherServletAutoConfiguration，TaskExecutionAutoConfiguration，ValidationAutoConfiguration类之后加载,



### 视图解析器

用来将逻辑视图转换为物理视图并且所有视图解析器都要继承ViewResolver接口，SpringMVC的上下文配置可以指定他们的顺序，每种策略对应着一个实体类，这样开发者可以混用多个视图解析器并指定先后顺序，直到视图解析并返回视图。





### web应用1

https://blog.didispace.com/springbootweb/



## DispatcherServlet



这是SpringMVC中可见DispatcherServlet的重要性而SpringBoot是将Spring进行自动装配的升阶版







# Springboot+vue全栈

### 启动类的介绍

@SpringBootApplication是一个@Configuration所以是一个配置类可以在该类中@EnableAutoConfiguration标识开启自动化配置@ComponentScan扫描包并把默认扫描的类都位于当前类所在包的下面

虽然启动类上面包含@Configuration注解但是开发者可以创建一个新的类来配置bean

@ComponentScan除了扫描@Service、@Repository、@Component、@Controller和@RestController还会扫描@Configuration



> 我们可以在resources下面创建banner.txt来打印启动时的TXT艺术字

如果想要关闭在main中写入下面代码

```java
        SpringApplicationBuilder builder = new SpringApplicationBuilder(JavaProjectApplication.class);
        builder.bannerMode(Banner.Mode.OFF).run(args);
```



### 基本的配置文件

application.properties

```properties
#端口
server.port=8081
#错误后映射的地址
server.error.path=ierror
#session的超时m代表分支默认秒
server.servlet.session .timeout=30m
#项目路径默认/
server.servlet.context-path= /chapter02
#字节编码
server.tomcat.uri-encoding=utf-8
#允许的最大连接数
server.tomcat.max-threads=500
#日志存放位置
server.tomcat.basedir=/home/ sang/ tmp
```



### 生成ssh认证

keytool -genkey -alias tomcathttps -keyalg RSA -keysize 2048 -keystore sang.p12 -validity 365

- -genkey创建一个新的密钥
- -alias表示keystore的别名
- -keyalg表示使用加密算法式RSA
- -keysize表示密钥长度
- -keystore表示生成的密钥存放位置
- -validity表示密钥的有效时间单位为天



输入相关信息后会在当前目录生成一个sang.p12的文件



key-store表示文件名

key-alias表示密钥别名

key-store-password刚刚输入的密码

运行并输入https://localhost:8080进行查看（注意使用http会告诉请求失败，需要重定向）

# 完结!!!!tmd这本书为啥这么老!连个代码都没法弄



## 另一本书



### @SpringBootApplication

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
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
    @AliasFor(
        annotation = EnableAutoConfiguration.class
    )
    Class<?>[] exclude() default {};

    @AliasFor(
        annotation = EnableAutoConfiguration.class
    )
    String[] excludeName() default {};

    @AliasFor(
        annotation = ComponentScan.class,
        attribute = "basePackages"
    )
    String[] scanBasePackages() default {};

    @AliasFor(
        annotation = ComponentScan.class,
        attribute = "basePackageClasses"
    )
    Class<?>[] scanBasePackageClasses() default {};

    @AliasFor(
        annotation = ComponentScan.class,
        attribute = "nameGenerator"
    )
    Class<? extends BeanNameGenerator> nameGenerator() default BeanNameGenerator.class;

    @AliasFor(
        annotation = Configuration.class
    )
    boolean proxyBeanMethods() default true;
}

```

源码

首先是@Target({ElementType.TYPE})说明该注解只能用在接口或者类和enum上

@Retention(RetentionPolicy.RUNTIME)说明是运行注解

@Documented将注解添加到doc中

@Inherited:可以被继承

@SpringBootConfiguration:springboot的配置注解实际上就是一个@Configuration

@EnableAutoConfiguration:启用自动注解功能

@ComponentScan:组件的自动扫描



SpringApplication的启动流程

通过run()方法我们得到

```java
    public static ConfigurableApplicationContext run(Class<?> primarySource, String... args) {
        return run(new Class[]{primarySource}, args);
    }

    public static ConfigurableApplicationContext run(Class<?>[] primarySources, String[] args) {
        return (new SpringApplication(primarySources)).run(args);
    }

//最终执行的run
    public ConfigurableApplicationContext run(String... args) {
        StopWatch stopWatch = new StopWatch();
        stopWatch.start();
        DefaultBootstrapContext bootstrapContext = this.createBootstrapContext();
        ConfigurableApplicationContext context = null;
        this.configureHeadlessProperty();
        SpringApplicationRunListeners listeners = this.getRunListeners(args);
        listeners.starting(bootstrapContext, this.mainApplicationClass);

        try {
            ApplicationArguments applicationArguments = new DefaultApplicationArguments(args);
            ConfigurableEnvironment environment = this.prepareEnvironment(listeners, bootstrapContext, applicationArguments);
            this.configureIgnoreBeanInfo(environment);
            Banner printedBanner = this.printBanner(environment);
            context = this.createApplicationContext();
            context.setApplicationStartup(this.applicationStartup);
            this.prepareContext(bootstrapContext, context, environment, listeners, applicationArguments, printedBanner);
            this.refreshContext(context);
            this.afterRefresh(context, applicationArguments);
            stopWatch.stop();
            if (this.logStartupInfo) {
                (new StartupInfoLogger(this.mainApplicationClass)).logStarted(this.getApplicationLog(), stopWatch);
            }

            listeners.started(context);
            this.callRunners(context, applicationArguments);
        } catch (Throwable var10) {
            this.handleRunFailure(context, var10, listeners);
            throw new IllegalStateException(var10);
        }

        try {
            listeners.running(context);
            return context;
        } catch (Throwable var9) {
            this.handleRunFailure(context, var9, (SpringApplicationRunListeners)null);
            throw new IllegalStateException(var9);
        }
    }

```

通过代码得知我们需要查看SpringApplication的构造函数得

```java
    public SpringApplication(ResourceLoader resourceLoader, Class<?>... primarySources) {
        this.sources = new LinkedHashSet();
        this.bannerMode = Mode.CONSOLE;
        this.logStartupInfo = true;
        this.addCommandLineProperties = true;
        this.addConversionService = true;
        this.headless = true;
        this.registerShutdownHook = true;
        this.additionalProfiles = Collections.emptySet();
        this.isCustomEnvironment = false;
        this.lazyInitialization = false;
        this.applicationContextFactory = ApplicationContextFactory.DEFAULT;
        this.applicationStartup = ApplicationStartup.DEFAULT;
        this.resourceLoader = resourceLoader;
        Assert.notNull(primarySources, "PrimarySources must not be null");
        this.primarySources = new LinkedHashSet(Arrays.asList(primarySources));
        this.webApplicationType = WebApplicationType.deduceFromClasspath();
        this.bootstrapRegistryInitializers = this.getBootstrapRegistryInitializersFromSpringFactories();
        this.setInitializers(this.getSpringFactoriesInstances(ApplicationContextInitializer.class));
        this.setListeners(this.getSpringFactoriesInstances(ApplicationListener.class));
        this.mainApplicationClass = this.deduceMainApplicationClass();
    }
```

其中webApplicationType参数是用于容器环境与容器初始化

WebApplicationType.deduceFromClasspath()根据这个得到webApplicationType的值为SERVLET

WebApplicationType的值有3个，分别如下所示。
①SERVLET：Servlet环境。
②REACTIVE：Reactive环境。
③NONE：非Web环境。

deduceFromClasspath()方法的实现逻辑如下：先判断webflux相关的类是否存在，存在则认为当前应用为REACTIVE类型；不存在则继续判断SERVLET相关的类是否存在，都不存在则为NONE类型；否则，当前应用为SERVLET类型。







# 警告

```java
 // 创建线程安全的Map 
    static Map<Long, User> users = Collections.synchronizedMap(new HashMap<Long, User>()); 
```



# 小后端例子（整合VUE）

## 环境准备

安装node.js和vue-cli

首先创建一个项目文件夹将Java项目和vue项目分两个文件存放.



## VUE部分

https://cli.vuejs.org/zh/guide/installation.html然后使用vue-cli搭建项目



#### 跨域问题放到文件vue.config.js

文件路径为：



```js
// 跨域配置
module.exports = {
    devServer: {                //记住，别写错了devServer//设置本地默认端口  选填
        port: 8002,
        proxy: {                 //设置代理，必须填
            '/api': {              //设置拦截器  拦截器格式   斜杠+拦截器名字，名字可以自己定
                target: 'http://localhost:8001',     //代理的目标地址
                changeOrigin: true,              //是否设置同源，输入是的
                pathRewrite: {                   //路径重写
                    '/api': ''                     //选择忽略拦截器里面的单词
                }
            }
        }
    }
}

```



#### request.js用来进行请求或者响应的处理

只有在用request进行请求时才进行处理页面跳转不进行处理

```js
import axios from 'axios'
import router from "../src/router";

const request = axios.create({
    baseURL:"/api",
    timeout: 5000
})

// request 拦截器
// 可以自请求发送前对请求做一些处理
// 比如统一加token，对请求参数统一加密
request.interceptors.request.use(config => {
    config.headers['Content-Type'] = 'application/json;charset=utf-8';
    // let user = sessionStorage.getItem("user");
    // console.error(config);
    // if(!user && router.path !="/register"){
    //     router.push("/login");
    // }

    // config.headers['token'] = user.token;  // 设置请求头
    return config
}, error => {
    return Promise.reject(error)
});

// response 拦截器
// 可以在接口响应后统一处理结果
request.interceptors.response.use(
    response => {
        console.error("1111");
        let res = response.data;
        // 如果是返回的文件
        if (response.config.responseType === 'blob') {
            return res
        }
        // 兼容服务端返回的字符串数据
        if (typeof res === 'string') {
            res = res ? JSON.parse(res) : res
        }
        return res;
    },
    error => {
        console.log('err' + error) // for debug
        return Promise.reject(error)
    }
)


export default request
```



#### 关于Element插件

高版本的VUE引入ElementPlus

低版本直接引入ElementUI即可



#### 对于APP.vue文件

一般只有一个这个

```
<router-view></router-view>
```



#### 关于路由的vue文件

##### 首先是正常的路由

```js
const routes = [
  {
    path: '/',
    name: 'Layout',
    component: Layout,
    redirect:"/user",
    meta: {
      requireAuth: true // 配置此条，进入页面前判断是否需要登陆
    },
    children:[
      {
        path: '/user',
        name: 'User',
        component: User,
      },{
        path: '/person',
        name: 'Person',
        component: () => import(/* webpackChunkName: "about" */ '../views/Person.vue'),
      },{
        path: '/about',
        name: 'About',
        // route level code-splitting
        // this generates a separate chunk (about.[hash].js) for this route
        // which is lazy-loaded when the route is visited.
        component: () => import(/* webpackChunkName: "about" */ '../views/About.vue')
      }
    ]
  },
  {
    path: '/login',
    name: 'Login',
    component: Login
  },
  {
    path: '/register',
    name: 'Register',
    component: ()=>import('../views/Register.vue')
  },

]
```

每一个对象变是一条路由

但是对于children内的路由一般展示在上层的<router-view></router-view>中

在上面这种带有children一般如下格式有着公共模块,有着展示区域

```vue
<template>
    <div>
        <Header/>

        <div style="display: flex;">
            <Aside/>
            <router-view style="flex: 1"></router-view>

        </div>
    </div>
</template>

<script>
    import Header from "../components/Header";
    import Aside from "../components/Aside";

    export default {
        name: "Layout",
        components:{
            Header,
            Aside
        },
    }
</script>

<style scoped>

</style>
```







##### 授权认证

```js
router.beforeEach((to, from, next) => {
  if (to.meta.requireAuth) { // 判断该路由是否需要登录权限
    if (sessionStorage.getItem("user")!=null ) { // 通过vuex state获取当前的userid是否存在
      next();
    } else {
      next({
        path: '/login',
        // query: { redirect: to.fullPath } // 将跳转的路由path作为参数，登录成功后跳转到该路由
      })
    }
  }
  else {
    next();
  }
})
```



#### 页面跳转

```js
this.$router.push("/login");
```

vue文件的函数中一般使用这种方法

在js文件中一般采用

```js
router.push("/login");
```

然后按alt+enter引入router



## Java部分

comment部分



跨域问题

```java
@Configuration
public class CorsConfig {

    @Bean
    public WebMvcConfigurer corsConfigurer(){
        return new WebMvcConfigurer() {
            @Override
            public void addCorsMappings(CorsRegistry registry) {
                registry.addMapping("/**")
                        .allowedHeaders("*")
                        .allowedMethods("*")
                        .allowedOrigins("*");
            }
        };
    }

}

```



mybatis-plus的配置文件(分页插件)

```java
@Configuration
@MapperScan("com.yh.mapper")
public class MybatisPlusConfig {
    // 最新版
    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor() {
        MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
        interceptor.addInnerInterceptor(new PaginationInnerInterceptor(DbType.MYSQL));
        return interceptor;
    }
}
```



同一的返回数据格式

```java
@Data
@Accessors(chain = true)
public class Result<T> {

    private String msg;
    private String code;
    private T data;

    public Result(){

    }

    public Result(T data) {
        this.data = data;
    }

    public static Result success(){
        Result result = new Result();
        result.code="0";
        result.msg="success";
        return result;
    }
    public static <T> Result<T> success(T data){
        Result result = new Result<>(data);
        result.code="0";
        result.msg="success";
        return result;
    }

    public static Result error(String msg,String code){
        Result<Object> result = new Result<>();
        result.msg=msg;
        result.code=code;
        return result;
    }

}
```

