
### 基本介绍

1. 介绍
	- 是简洁的js库，对原生js的封装使用户更方便处理前端页面
	- 提供方法，事件，API，选择器，方便的AJAX交互
	- 解决浏览器兼容问题
2. 快速入门
	- 使 用 <script type="text/javascript" src="./script/jquery-3.6.0.min.js"></script> 引 入jQuery 库文件
	- `$(function () {})   等价  window.onload = function () {}
	- `$("#btn01") 底层 : document.getElementById("btn01")`  返回的对象就是 jquery 对象 ( 即进行了封装 ) ，而不是原生的 dom 对象
	- 获取对象的方法是 $("#id"), 必须在 id 前有#
	- 规定 jquery 对象的命名以 $ 开头~~不是必须，但是约定

### jQuery和DOM对象

-  jQuery 对象就是对 DOM 对象进行包装后产生的对象。==是一个数组对象
- 
1. DOM对象转为jQuery
	- `jQuery对象=$(DOM对象)=$name
	- `alert($name.val())`
1. jQuery对象转为DOM
	- 通过【index】: `var name = $name[0] ——> alert(name.value)
	- 通过get (index)：`var name = $name.get(0)

### jQuery选择器

- 选择器是 jQuery 的核心, 在 jQuery 中, 对事件处理, 历 遍历 DOM 和 和 Ajax 操作都依赖于选择器
1. 基本选择器—— *通过 id,标签名，class查找DOM元素*
	-  $("#id01")：id="id01"
	-  $("div")/ $("input")：返回标签对应集合的元素
	- $(".myClass")
	-  $("div,span,p.myClass") ：将每一个选择器匹配的元素合并后返回
2. 层次选择器—— 通过 DOM 元素之间的层次关系来获取特定元素, 例如后代元素, 子元素, 相邻元素, 兄弟元素等
	-  $(”form input”)：ancestor descendant。给定祖先元素下匹配所有后代
	-  $(”form > input”) ：parent > child 。给定父元素下匹配所有子元素
	-  $(”label + input”) ：prev + next。匹配所有紧接在 prev 元素后的 next 元素
3. 基础过滤选择器
	-  $("div:first") / $("div:last") :匹配找到的第一个div元素
	- $("div:eq(1)") :匹配找到的第2个div元素
	-  $("input:not(:checked)")
	- $("div:even") :匹配所有索引值为偶数的元素，从0开始
4. 内容过滤选择器
	-  $("div:contains('John')") /  $("div : not(:contains('John'))")
	-  $("div:empty") ：匹配所有空div元素
	- $("div:has(p)").addClass("test")
5. 可见度
	- $("div:hidden") / $("div: visible")
6. 属性过滤
	- $("div[id]")
	-  $("input[name='newsletter']").attr("checked", true)
7. 表单选择器
	-  $(":input")/text/password/radio：选择所有input/~标签
8. 表单属性过滤
	- $("input:enabled") / disabled/checked

### jQuery的DOM操作

- 获取、修改属性
	- $("img").attr("height"）
	- $("img").attr("height", "200");
	- $("img").removeAttr("height"）
- **创建节点
	-  $(html标签)—— $("<p/>") 或 $("<p></p>")
	- **内部插入法：在元素中插入内容（子元素、节点）
		-  A, B都是jquery对象。A.append(B)，B.appendTo(A)
		- A.prepend(B)
	- **外部插入法：在元素外插入内容（兄弟节点）
		-  A.after(B)/B.before(A)
		- 
- 删除节点：$("#sh").remove()
- 清空节点：$("p").empty()
- 复制节点：
	- $("button").clone()
	- $("button").clone(true)：一并复制元素中的事件
- 替换节点：
	- A.replaceWith(B) / B.replaceAll(A)
	- 被替换后元素绑定的事件消失

- **获取 HTML, 文本和值
	- html() 设置或返回所选元素的内容
	- text() 读取和设置某个元素中的文本内容
	- val() 读取和设置某个元素中的值

- **常用遍历方法
	- children()，next() , siblings() 
	- nextAll().eq(index)
	- nextAll().filter("div")

### jQuery的事件

![[Pasted image 20231209111457.png]]