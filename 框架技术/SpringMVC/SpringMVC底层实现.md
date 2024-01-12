
参考[[SpringMVC介绍]]
![[Pasted image 20231218184403.png]]


- 浏览器发出请求URL到汤姆猫，**汤姆猫启动时创建前端控制器，前端控制器创建Spring容器并初始化
	- 编写Spring容器类，创建集合保存扫描包的全路径。
	- 编写扫描方法，通过类加载器得到指定包对应的工作路径。**在IO中，把目录视为一个文件**，创建文件对象并遍历，判断是目录则递归扫描；否则拿到全路径并转为/形式，放入单例池ioc
	- 前端控制器编写初始化方法
- 完成URL和控制器方法映射
	- 编写Handler类，记录**请求URL** 和**控制器方法**的映射关系
	- 前端控制器中，编写映射方法，遍历ioc，得到bean的class对象，筛选带有@controller的类；遍历所有方法，筛选带有@RequestMapping的方法，@RequestMapping内的值即为映射路径。将 ”URL-映射路径，@controller“ 放入Hander的集合
- 前端控制器分发请求到目标方法
	- 编写方法，通过request对象返回对应的Handler对象   ~~若无，返回null
	- 编写方法 分发请求。用请求URL创建Handler对象，若不为空则匹配成功，反射调用 其他控制器的方法（==目标方法==）
- web.xml动态获取Spring配置文件
- 自定义@Service注解
	- 获取带有@Service的实现类，得到注解的value值。
	- 若未指定value值，则通过反射得到所有接口；遍历接口，获取接口名称并改为首字母小写，通过多个接口名来注入（beanname-bean放入ioc）/ 或直接通过类名首字母小写注入
- 自定义@AutoWired注解

- 自定义@RequestParam获取控制器方法的参数
	- 应考虑目标方法的形参是多种多样的，不一定只有request , response。可**把需要传递给目标方法的 实参 封装到参数数组**
	- 遍历形参数组，把实参赋给对应形参，其余以数组形式传入，进行反射调用

- 封装HTTP请求数据 到参数数组
	- 获取HTTP请求的参数集合，`Map<String, String[]> parameterMap= request.getParameterMap() // 参数名-参数值`
	- 编写方法`find(Method method , String name) `返回 请求参数是目标方法的第几个形参——取出method 所有形参，依次与@RequestParam(value = "xxxx")的value值匹配比较，找到xxxx=name的形参位置，返回。
	- 遍历请求参数集合，调用find()，将请求参数按顺序填充到实参数组——遍历目标方法所有形参名称，若匹配则将当前请求参数填充到实参数组

- 完成视图解析
	- 在前端控制器中，可在封装HTTP请求数据之前，设置请求的编码方式，防止中文乱码问题
	- 反射调用目标方法，对返回的结果进行解析，分直接转发，重定向，请求转发。可对含有@ResponseBody的目标方法的返回结果转为JSON格式（引入jackson包，进行数据转换）

- `@ResponseBody`-返回`JSON`格式数据
	- 控制器中编写方法，返回 List< xxx>类型数据。  
	 *注：该方法为目标方法，结果返回给springmvc底层反射调用的位置*   
	