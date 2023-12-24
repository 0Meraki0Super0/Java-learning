### 简介

-  几个重要概念
	- Spring可以整合其他框架，核心为IOC和AOP
	- **IOC：Inversion of Control反转控制。** Spring根据配置文件/注解创建对象，并放入容器，并完成对象之间的依赖；当需要使用某个对象实例，直接从容器中取
- 原理--实现ApplicationContext . java 容器 
	- 创建容器并解析beans.xml文件，生成对象
		```java
		/*beans.xml文件如下：
		<bean class="spring.bean.Monster" id="monster1">
		<property name="monsterId" value="100" />
		<property name="name" value="100" />
		<property name="skill" value="100" />			
		</bean>
		//注：若bean对象不配置id，则默认为全类名
		//此处设置name,value底层调用setter !!
		*/
		public class ApplicationContext{
			ConcurrentHashmap <String , Object> singleObjects=new ConcurrentHashmap<>() 
			
			//构造器，用来接收容器的配置文件(默认在src下)
			public ApplicationContext(String iocBeanXmlFile){
				//拿到类加载路径
				String path=this.getClass().getResource("/").getPath()
				//dom4j解析->创建SaxReader
				SaxReader reader=new SaxReader()
				//得到Document对象 和 根元素
				Document document = reader.read(new File(path+iocBeanXmlFile))
				Element root=document.getRootElement()
				//得到第一个bean
				Element bean = (Element) root.elements("bean").get(0)
				//获取相关属性()
				String classpath =bean.attributeValue("class")//获取类路径
				List<Elements> property = bean.elements("property")//获取指定属性值
				Elements monsterId = property.get(0).attributeValue("value")
				
				//使用反射创建对象
				Class<?> aclass = Class.forName(classpath)
				Monster o =(Monster)aclass.newInstance()
				//使用反射机制赋值
				Method[] declaredMethods=aclass.getDeclaredMethods()
				for(Method declaredMethod: declaredMethods){
					declaredMethod.invole(o)
				}
				o.setSkill( dance);
				//将创建好的对象放入单例池中
				singleObjects.put(id,o)
			}
			public Object getBean(String id){
				return singleObjects.get(id)
			}
		}
		public class Test{
			//创建容器ioc，类型为ClassPathXmlApplicationContext
			//注意，此处applicationContext为以上类的子类，为对Spring原生框架的简单模拟
			ApplicationContext ioc = new ApplicationContext("beans.xml")
			Monster monster001=(Monster) ioc.getBean("monster1")
		}
```
### Spring容器结构
- 实例见上方ioc容器，其重要结构如下
[[Pasted image 20231213093212.png]]

### IOC 

- **IOC—spring 配置bean**
##### Bean的配置

- 按类型获取（同类型bean只有一个 + 单例）
	`Monster monster001=ioc.getBean("Monster.class")`
- 按构造器配置（使用Monster的有参构造器生成对象）
	*注1：除了index , 也可通过 name (构造器参数名)  /  type(参数类型java.lang.Integer) 来指定参数*
	*注2：同一类中不能存在 参数类型 与 顺序 完全相同的构造器 *
	`<constructor-arg value="100" index="0">`
	`<constructor-arg value="白骨精" index="1">`
	==`Monster monster002=ioc.getBean("monster1",Monster.class)`
- 按p名称空间配置
	`p:monsterId="500"`  (alt+enter)
	`Monster monster003=ioc.getBean("monster1",Monster.class)`
- 按ref引用设置bean (Spring的依赖注入)
	``<bean class="spring.bean.Monsterrr" id="monsterDAO">
	``<bean ····><property name="monsterDAO" ref="monsterDAO" /></bean>
- 通过内部bean配置
	``< property name="monsterDAO" >< bean ···· /> </property> 
