### 概述
- JDBC是Java提供的一套用于数据库操作的接口==API==，程序员面向接口编程，不同的数据库厂商需针对这套接口提供不同的实现（*MySQL实现接口/类，jar-----mysql驱动文件*）
### JDBC实现过程
*前置工作：将mysql.jar*放入libs文件夹，并加入到项目。在当前主类导入com.mysql.jdbc.Driver
1. 注册驱动
	- 创建driver对象：`Driver driver = new Driver();`
2. 得到连接
	- `String url = "jdbc : mysql : //localhost :3306/testt"
		- 解释：规定了连接协议，主机，端口，数据库。本质为socket连接
	```java
		Properties p=new Properties();  
		p.setProperty("user","root");  
		p.setProperty("password","1112");  
		Connection connect=driver.connect(url,p);
	```
3. 执行SQL
	- `String  sql = "insert into user values(null, "20",30) "
	- `Statement s = connect.creatStatement()
		- s用于执行SQL语句并返回其结果的对象
	- `int row = s.executeUpdate(sql)
		- row = SQL语句影响的行数
4. 关闭连接资源
	- `s.close()
	- `connect.close()

### 连接数据库的五种方式
1. 直接用com.mysql.jdbc.Driver()
	- 属于静态加载，灵活性差
2. 利用反射加载Driver()
	- `Class a=Class.forName("com.mysql.jdbc.Driver")`
	- `Driver driver =(Driver)a.newInstance()`
3. 使用DriverManager替代Driver，统一管理
	- `DriverManager.registerDriver(driver)
	- `Connection connect=DriverManager.getConnection(url,user,passwd)`
	- 更灵活，统一管理，不用创建Properties对象管理用户名密码
4. 使用Class.forName自动注册驱动，简化代码
	- `Class.forName("com.mysql.jdbc.Driver")`
	- 不用创建driver实例，自动在底层静态代码块里注册了driver驱动
	- ~~注意，jdk5之后可这句也不用写了，全自动注册驱动
5. 基于4，增加了配置文件，使连接更灵活
	- 配置文件中写入user,passwd,url,driver
	- 创建properties对象获取信息
	  ```Java
	  new Properties().load(new InputStream("filepath"))
	  String user=p.getProperty("user")
```

### ResultSet（结果集）

是通过执行查询数据库的语句生成的数据表。ResultSet对象保持一个光标指向当前数据行，next() 让光标下移
```java
	ResultSet resultset = statement.executeQuery(sql);
	while(resultset.next()) {
		int id = resultset.getInt(1) //获取该行第一列
		Data data = resultset.getDate(2) //获取该行第二列
}
```
### Statement

用于执行静态SQL语句并返回其生成结果的对象。注意，有SQL注入的风险
解决：用PreparedStatement代替
- 不再用+拼接SQL语句，参数用？占位符代替
- 给？赋值：
	- `preparedstatement.setString(1,name)
	- `preparedstatement.setString(2,pwd)


### 总结
![[Pasted image 20231202102246.png]]

![[Pasted image 20231202102338.png]]