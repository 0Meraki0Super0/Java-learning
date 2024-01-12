
### WEB开发介绍

1. WEB用于表示WEB服务器供浏览器访问的资源
2. WEB资源
	- 静态：提供的数据不变。如html , css , js 
	- 动态：可与用户交互。如Servlet，PHP , SSM
3. BS与CS开发
	- BS：browser-server。
		- 不同浏览器之间兼容性差。安全性较低
		- 易用性较强（~~有浏览器就行~~）。拓展性好（~~有浏览器就行~~）
	- CS：client-server
		- 需要自己开发客户端

4. 开发web资源需要web服务器
### 浏览器访问Web

- Javaweb开发技术栈图
![[Pasted image 20231205125358.png]]
- **浏览器请求资源流程分析
	1. 浏览器解析出主机名，本机host/dns服务解析出IP
	2. 根据IP向Tomcat发出HTTP请求
	3. Tomcat（==又称中间件==）解析出web应用
	4. Tomcat解析出具体页面资源，并从**容器**中获取
	5. 将结果以响应HTTP响应返回浏览器
	6. 浏览器解析结果并显示

### Tomcat服务配置

- Tomcat是一个Apache提供的，轻量级web服务器，本质为Java程序，可以处理浏览器的HTTP请求
- 
- **Javaweb程序/工程目录结构
![[Pasted image 20231205124535.png]]
![[Pasted image 20231205132235.png]]
- **部署方式
	- 静态资源：http://localhost:8080/news/web文件夹···/xx.html

### Servlet

- 是一个供其他Java程序 (Servlet引擎) 调用的Java类，不能独立运行
- Servlet常驻内存(可以理解为存在Tomcat的哈希表中)，是==单例==
- 理解：Servlet（服务器端小程序）是一种用于在服务器上扩展请求-响应模型的Java类。它在Java编程语言中编写，通常用于生成动态网页内容。通常运行在支持Java的Web服务器上，如Apache Tomcat或Jetty。它们与常规的静态Web页面不同，后者的内容在服务器上提前准备好，并直接发送到客户端。相反，Servlet能够根据请求的参数动态生成内容。这使得Servlet成为处理表单提交、用户登录、数据库访问等任务的理想选择。
	

##### 手动开发Servlet
1. 编写hello类实现servlet接口
	```java
		public class hello implements Servlet{
		1.初始化servlet，只在创建实例时调用一次
		public void init()
		2.返回servlet的配置
		public ServletConfig getServletConfig()
		3.处理浏览器请求*浏览器每次请求servlet时，均调用一次service*tomcat调用该方法        时，会把HTTP请求的数据封装成实现了ServletRequest接口的request对象*通过该对象，servlet可以得到用户提交的数据
		public void service(ServletRequest,ServletResponse)
		4.返回servlet信息
		public String getServletInfo()
		5.servlet销毁时调用，仅一次
		public void destroy()
	}
```
2. 为hello提供对外访问地址 -->配置 web.xml文件
	- servlet-name : 唯一
	- servlet-class： servlet类的全路径，Tomcat通过反射生成该Servlet时使用
	- url-pattern：该 servlet访问的url名称（eg. /hello）~~随便取名~~
	*访问该servlet方式：*http://localhost:8080/servlet/hello

##### 浏览器请求Servlet流程

- 如果是第一次请求（Tomcat）
	 浏览器发送 ：http://localhost:8080/servlet/==hello
	1. 查询 web.xml，寻找资源”/hello“的url-pattern配置（*如果发现某个类为@webservlet，就说明该类是servlet，读取urlpattern*s）
	2. 若找到url-pattern，则可得到servlet-name
	3. Tomcat 维护了一张特大 HashMap<id , Servlet>, 查询该servlet实例对应的id
	4. 若无，即没有实例。则根据name得到class
	5. 使用反射技术将servlet实例化->init()，并加入哈希表
	6. Tomcat调用service()，hello处理service（）
- 如果是第二次及以后
	- 1，2，3，6
	- *注：若没找到url-pattern，---> 404

##### Servlet生命周期
1. 初始化
- servlet容器（如Tomcat）==加载==servlet完成后，创建实例调用init()方法。         servlet容器在以下情况==装载==（即把某实例加入哈希表）servlet
	 - 在web.xml中配置启动时自动装载 --load-on-startup
	 - 容器启动后浏览器首次发送servlet请求
	 - servlet重新装载时（如Tomcat redeploy，会销毁哈希表里所有实例），浏览器再次发的第一个请求
	 <*整个生命周期内仅调用一次init()*>
2. 处理浏览器请求
	- 每次收到一个HTTP请求，服务器（Tomcat）创建一个新进程
	- **而且创建一个封装HTTP请求的servletrequest对象 和代表响应的servletreponse**
	- 最后调用service()，将请求和响应对象作为形参传递
3. 终止阶段
	- servlet容器/web应用终止，调用destroy()。
	- ~~一般不用管，

##### 通过继承HttpServlet开发Servlet

- HttpServlet是实现了Servlet接口的类，使用更加方便。实际开发中一般不直接用servlet
- 主要步骤：
	- public class okServlet ==extends== HttpServlet
	- 配置web.xml
	- ==重写doGet() , doPost()方法==（*注：*）

