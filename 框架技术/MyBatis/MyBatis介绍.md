——**简化数据库操作的持久层框架**

### 简介
- 为什么需要MyBatis ?
	- 传统连接方式：需要自己连接数据库，且不能通过OOP操作数据库，硬编码。
- MyBatis 基本介绍：
	- 配置连接池即可。以OOP操作DB
	- 在Java和SQL间提供更灵活的映射方案
	- 将对数据库（表）的操作剥离Java代码（解耦），放入XML配置文件
	- 可对DB操作进行优化，提高效率。如配置缓存

- **Java用MyBatis操作数据库原理图**
![[Pasted image 20231226091259.png]]

- **核心流程**
1. 配置mybatis.xml文件
2. 根据配置文件创建sqlSessionFactory (会话工厂)
3. 工厂创建sqlSession会话，用来操作数据库。内部包含执行器
4. 通过MapperProxy的==动态代理机制==创建代理对象Mapper(*封装了对数据库的操作*)，调用某个方法时，将对应的SQL语句交给Executor执行

*也可直接在XML文件中的< mapper>标签内定义ResultMap来定义如何映射数据库查询结果到Java对象
```xml
<mapper namespace="com.example.dao.MyMapper">
    <!-- 定义ResultMap -->
    <resultMap id="yourResultMapId" type="com.example.model.YourModel">
        <!-- 映射数据库列到Java对象属性 -->
        <id property="id" column="column_name" />
        <result property="propertyName" column="column_name" />
        <!-- 可以继续添加其他属性映射 -->
    </resultMap>
    
    <!-- 其他SQL语句和映射配置 -->
</mapper>
<!-- MyBatis XML映射文件中的查询SQL语句 -->
<select id="selectUserById" parameterType="int" resultType="com.example.model.User">
    SELECT * FROM users WHERE id = #{id}
</select>
```

