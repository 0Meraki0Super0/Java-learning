
### 整体架构
![[Pasted image 20231215115945.png]]

### 实现流程

- 编写@component的应用了接口的实例类。
- 编写@component @aspect的切面类，其中配置作用域，切入点，前后置，异常等逻辑
- 在XML文件中启用基于注解的AOP功能 `<aop:aspectj-autoproxy />`
- 编写ApplicationContext类模拟Spring容器，定义单例哈池，已封装的bean对象。编写构造器，传入配置类/XML文件，进行扫描和解析出其中所有类，把包含@component的类拿出，把单例对象放入单例表，===========
- 编写测试类，创建Spring容器ioc(可传入XML文件生成容器，也可传入采用注解的配置类)，getBean获取对象，用实例接口类型接收。即可调用实例的方法进行测试
- 见IDEA