- 对集合/数组配置
	1. List 对象
		`<property name="monsterLIst">
			`<list> <ref bean="monster1"/><bean class=xx>···</bean>
			`</list> </property>
- 通过util名称空间 (简便的数据复用)
	`<util:list id="mybooklist">  <value>红楼梦</value>   </util:list>`
	``<property name="booklist" ref="mybooklist">
- 属性级联赋值
	``<property name="dept.name value="搬砖" />
	
- 通过静态工厂获取Bean
	*在类的静态代码块中创建哈希表并放入对象*
	`<bean id="monster1" class="pathOfstaticFactory" factory-method="getMonster">
	`<constructor-arg value = monster01></bean>`
- **通过FactoryBean获取bean对象**
		*注：`FactoryBean中维护一张Hashmap <key-value> key为 MyfactoryBean的属性key，value为想获取的对象的key (id)*
	- ``<bean id="my_monster01 class="FactoryBean-Path">
		`<property name="key" value="monster01" /> 
		`</bean>`
- Bean配置信息重用（继承）
	- 添加属性 `parent=monster1
	- 若bean指定了属性abstract = true，则无法创建实例，只能被继承
- 创建顺序
	- 默认按照配置顺序
	- 若增加属性 depends-on="monster1"，则在id为1的之后创建
- **通过属性文件配置
	- `<context:property-placeholder location="classpath: my.properties"/>//此处配置文件直接放在src目录下
	- ``<bean id="monster01 class="">
		`<property name="monsterId" value=" ${monsterId}" /> 
		`</bean>`


- Bean的单例和多例
	- 默认单例，在启动容器时创建, 放入SingletonObjects
	- 若bean标签中添加属性scope=prototype，则为多例
	- 若希望单例==也==在getBean()时创建，可指定属性 懒加载lazy-init="true"
- Bean的生命周期
	 *bean对象由JVM创建，执行以下方法*
	 - 构造器+setter
	 - 执行init ()--自己写逻辑
	 - 容器关闭时，执行 destroy()--自己写
- **配置bean的后置处理器
	- 在ioc中可配置，本质为对象
	- 该处理器在bean初始化方法调用前 和 调用后 被调用
	- 作用？对IOC容器中==所有==对象统一处理（AOP）
	- `postProcessBeforeInit( Object bean , String beanName)` //bean 为IOC容器中创建的bean。name为bean的id。可以在此对bean进行修改、处理
- 自动装配bean
	- 添加属性autowire="byType"：通过类型 为对象的属性 自动赋值/引用。注意不能有两个相同类型的对象，若某个对象没有属性，则不用自动装配
	- autowire="byName"：通过该对象的属性的setXXX()里的名称寻找 id=XXX的对象来自动装配
- Spring el 表达式

---*具体实现见IDEA-spring项目，另附注解类*


##### 注解配置Bean
1. 介绍
	- @Repository：标识该类为Repository，即持久化层的类
	- @controller：该类为控制器
	- @Service
	- @Component：该类是组件，通用标识
	- **Spring的IOC容器识别到注解就会生成对象，但不管注解的含义是否正确
	- 默认情况下，类名首字母小写作为id的值(monster)。也可用注解的value来配置id，如@Repository(value="monster01")--

2. beans.xml配置
	- `<context:component-scan base-package="com.xx.component"/>`：
			对指定包下的类扫描，并指定要扫描的包。当Spring创建时，就会扫描component包下所有注解，实例化对应的类，并将对象放入IOC容器
	-  `<context:component-scan base-package="com.xx.component">
		 `<context:exclude-filter type="annotation" expression="注解的全类名"></context:component-scan>
			排除一些注解
##### 自动装配

1.  @Autowire：在IOC容器中查找对应组件类型；若Bean不唯一，则须指定属性名作为id值再进行查找
	 	```@Autowire
		  private UserService userservice;```
2. @Resource：
		  ``@Resource(name="userService100") / (Type=UserService.class).
		  `private UserService userservice; //在容器里寻找UserService类型，id为userService100的属性。若不指定，优先使用byName,name=userService来匹配
##### 泛型依赖注入


