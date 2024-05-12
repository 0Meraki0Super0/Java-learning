### 用法
- concat(name,' ','City')：空格手动拼
- concat('%',name,'%')：拼接like匹配条件（包含name）
- concat(name,'%')：可表示以name开头并比name长
- char_length(string)：返回字符串长度
- length(string)：字符串的字节数

### 条件查询

- 查询去重数据：
	- `select distinct colomn from table
	- `select colomn from table group by colomn`// 将结果按colomn分组
- 限制返回行数：
	- `select colomn from table limit 0，2 //第一个参数指定第一个返回记录行的偏移量(可省略)（初始行为0）；第二个参数指定返回记录行的最大数目`
- 查询后的列重命名
	- `select colomn AS myColomn from table limit 2`
- 查找后排序
	- `select colomn from table order by id ASC/DESC`
- where
	- 查询：`select colomn from table where name IN ('我','你')
	- 过滤空值：`select colomn from table where name IS NOT NULL
	- 排除：`select colomn from table where name NOT IN ('我')/ name !='我'
- Like匹配
	- `select colomn from table where name （NOT） LIKE "%我%"
	- %0或多个字符%
	- __ 1个字符 __ 
	- 【匹配其中任意1个字符】
	- 【^不匹配其中任意1个字符】
### 高级查询

- 查最值：`select MAX(age) from user 
- 男生的平均年龄及GPA
	- `SELECT COUNT(gender) ,ROUND(AVG(gpa),1) FROM user WHERE gender="male"`
	- `AVG` 函数计算平均值
	- `ROUND` 函数将结果四舍五入到一位小数
- 分组统计
	- `SELECT gender,university,COUNT(device_id) AS 'user_num', AVG(active_days_within_30) AS 'avg_active_day',AVG(question_cnt) AS 'avg_question_cnt' FROM user_profile GROUP BY gender,university`

### 多表查询

明天再说
