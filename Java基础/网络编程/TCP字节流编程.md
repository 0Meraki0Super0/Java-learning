
### Server

1. 服务器在本机9999端口监听
	- `ServerSocket serverSocket = new ServerSocket(9999)`
2. 若有客户端连接，返回socket对象。若无，==程序阻塞，等待连接
	- `Socket socket=serverSocket.accept();`
	- *serverSocket可通过 accept() 返回多个 socket---多并发*
3. 读取客户端写入到数据通道的数据
	- `InputStream i= socket.getInputStream()`
4. IO读取
	```java
	byte[] buf = new byte[1024] //设置缓冲区，每次读1024字节
	int readLen = 0
	while((readLen = i.read(buf)) != -1)
		sout(new String(buf,0,readLen)) //根据读取实际长度显示内容
```
5. 写入数据到数据通道，结束写入标记
	- `i.write("1111")`
	- `socket.shutdownInput()`
6. 关闭流和socket
	```java
	i.close()
	socket.close()
	serverSocket.close()
```

### Client

 1. 连接服务器（服务器IP+端口）
	 - `Socket socket=new Socket(InetAddress.getLocalHost(),9999)
	 - *注：此处服务器与客户端都在本机*
2. 得到和socket对象关联的输出流对象，并写入数据到 数据通道
	- `socket.getOutputStream().write("hello~").getBytes()`
3. 设置结束标记
	- `socket.shutdownOutput()
4. 关闭


### 补充说明

1. 客户端通过某个端口和服务端通讯，这个端口由TCP/IP分配，有随机性
2. 
### 字符流编程

——广泛使用
- 将字节流转为字符流——==转换流==
	- `OutputStreamWriter(字节流)
	- `IntputStreamReader(字节流)
	- `BufferedWriter bufferedWriter = new BufferedWriter (outputStreamWriter)
- 设置写入标记的另一种方式。
	- `writer.newLine()
	- *注：此时服务器应使用 readLine() 读取*