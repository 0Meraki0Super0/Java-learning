
### JSON
1. 介绍：
	- 是JavaScript的对象表示法
	- 轻量级的文本数据交换格式，独立于语言
2. 使用实例
```json
	var myJson = {
	"key1": "韩顺平教育",                 // 字符串
	"key2": 123,                         // Number
	"key3": [1, "hello", 2.3],           // 数组
	"key4": {"age": 12, "name": "jack"}, //json 对象
	"key5": [                            //json 数组
	{"k1": 10, "k2": "milan"},
	{"k3": 30, "k4": "smith"}]
	};
	console.log("key1= " + myJson.key1)
```
3. JSON和字符串对象转换
	- ``JSON.stringify(json) ：将一个 json 为 对象转换成为 json 字符串
	- `JSON.parse( jsonString ) ：将一个 json 为 字符串转换成为 json 对象
4. **JSON 在 在 Java 中应用场景
	- Javabean对象 和  json字符串 的转换
		 *注：**JavaBeans**是[Java](https://zh.wikipedia.org/wiki/Java "Java")中一种特殊的类，可以将多个对象封装到一个对象（bean）中。特点是可[序列化](https://zh.wikipedia.org/wiki/%E5%BA%8F%E5%88%97%E5%8C%96 "序列化")，提供无参构造器，提供getter和setter方法访问对象的属性。名称中的“Bean”是用于Java的可重用软件组件的惯用叫法。*
		 1. 测试类应继承TypeToken泛型类并指定泛型
		 2. new Gson()对象，java对象book
		 3. `String bookStr=gson.toJson(book)`
		 4. `Book book1=gson.fromJson(bookStr,Book.class)`
	-  List 集合和 json 的转换
		1. `String bookListStr = gson.toJson(bookList)
		2. `List<Book> bookList2 = gson.fromJson(bookListStr, new BookType().getType())
	- map 集合和 json 的转换
		1. `String bookMapStr = gson.toJson(bookMap)`
		2. `Map<String, Book> bookMap2 = gson.fromJson(bookMapStr, new
		`TypeToken<HashMap<String, Book>>(){}.getType())


### Ajax

##### 介绍
-  异步 JavaScript 和 XML----是一种浏览器==异步发起请求(指定发哪些数据)，局部更新页面的技术
- 原理（浏览器）
	1. 创建XMLHttpRequest对象（Ajax引擎对象）
	2. 通过XMLHttpRequest对象发送指定数据（异步）（ *注：异步发送可以实现服务端响应前，浏览器不用等待，用户可以继续进行其他操作*）
	3. XMLHttpRequest对象指定数据返回后，由哪个函数来处理
	4. 得到返回数据后，可以进行DOM操作，完成局部刷新/数据/页面
- 问题：需要考虑浏览器兼容性问题。实际开发中用jQuery

#####  jQuery的Ajax 请求
- 相关方法
	1. $ . ajax() : 执行异步HTTP（Ajax）请求
	2. $ . get() / $ . post()请求----底层仍为$ . ajax() 方法实现异步请求
	3. $ . getJSON()--底层同上

**总结框架：
![[Pasted image 20231209133707.png]]