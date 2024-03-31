
### 动态代理

-  原理解读
	- 背景介绍：Car为子类，实现了Vehicle接口
	1. 代理类--提供代理
		```Java
		public class VehicleProxyProvider {
    //定义一个属性,target_vehicle 表示真正要执行的对象,该对象实现了Vehicle接口
    private Vehicle target_vehicle;
    //构造器
    public VehicleProxyProvider(Vehicle target_vehicle) {
        this.target_vehicle = target_vehicle;
    }

    //重点！返回一个代理对象, 该代理对象可以通过反射机制调用到 被代理对象的方法
    public Vehicle getProxy() {
        //得到类加载器
        ClassLoader classLoader =target_vehicle.getClass().getClassLoader();
        //得到要代理的对象/被执行对象 的接口信息
        Class<?>[] interfaces = target_vehicle.getClass().getInterfaces();

        //InvocationHandler 是接口 -> 通过匿名对象的方式来创建该对象(处理器对象)
        InvocationHandler invocationHandler = new InvocationHandler() {
            /*
             * invoke 方法是执行target_vehicle的方法时调用
             * @param o 表示代理对象
             * @param method 就是通过代理对象调用方法时的方法：代理对象.run()
             * @param args : 表示 调用 代理对象.run(xx) 传入的参数
             * @return 表示 代理对象.run(xx) 执行后的结果.
             * @throws Throwable
             */
             
            @Override
            public Object invoke(Object o,Method method, Object[] args)throws Throwable             {
                System.out.println("交通工具开始运行了....");
                Object result = method.invoke(target_vehicle, args);
                System.out.println("交通工具停止运行了....");
                return result;
            }
        };  <----匿名内部类结束
        //创建代理对象，
	    Vehicle proxy=Proxy.newProxyInstance(classLoader,interfaces, invocationHandler)
        return proxy;
    }
}```

	2. 测试类Test-获取代理
		```Java
	    @Test
		public void proxyRun() {
        Vehicle vehicle = new Car();
        //创建VehicleProxyProvider对象, 并且传入的要代理的对象
        VehicleProxyProvider vehicleProxyProvider = 
	        new VehicleProxyProvider(vehicle);

        //获取代理对象, 该对象可以代理执行方法，编译类型 Vehicle
        //运行类型=代理类型=class com.sun.proxy.$Proxy
        Vehicle proxy = vehicleProxyProvider.getProxy();
        //此处执行到代理对象的invoke方法,传入参数method=run(),Object=vehicle(由proxy编译类型获取)，不执行Car类自己的run()方法
        proxy.run();
        //本例中代理类invoke方法中有method.invoke(target_vehicle),这时通过反射+动态绑定，执行到被代理对象（ship）的fly()方法执行。
        String result = proxy.fly(10000);
    }```
    
- 深入理解 **横切关注点**
	- 前置通知@before：invoke方法中反射调用前的代码
	- 返回通知@AfterReturning：invoke方法中反射调用之后
	- 异常通知@AfterThrowing：反射的方法异常时执行
	- 后置通知@After：在finally内
	- 环绕通知@Around
- 价值：不改变源码的情况下进行对象功能增强，功能拓展


### AOP基本介绍

