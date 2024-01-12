
### 关系
- **`springboot > spring > springmvc > servlet > javaweb > javase`**
- `spring是springboot的重要组件之一
- `springboot简化了Spring项目配置流程`

- 约定优于配置：是一种软件开发的理念，旨在减少开发者需要做的显式配置。它强调使用一组默认约定，而不是通过大量的配置来指导应用程序的行为。这个理念的目标是提高开发者的生产力，减少样板代码，降低配置的复杂性，使开发过程更加简洁和直观。

### 依赖管理 与 自动配置
1. 修改默认版本号：pom.xml中，自己引入依赖 或在properties节点中指定
2. Starter场景启动器：将相关场景中的所有依赖——依赖树。核心启动器：Spring-boot-starter
![[Pasted image 20231230173404.png]]
3. 自动配置机制：基于**约定优于配置，按需加载**原则，配置常用依赖。
4. 修改默认配置——/src/main/resources/**application.properties**：
	- 修改服务器监听端口：`server.port=xxx`
	- 修改上传文件大小限制：
	- 自定义配置属性：可通过@value(${xx}) 访问配置文件中xx的属性值

### 容器功能

1. **BeanConfig类**
-  @Configuration注解：修饰类，表示配置类 (类似beans.xml)。该类的bean也会注入容器。配置属性如下
	- proxyBeanMethod=false：每次新建Bean，非代理方式。在调用@Bean时才生效，故须先获取BeanConfig组件，再调用@bean
	- 若通过ioc.getBean获取，则proxyBeanMethod配置无效，Bean为单例，是代理方式
- @Bean注解：修饰方法，返回bean。默认id为方法名。默认单例。
- @Import注解：修饰类，可以指定class数组，来指定注入容器的组件
- @Conditional注解：修饰方法，指定条件注入  
	*例：@ConditionalOnBean (name = "xx") :当容器中有名称(id)为xx的bean时 (类型无所谓)，注入该Bean*
	*注：也可修饰配置类，表示修饰其中所有bean*

*类似Spring的@ComponentScan（）机制*

2. **配置绑定**
- 绑定Bean 和 配置文件中指定的K-V。
- 设置k-v：furn01.id=100
- 配置类@ConfigurationProperties（prefix="furn01"）

### 底层机制

引入：`ConfigurableApplicationContext ioc=SpringApplication.run(MainApp.class,args);`
run()方法执行时，内置汤姆猫如何启动？如何支持Controller组件
1. 汤姆猫启动方法
	```java
	public class HspSpringApplication {
    //这里我们会创建tomcat对象，并关联Spring容器, 并启动
    public static void run() {
        try {
            //创建Tomcat对象 HspTomcat
            Tomcat tomcat = new Tomcat();
            //1. 让tomcat可以将请求转发到spring web容器,因此需要进行关联
            //2. "/hspboot" 就是我们的项目的 application context , 就是我们原来配置tomcat时，指定的application context
            //3. "D:\\hspedu_springboot\\hsp-springboot" 指定项目的目录
            tomcat.addWebapp("/hspboot","D:\\hspedu_springboot\\hsp-springboot");
            //设置9090
            tomcat.setPort(9090);
            //启动
            tomcat.start();
            //等待请求接入
            System.out.println("======9090====等待请求=====");
            tomcat.getServer().await();
        } catch (Exception e) {
            e.printStackTrace();
        }

    }
}
```
	- 编写mainApp，`HspSpringApplication.run()--> 即可启动springboot`
2. 汤姆猫和Spring容器关联
	```java
	public class HspWebApplicationInitializer implements WebApplicationInitializer {
    @Override
    public void onStartup(ServletContext servletContext) throws ServletException {

        System.out.println("startup ....");
        //加载Spring web application configuration => 容器
        //自己 写过 HspSpringApplicationContext
        AnnotationConfigWebApplicationContext ac =
                new AnnotationConfigWebApplicationContext();
        //在ac中注册 HspConfig.class 配置类
        ac.register(HspConfig.class);
        ac.refresh();//完成bean的创建和配置

        //1. 创建注册非常重要的前端控制器 DispatcherServlet
        //2. 让DispatcherServlet 持有容器
        //3. 这样就可以进行映射分发, 回忆一下SpringMvc机制[自己实现过]
        //HspDispatcherServlet
        DispatcherServlet dispatcherServlet = new DispatcherServlet(ac);
        //返回了ServletRegistration.Dynamic对象
        ServletRegistration.Dynamic registration =
                servletContext.addServlet("app", dispatcherServlet);
        //当tomcat启动时，加载 dispatcherServlet
        registration.setLoadOnStartup(1);
        //拦截请求，并进行分发处理
        registration.addMapping("/");
    }
}
```