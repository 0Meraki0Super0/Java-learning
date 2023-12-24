
### Listener监听器

 - 介绍
	 - Javaweb三大组件：Servlet程序，Listener，Filter
	 - 是JavaEE的规范，即接口。共8个
	 - 作用是监听某种变化(对象创建，销毁，属性变化)，触发对应方法完成相应任务

##### ServletContextListener监听器

- 介绍：
	- 是实现了 ServletContextListener接口的类。可以监听ServletContext对象。
	- 在web应用启动时，就会产生ServletContextEvent 事件，同时会调用监听器的对应事件处理方法contextInitialized,并传递事件对象，可通过该对象获取相关信息（*servletContextEvent . getName()*）

##### ServletContextAttributeListener监听器

- 监听ServletContext对象的属性变化

##### 其他监听器

1. HttpSessionListener  (HttpSessionAttributeListener)
	- 监听session的生命周期
2. ServletRequestListener

### Filter过滤器

##### 基本介绍
- 
	- 作用：拦截请求，过滤响应
	- 应用场景：权限检查，事务操作
	- 可在web.xml文件中配置
- 原理：
	1. Tomcat收到请求，判断是否走过滤器
		*注：过滤规则由程序员指定*
	2. 过滤器判断是否通过验证
	3. 过滤器分析实例
		- tomcat创建Filter接口的实例filter 调用：init ( filterConfig )：
		- 配置web.xml文件---filterConfig
		```jsp
		<filter>
			<filter-name>filter<>
		    <filter-class>com.xx.filter<>   
		</filter>
		<filter-mapping>
			<filter-name>filter<>
		  <url-pattern>/manage/*</url-pattern>  //这是过滤条件
		当请求的url和过滤条件匹配时，调用该filter
		/manage/* 解析为完整路径http://ip:port/工程路径/manage/*
		``` 
		-  可以认为Tomcat调用servlet前，先匹配filter。根据request对象封装的uri到filterUrlMapping 去匹配，匹配上就调用对应的filter对象的doFilter()方法。没匹配上就走servlet/jsp/html等资源
		- doFilter(sR,sR,filterChain)：如果继续访问目标资源，可在该方法内~~自由发挥~~（做事务管理，数据获取，日志管理）                                    
			- 注： 若方法没有调用`filterChain.doFilter (sR , sR) `则停止
			- 该方法解读：继续访问目标自由url , 将对象继续向目标资源传递
			- 在一个请求中，两个sR都是同一个对象在传递
			 *Filter过滤器只关心请求的地址是否匹配，不关心是否存在*
			- 获取session, 判断是否通过验证：
			`HttpSession session ((HttpServletRequest)SR). getSession();
			if(XXX): ==filterChain.doFilter== (servletRequest , servletResponse )//放行
			else：sR.getRequestDispatcher("/x.jsp").forward(sR,sR)//请求转发到某页面

##### Filter生命周期

- web启动时由tomcat创建==一个==实例，同时创建FileConfig对象
- 调用默认无参构造器，及init（FileConfig）
- 通过FileConfig对象可获取filter相关配置信息
- 当HTTP请求和该filter的url-pattern匹配，调用doFilter()开始过滤
- 调用doFilter()时，tomcat同时创建两个sR并传入

##### FilterChain过滤器链

在处理复杂业务时，一个过滤器往往不够，可采用多个过滤器共同完成过滤任务

- 原理：
	- 若一个url请求匹配到几个过滤器，则按web.xml中配置的顺序执行——>filter调用链
	- 若配置顺序为A-B，则顺序：HTTP请求--Tomcat--A的dofilter()--A的前置代码--A的chain.dofilter()--B的dofilter()--B的前置代码--B的chain.dofilter()--==目标资源==--B的后置代码--A的后置代码--把数据返回浏览器
- 注意：
	- 多个filter和目标资源在一次http请求中，属于同一个线程，使用同一个request对象
	- chain.dofilter()将执行下一个过滤器的dofilter()方法。若没有下一个，则执行目标资源