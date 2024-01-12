
YAML（YAML Ain't Markup Language）是一种以==数据为中心的标记语言==，不依赖于特定的编程语言。常用于配置文件和数据交换的场景。

### 基本语法
```yaml

person:
  name: John Doe
  age: 30
  address:        #address: {city: new york, zip: 10001}
    city: New York
    zip: 10001
  skill:          #skill: [dry, cry]
	- dry
	- cry
 
```
解读：
- 形式为k-v，格式为k冒号空格v
- 区分大小写
- 缩进表示层级
- 字符串不用加引号

**使用细节**
- 在spring boot项目中，YAML多用于配置文件——application.yaml ，放在src-main-resources目录下。该目录下有application.properties ，==优先级更高==。故应避免相同的数据绑定
- 在pom.xml中引入spring-boot-configuration-processor，可以实现yaml文件中字段信息提示功能