- `Aspect Oriented Programming` 面向切面编程
- 两种实现方式：动态代理（内置aop）/  框架aspectj
- 原理解读：
	- 切面类---  *类似上面的VehicleProxyProvider
		```Java
	@ Order(value = 2)//表示该切面类执行的顺序, value的值越小, 优先级越高
	@ Aspect //表示是一个切面类 [底层切面编程的支撑(动态代理+反射+动态绑定...)]
	@ Component 
	
	
//原生Spring会自动把SmartAnimalAspect当作组件 注入容器
（getBean机制类似getProxy,会返回一个代理对象）
public class SmartAnimalAspect {
    //定义一个切入点, 在后面使用时可以直接引用, 提高了复用性
    // 注意：此处指定切入方法！！不指定则全部切入
    @Pointcut(value = "execution(public float 全类名.getSum(float, float)))")
	public void myPointCut() {
    }
    
    //JoinPoint joinPoint:底层执行时,由AspectJ切面框架，会给该切入方法传入joinPoint对象
    @Before(value = "myPointCut()")
	public void showBeginLog(JoinPoint joinPoint) {
        //通过连接点对象joinPoint 可以获取方法签名
        Signature signature = joinPoint.getSignature();
        System.out.println(signature.getName() + "-参数 "
                + Arrays.asList(joinPoint.getArgs()));
    }
    //如果希望把目标方法执行的结果返回给切入方法,可以在@AfterReturning 增加属性returning = "res"，同时在切入方法形参增加 Object res
    //参考简易AOP实现原理(反射+静态方法)，将方法返回值赋值给returning，再传给切入方法
    //使用切入点
    @AfterReturning(value = "myPointCut()", returning = "res")
    public void showSuccessEndLog(JoinPoint joinPoint, Object res) {
        Signature signature = joinPoint.getSignature();
        System.out.println(signature.getName() + " 目标方法返回值" + res);
    }
    //catch{},异常信息传入throwing
    @AfterThrowing(value = "myPointCut()", throwing = "throwable")
    public void showExceptionLog(JoinPoint joinPoint, Throwable throwable) {
        Signature signature = joinPoint.getSignature();
        System.out.println(signature.getName() + " 异常信息=" + throwable);
    }

    @After(value = "myPointCut()")
    public void showFinallyEndLog(JoinPoint joinPoint) {
        Signature signature = joinPoint.getSignature();
        System.out.println(signature.getName());
    }


	//JoinPoint常用的方法.
	joinPoint.getSignature().getName(); // 获取目标方法名
	joinPoint.getSignature().getDeclaringType().getSimpleName(); // 获取目标方法所属类的简单类名
	joinPoint.getSignature().getDeclaringTypeName(); // 获取目标方法所属类的类名
	joinPoint.getSignature().getModifiers(); // 获取目标方法声明类型(public、private、protected)
	Object[] args = joinPoint.getArgs(); // 获取传入目标方法的参数，返回一个数组
	joinPoint.getTarget(); // 获取被代理的对象
	joinPoint.getThis(); // 获取代理对象自己
    }
}```
	2. 测试类
		```Java
		public class AopAspectjTest {
		public void smartDogTestByProxy() {
	        //得到spring容器
	        ApplicationContext ioc =
		        new ClassPathXmlApplicationContext("beans1.xml");
	        //通过接口类型来获取到注入的SmartDog对象-就是代理对象
	        SmartAnimalable smartAnimalable =ioc.getBean(SmartAnimalable.class);
	        或 SmartAnimalable smartAnimalable =(SmartAnimalable) ioc.getBean ("smartDog") 
	        smartAnimalable.getSum(10, 2);//被切入的方法在切面类中指定
	    }
	}
	```

- 切入表达式
	- 可以指向类，接口的方法，对该类/对象生效
	- 若指向接口，也可对未实现该接口的类生效 (*Spring 的CGlib动态代理技术)
	- 区别：
		- 动态代理--面向接口-->增强接口的方法
		- CGlib--面向父类-->增强父类的方法
	- 切入表达式重用：定义一个切入点

- 切面类执行顺序
	- 一个切入点有多个切入方法，执行顺序不定
	- 解决：切面类前添加`@Order(value= 1)`设置优先级
	- 类似Filter过滤器的链式调用，对于优先级高的切面类，前置通知优先执行，后面的最后执行
	![[Pasted image 20231215090800.png]]

 - 基于XML配置AOP
	 ```XML
	<bean class="切面类全类名" id="smartAnimalAspect"/>
    <!--配置一个SmartDog对象-bean-->
    <bean class="SmartDog全类名" id="smartDog"/>
    <!--配置切面类, 细节一定要引入 xmlns:aop-->
    <aop:config>
        <!--配置切入点-->
        <aop:pointcut id="myPointCut" expression="execution(public float spring.xml.SmartDog.getSum(float, float)))"/>
        <!--配置切面的前置通知-->
        <aop:aspect ref="smartAnimalAspect" order="1">
            <!--配置前置通知-->
            <aop:before method="showBeginLog" pointcut-ref="myPointCut"/>
        </aop:aspect>
    </aop:config>
```
- 多实例-注解：@scope(value="prototype")，如何实现？

### BeanPostProcessor

- 后置处理器，在Bean的初始化方法前/后调用，具体见IDEA。在`creatBean()`方法内,对创建好的Bean对象进行前置，初始化，后置操作
- AOP与BeanPostProcessor：
	- AOP底层基于BeanPostProcessor机制。即在Bean创建好后，根据是否需要AOP处理来决定返回代理对象还是原生Bean
	- 以上逻辑在后置处理器的后置操作内，运用动态代理来生成代理对象



