---
title: HttpServlet
---



#### HTTP：
* 什么是HTTP？
	* Hyper Text Transfer Protocol 超文本传输协议
* 什么是传输协议？
	* 定义了，客户端和服务器端通信时，发送数据的格式
* HTTP的特点：
	1. 基于TCP/IP的高级协议
	2. 默认端口号:80
	3. 基于请求/响应模型的:一次请求对应一次响应
	4. 无状态的：每次请求之间相互独立，不能交互数据
* HTTP版本特性：
	* 1.0：每一次请求响应都会建立新的连接
	* 1.1：复用连接

* HTTP请求消息数据格式
	* 字符串格式：
		```
		POST /login.html	HTTP/1.1
		Host: localhost
		User-Agent: Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:60.0) Gecko/20100101 Firefox/60.0
		Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
		Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
		Accept-Encoding: gzip, deflate
		Referer: http://localhost/login.html
		Connection: keep-alive
		Upgrade-Insecure-Requests: 1
		
		username=zhangsan	
		```
	1. 请求行
		```
		# 请求方式 请求url 请求协议/版本
		GET /login.html	HTTP/1.1
		```
	2. 请求头：客户端浏览器告诉服务器一些信息
		* 数据格式：(key : value)请求头名称: 请求头值
		* 常见的请求头：
			1. User-Agent：浏览器告诉服务器，浏览器版本信息
				* 作用：在服务器端获取该头的信息，解决浏览器的兼容性问题
			2. Referer：http://localhost/login.html
				* 作用：告诉服务器，当前请求来源
					1. 防盗链
					2. 统计工作
	3. 请求空行
		* 用于分割POST请求的请求头，和请求体
	4. 请求体(正文)：
		* 封装POST请求消息的请求参数

* HTTP响应消息数据格式

* HTTP常用两种请求方法：
	* GET：
		1. 请求参数在请求行中，在url后。
		2. 请求的url长度有限制的
		3. 不太安全
	* POST：
		1. 请求参数在请求体中
		2. 请求的url长度没有限制的
		3. 相对安全




#### Request：
1. request对象和response对象原理
	1. request和response对象是由服务器(web容器)创建
	2. request对象用于获取请求消息，response对象用于设置响应消息

2. request对象继承体系结构：	
	```
	ServletRequest		-- 接口
		|	继承
	HttpServletRequest	-- 接口
		|	实现
	org.apache.catalina.connector.RequestFacade 类(tomcat)
	```

3. request主要功能：
	* 获取请求行数据 ：GET /demo/testService?name=zhangsan HTTP/1.1
		1. 获取请求方式 ：GET
			* String getMethod()  
		2. **获取虚拟目录：/demo**
			* String getContextPath()
		3. 获取Servlet路径: /demo1
			* String getServletPath()
		4. 获取get方式请求参数：name=zhangsan
			* String getQueryString()
		5. **获取请求URI：/demo/testService**
			* String getRequestURI():		/demo/testService
			* StringBuffer getRequestURL(): http://localhost/demo/testService
		6. 获取协议及版本：HTTP/1.1
			* String getProtocol()
		7. 获取客户机的IP地址：
			* String getRemoteAddr()
			
	* 获取请求头数据
		* **String getHeader(String name):通过请求头的名称获取请求头的值**
		* Enumeration<String> getHeaderNames():获取所有的请求头名称
		
	* 获取请求体数据:
		* 只有POST请求方式，才有请求体，在请求体中封装了POST请求的请求参数
		1. 获取流对象
			*  BufferedReader getReader()：获取字符输入流，只能操作字符数据
			*  ServletInputStream getInputStream()：获取字节输入流，可以操作所有类型数据
		2. 再从流对象中拿数据
			
4. request其他功能：
	* 获取请求参数通用方式：不论get还是post请求方式都可以使用下列方法来获取请求参数
		1. String getParameter(String name):根据参数名称获取参数值    username=zs&password=123
		2. String[] getParameterValues(String name):根据参数名称获取参数值的数组  hobby=xx&hobby=game
		3. Enumeration<String> getParameterNames():获取所有请求的参数名称
		4. Map<String,String[]> getParameterMap():获取所有参数的map集合

		* 中文乱码问题：
			* get方式：tomcat 8 已经将get方式乱码问题解决了
			* post方式：会乱码
				* 解决：在获取参数前，设置request的编码request.setCharacterEncoding("utf-8");		
	* 请求转发：一种在服务器内部的资源跳转方式
		1. 步骤：
			1. 通过request对象获取请求转发器对象：RequestDispatcher getRequestDispatcher(String path)
			2. 使用RequestDispatcher对象来进行转发：forward(ServletRequest request, ServletResponse response) 
		2. 特点：
			1. 浏览器地址栏路径不发生变化
			2. 只能转发到当前服务器内部资源中。
			3. 转发是一次请求

	* 共享数据：
		* 域对象：一个有作用范围的对象，可以在范围内共享数据
		* request域：代表一次请求的范围，一般用于请求转发的多个资源中共享数据
			1. void setAttribute(String name,Object obj):存储数据
			2. Object getAttitude(String name):通过键获取值
			3. void removeAttribute(String name):通过键移除键值对
	* 获取ServletContext：
		* ServletContext getServletContext()