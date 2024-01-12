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
![[Pasted image 20231213093212.png]]


