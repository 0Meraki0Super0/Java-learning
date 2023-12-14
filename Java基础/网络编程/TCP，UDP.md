### TCP，UDP

1. **TCP：传输控制协议
	- 建立TCP连接，形成数据传输通道
	- 三层握手，==可靠的==
	- 端到端，数据量大
	- 释放连接。
	- ==效率低==
2. **UDP：
	- 将数据源，目的封装为数据包，~~不用建立连接
	- 数据报 小于 64kb
	- ==不可靠传输==
3. **netstat指令
	- `netstat -an`：查看当前主机网络，端口情况
	- `netstat -an|more`：分页显示

### InetAddress类方法

1. getLocalHost：获取本机InetAddress对象
2. getByName：通过域名/主机名获取InetAddress对象
3. getHostAddress：通过InetAddress对象获取IP

### Socket （套接字）

1. 基本介绍
	- 是两台机器间通信的端点
	- socket允许应用程序将网络连接当作一个流，数据进行IO传输
	- 发起连接方为客户端
2. 网络编程
	- TCP编程
	- UDP编程