##### Servlet 注解（Annotation）

1. @webServlet (urlPatterns={"/hello","/wr"} )  解释：
	- @webServlet——注解
	- 对应web.xml 的 url-pattern，{ }内可配置多个url-pattern
	- 代替了web.xml 的配置。
	
2.  urlPatterns 配置
	- 精确匹配：@webServlet (urlPatterns={"/hello/nihao","/wr"} )
	- 目录匹配：@webServlet (urlPatterns={"/wr/* "} )
				(*可以通过wr/aa , wr/bb/cc 等任意子目录匹配*)
	- 拓展名匹配：@webServlet (urlPatterns={" * .action "} )
				（*可以通过aa.action , bb.action等访问*）
	- 任意匹配：@webServlet (urlPatterns={"/ "} )  或 @webServlet (urlPatterns={"/* "} )
				（*匹配任意请求*）
3. 注意事项
	- 当使用任意匹配时，会覆盖Tomcat的 DefaultServlet（*作用：当其他url-pattern都匹配不上时，就会使用默认servlet来处理静态资源*），则无法处理并返回静态资源
	- 优先级：精确>目录>拓展名>任意>DefaultServlet

##### ServletConfig

- ServletConfig是为Servlet程序配置信息的类。他们的对象都由Tomcat负责创建
- 作用：
	- 获取Servlet程序的servlet-name
	- 获取初始化参数
	- 获取servletcontext对象
	- 
- 应用实例
	- 在doPost()方法中获取配置信息——————HttpServlet**父类GenericServlet有getServletConfig()**，通过该方法得到ServletConfig对象，用该对象的方法获取配  置
	    *注：ServletConfig对象是GenericServlet private ==transient== ServletConfig config。  *transient表示该属性不会被串行化（有些重要信息不想保存入文件）
	    
	- ServletConfig config 使用流程
		1. 当hello对象（Servlet-name）初始化时，Tomcat创建一个ServletConfig对象
		2. 如果hello init() 中调用了super.init(config)，则将该ServletConfig对象传给父类GenericServlet的config属性（初始化）
		3. 如果没有调用，则getServletConfig() 得到的属性为空

##### ServletContext

*问题引出：如何统计每个Servlet被访问的次数？可以使用多个Servlet操作一张数据表
- 也可以**使用多个Servlet共享ServletContext
	- ServletContext基本介绍：
		- 是接口，表示servlet上下文对象
		- 一个Web工程仅有一个。启动时创建，结束时销毁
	- ServletContext对象特点
		- 可被多个Servlet共享
		- 数据存储形式：k-v
		- 可实现多个Servlet通信
		- 数据存在内存
		- 
	- 作用
		1. 获取web.xml中配置的上下文参数<context-param（属于整个web应用)
			- getServletContext()拿到ServletContex对象
			- getInitParameter("")获取相关属性（String类型）
		2. 动态获取当前工程路径
			- getContextPath()  （/servlet）
		3. 获取工程部署在服务器硬盘的**绝对路径
			- getRealPath("/")
		4. 像map一样存取数据，共享
		
	- 网站访问次数计数器
		1. 通过其父类方法获取ServletContext对象
		2. getAttribute("visit") 获取属性的k-v。若为空，将V设置为1；否则属性值+1
			``注：Integer.parseInt(visit+ "" )+1 ——>先转字符串，再转Int

##### HttpServletRequest

- HttpServletRequest 对象(Request )代表客户端的请求。封装着客户端http请求的信息
- 获取请求头数据：
	- getRequestURI()：http://主机/uri
	- getRequestURL()：绝对资源定位符
	- getRemoteAddr()：请求的客户端IP
	- getHeader("请求头字段")：获取HTTP请求头的指定信息（HOST，Referer···）
	- `String [] s= userAgent.split(" ")    s[s .length-1].split("\\/")[0]
- 获取表单数据
	- setCharacterEncoding("utf-8")：设置编码方式
	- response.setControlType("==text/html== ; charset=utf-8")：表示返回的信息用文本解析数据（*application/x-tar：表示返回的是文件，浏览器以下载文件方式处理）
	- getParameter("")：获取单个数据
	- getParameterValues("")：获取一组数据
- **请求转发：
	- 需求：需要在一次请求中使用到多个Servlet
	1. 介绍
		- 指一个web资源收到客户端请求后，通知服务器去调用另一个web资源
		- Request 对象有getRequestDispatcher()方法可以返回getRequestDispatcher对象，其forward方法可实现请求转发
		- ==数据通过request对象带给其他web资源
	2. 请求转发流程
		tomcat根据web.xml调用servlet01，servlet01根据web.xml调用servlet02，servlet02处理完后将数据对象返回tomcat
	3. 实例
		- getRequestDispatcher("/manageServlet")：写的是要转发的Servlet的url
		- forward (request , response)：把当前servlet的request 传递给下一个
	4. 注意事项
		- 浏览器地址不会变化，保留在第一个servlet的url。
		- 基于上一点，如果刷新页面，浏览器会重新发请求
		- 可以转发到WEB-INF目录下
		- 
- **请求重定向
- 浏览器请求地址变化