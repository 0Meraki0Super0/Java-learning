### 简介

- 是WEB层的框架，基于Servlet
- 基于Spring框架，是Spring处理WEB层的模块
- 

### 执行流程
1. 浏览器发出请求url，传到**前端控制器(core)**
2. DispatcherServlet (前端控制器/中央控制器/分发控制器) 调用 **处理器映射器**
3. HanderMapping(处理器映射器) 返回 **处理器执行链** (HandlerExecution)，包含多个HandlerInterceptor拦截器
4. 前端控制器 调用 **处理器适配器** (HandlerAdapter)
5. 处理器适配器 调用 Hander——即**Controller**
6. Controller调用组件，数据库。返回ModelAndView对象
7. 处理器适配器将ModelAndView返回**前端控制器**
8. 前端控制器调用 视图解析器
9. 返回View对象
10. 视图渲染，把Model里的数据填充到View
11. 前端控制器返回响应

![[Pasted image 20231218184403.png]]
### @RequestMapping

- @RequestMapping注解可修饰类、方法。表示类请求页面，方法请求请求页面
	value=“/xx” 指定控制器的某个方法的请求url——请求映射到某个页面，**不能重复**
- 可指定请求方式：`@RequestMapping(value="/buy",method=RequestMethod.GET)
	== `GetMapping(value="/buy")
- 可使用占位符，**获取到前端页面的参数**
	`<a href = "user/reg/wr/18">哈哈哈</a>`
	
	``@RequestMapping(value="/reg/{username}/{userid}")
	``public String register(@PathVariable("username") String name,
	``@PathVariable("username") String id)  {······}

- 注：URL访问时提交的参数名应和 后台对应方法的形参名一致

### Rest
- 是目前流行的请求方式，指（资源）表现层状态转化。
- HTTP协议内容见[[HTTP，HTTPS]]    
- Rest的核心过滤器：
	- 默认为GET,POST请求
	- HiddenHttpMethodFilter：将以POST方式提交的DELETE, PUT请求进行转换
	- 该过滤器在web.xml中配置。按method参数名来读取

### 映射请求数据
- 获取参数值
如何获取到url请求 http://xxx/url?参数名=参数值 中的参数
在一个Handler中的某个处理方法中，@RequestParam表示一个收到的参数，required=false表示可以没有：
	`@RequestMapping(value = "/vote01")
	`public String test01(@RequestParam(value = "name", required = false) String  username){}`
- 获取JavaBean类型数据
	*注：表单控件名称name应和Javabean对象字段对应*

### 模型数据

- 模型数据处理----控制器 把数据放入 request域，并在前端取出显示
1. 以HttpServletRequest传递
	`@RequestMapping(value = "/vote05")
	`public String test05(Master master, HttpServletRequest request,  HttpServletResponse response)`
2. 通过请求的方法参数 Map<String,Object>传递，springMVC 会将 map 的 k-v 放入到 request 域对象
3. 返回 ModelAndView对象。既包含model数据，也包含视图消息
	-  其addObject 方法可以添加 key-val 数据，默认在 request 域中

-  模型数据处理----控制器 把数据放入session域，并在前端取出显示
	通过请求的方法参数HttpSession session传递

- @ModelAttribute 实现prepare 方法
1. 在某个方法上增加@ModelAttribute注解，调用该Handler的所有方法前，都先调用这个
2. 最佳实践：修改用户信息。可在prepare方法中查询这个用户信息，在修改方法中使用该信息。表单若修改，则更新信息；若未修改(如禁止修改)，保持原来值

### 视图与视图解析器

- 在 springMVC 中的目标方法最终返回都是一个视图，返回的视图都会由一个视图解析器来处理 (默认)
1. 自定义视图流程
	- 配置xml文件-增加视图处理器。该处理器须配置到IOC容器。order表示优先级，越小越高
		`<bean class="org.springframework.web.servlet.view.BeanNameViewResolver">
  	``<property name="order" value="99"></property>             </bean>`
	- 编写自定义视图类，并加入IOC容器
		`@component(value="myView")`
		`public class MyView extends AbstractView{
		
		}`
	- 创建控制器Handler
		`@RequestMapping("/good") @Controller`
	- 写视图的前端页面，及跳转逻辑
2. 工作流程
	- SpringMVC 调用目标方法, 返回自定义 View 在 IOC 容器中的 id
	- SpringMVC 调用 BeanNameViewResolver 解析视图: 从 IOC 容器中获取并返回 id 值对应的 bean, 即自定义的 View 的对象
		*注：若该id不存在，任按照默认处理机制*
	- SpringMVC 调用自定义视图的 renderMergedOutputModel 方法渲染视图
	 
3. 目标方法 指定转发 & 重定向
	- 默认为请求转发，如`@RequestMapping(value="/test")
	- 重定向: 设置`value=“/order”`，方法 `return "redirect:/login.jsp"
	- 直接指定转发：`return "forward:/xx/xx/view.jsp"`
		*注：不能重定向到/WEB-INF目录*

### 手动实现SpringMVC

主要流程
- 浏览器发出请求URL到汤姆猫，**汤姆猫启动时创建前端控制器，前端控制器创建Spring容器并初始化
	- 编写Spring容器类，创建集合保存扫描包的全路径。
	- 编写扫描方法，通过类加载器得到指定包对应的工作路径。**在IO中，把目录视为一个文件**，创建文件对象并遍历，判断是目录则递归扫描；否则拿到全路径并转为/形式，放入单例池ioc
	- 前端控制器编写初始化方法
- 完成URL和控制器方法映射
	- 编写Handler类，记录**请求URL** 和**控制器方法**的映射关系
	- 前端控制器中，编写映射方法，遍历ioc，得到bean的class对象，筛选带有@controller的类；遍历所有方法，筛选带有@RequestMapping的方法，@RequestMapping内的值即为映射路径。将 ”URL-映射路径，@controller“ 放入Hander的集合
- 前端控制器分发请求到目标方法
	- 编写方法，通过request对象返回对应的Handler对象   ~~若无，返回null
	- 编写方法 分发请求。用请求URL创建Handler对象，若不为空则匹配成功，反射调用控制器的方法
- web.xml动态获取Spring配置文件
- 自定义@Service注解
	- 获取带有@Service的实现类，得到注解的value值。
	- 若未指定value值，则通过反射得到所有接口；遍历接口，获取接口名称并改为首字母小写，通过多个接口名来注入（beanname-bean放入ioc）/ 或直接通过类名首字母小写注入
- 自定义@AutoWired注解
- 自定义@RequestParam获取控制器方法的参数
	- 应考虑目标方法的形参是多种多样的，不一定只有request , response。可**把需要传递给目标方法的 实参 封装到参数数组**
	- 





### 数据格式化
