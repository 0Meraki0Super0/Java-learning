
### 底层实现

1. 创建configuration类，实现读取mybats.xml文件，==拿到连接。==
2. 编写Monster类，属性须和SQL语句中变量相同.@Data
	- 相关注解：
		- @Getter，@Setter：给本类所有属性加上对应的getter，setter
		- @toString
		- @NoArgsConstructor,@NoArgsConstructor：生成无参，全参构造器
		- **@Data：等价于以上全部**
3. 编写Executor，提供查询方法——接收传入的sql语句，执行，返回monster对象
4. 编写==SqlSession——configuration与Executor的桥梁==。提供查询方法，调用执行器进行查询，返回monster对象
5. 编写MonsterMapper接口，声明方法getName（如查询，添加······）
6. monsterMapper.xml中指明接口路径，配置接口方法
	`<select id="getName"  resultType="com.xx.Monster">SQL</select>`
7. 编写==MapperBean——关联Mapper接口与其xml文件==。包含接口信息和接口方法：
	- MapperBean类——接口名，方法集合
	- Function类——SQL类型，方法名，SQL语句，返回类型，参数类型。
8. 在configuration中读取Mapper.xml文件，并创建MapperBean对象。读取方法：用类加载器，获得root节点，拿到节点下所有标签，利用迭代器，为每个标签元素创建Function对象并赋值，加入list。迭代结束后，将Function的list设置给mapperBean对象
9. 编写MapperProxy，当执行mapper接口的代理对象方法时，会执行到invoke方法。该方法中创建mapper Bean对象，判断：方法的运行类型（类）的名称=该mapperBean对应的接口名 && mapperBean的Function非空。循环找到对应方法，传入参数执行SqlSession中查询方法
10. SqlSession中编写方法，传入class，返回动态代理对象
11. 测试类，创建对应对象，调用方法

### 原生API调用

- 即直接通过SqlSession的接口方法实现
- 初始化：`MyBatisUtils.getSqlSession()`
- 增删改：`SqlSession.update("com.xx.MonsterMapper.updateMonster"); SqlSession.commit(); close`

