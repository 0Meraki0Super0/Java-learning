### 简介

是Spring提供的 访问数据源的技术。类似[[JDBC Utils]]。可以将 JDBC 的常用操作封装为模板方法
- 实现过程
	- 配置jdbc.properties文件，参考[[JDBC API]]
	
	- 配置jdbcTemplate.xml，引入属性文件；配置数据源对象DataSourcec参考[[Spring基础]]中 配置属性文件
	- 对Spring来说，它也是一个bean对象，初始化与getBean方式同上[[Spring基础]]
	- CRUD方式：
	 `Sting sql="insert into monster values(?,?,?)";
	 `update monster set name=? where id=?
	- 查看受影响的记录数：
	 `int affected=jdbcTemplate.update(sql,100,200,"a");

- 相关方法
	- queryFor0bject()：根据id查找对象

- Spring 组件使用jdbcTemplate：
	```java
	@ Repository //注解，自动配置类对象
	public class monsterDao{
		@ resource //自动装配属性对象
		private JdbcTemplate jdbcTemplate;
		public void save(Monster monster){//完成保存任务
			sql=xxx
			`int affected=jdbcTemplate.update(sql,monster.getId(),monster.getName())
		}
	}
```