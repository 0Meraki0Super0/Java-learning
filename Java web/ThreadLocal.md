
### 介绍
1. 介绍
	- ThreadLocal可以实现在同一个线程数据共享---解决多线程数据安全
	- 一个线程可以创建多个 `ThreadLocal` 对象。每个 `ThreadLocal` 对象都代表一个线程局部变量
	- ThreadLocal对象充当了一个在每个线程中存储和访问局部变量的 容器
	- ThreadLocal中保存数据，线程销毁后自动释放
2. 原理
	- get()方法解读：
		- 先获取当前线程：`Thread t = Thread.currentThread()`
		- 获取当前对象绑定的ThreadLocalMap对象
			`ThreadLocalMap map = getMap(t)`
		- 从当前线程的map中获取与当前threadLocal 对象关联的Entry[k,v] (对象+值)`ThreadLocalMap.Entry e = map.getEntry(this)`
		- 取出Entry对象的value，放入threadLocal对象的数据
		 ``注：ThreadLocals有一个属性Entry[], ` 
	- set()方法解读：
		- 获取当前线程及绑定的map对
		- `map.set(this,value)`此处this为ThreadLocal对象
	- getMap()方法
		- `return t.threadLocals`
3. 总结
	- 一个线程thread可以有多个 `ThreadLocal` 对象，各个独立，类型为ThreadLocalMap，map中有一张table表，表中存放Entry[k-v]，类型为ThreadLocal. ThreadLocalMap. Entry[k,v]。k为ThreadLocal对象，v为Object类型的对象

![[Pasted image 20231209145117.png]]