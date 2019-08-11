---
title: Cookie&Session
---

### 会话技术
* 什么是会话：
	* 一次会话中包含多次请求和响应。
	* 一次会话：浏览器第一次给服务器资源发送请求，会话建立，直到有一方断开为止
* 会话的作用：
	* 在一次会话的范围内的多次请求间，共享数据
* 会话实现方式：
	1. 客户端会话技术：Cookie
	2. 服务器端会话技术：Session

### Cookie
* 什么是Cookie：
	* 客户端会话技术，将数据保存到客户端
* Cookie使用方法：
	```
	// 1.创建Cookie对象，绑定数据
	new Cookie(String name, String value) 
	// 2.发送Cookie对象
	response.addCookie(Cookie cookie) 
	// 3.获取Cookie，拿到数据
	Cookie[]  request.getCookies()
	```
* Cookie实现原理
	* 基于响应头set-cookie和请求头cookie实现
	![cookie原理](https://s2.ax1x.com/2019/08/11/evqE40.jpg)
* cookie的细节
	1. 一次可不可以发送多个cookie?
		* 可以创建多个Cookie对象，使用response调用多次addCookie方法发送cookie即可。
	2. cookie在浏览器中保存多长时间？
		1. 默认情况下，当浏览器关闭后，Cookie数据被销毁
		2. 持久化存储：
			* setMaxAge(int seconds)
				1. 正数：将Cookie数据写到硬盘的文件中。持久化存储。并指定cookie存活时间，时间到后，cookie文件自动失效
				2. 负数：默认值
				3. 零：删除cookie信息
	3. cookie能不能存中文？
		* 在tomcat 8 之前 cookie中不能直接存储中文数据。
			* 需要将中文数据转码---一般采用URL编码(%E3)
		* 在tomcat 8 之后，cookie支持中文数据。特殊字符还是不支持，建议使用URL编码存储，URL解码解析
	4. cookie共享问题？
		1. 假设在一个tomcat服务器中，部署了多个web项目，那么在这些web项目中cookie能不能共享？
			* 默认情况下cookie不能共享
			* setPath(String path):设置cookie的获取范围。默认情况下，设置当前的虚拟目录
				* 如果要共享，则可以将path设置为"/"
		2. 不同的tomcat服务器间cookie共享问题？
			* setDomain(String path):如果设置一级域名相同，那么多个服务器之间cookie可以共享
				* setDomain(".baidu.com"),那么tieba.baidu.com和news.baidu.com中cookie可以共享
* Cookie的特点和作用
	* 特点
		1. cookie存储数据在客户端浏览器
		2. 浏览器对于单个cookie 的大小有限制(4kb) 以及 对同一个域名下的总cookie数量也有限制(20个)
	* 作用：
		1. cookie一般用于存出少量的不太敏感的数据
		2. 在不登录的情况下，完成服务器对客户端的身份识别


### Session
* 什么是Session：
	* 服务器端会话技术，在<font color=red size=5>一次会话</font>的多次请求间共享数据，将<font color=blue>数据保存在服务器端</font>的对象中。HttpSession
* Seesion使用方法：
	```
	// 1.获取HttpSession对象：
	HttpSession session = request.getSession();
	// 2.使用HttpSession对象：
	Object getAttribute(String name)  
	void setAttribute(String name, Object value)
	void removeAttribute(String name)  
	```
* Session实现原理
	* Session的实现是依赖于Cookie的。
	![Session实现原理](https://s2.ax1x.com/2019/08/11/evqZCV.jpg)
* Session细节：
	1. 当客户端关闭后，服务器不关闭，两次获取session是否为同一个？
		* 默认情况下。不是。
		* 如果需要相同，则可以创建Cookie,键为JSESSIONID，设置最大存活时间，让cookie持久化保存。
			 Cookie c = new Cookie("JSESSIONID",session.getId());
	         c.setMaxAge(60*60);
	         response.addCookie(c);
	2. 客户端不关闭，服务器关闭后，两次获取的session是同一个吗？
		* 不是同一个，但是要确保数据不丢失。tomcat自动完成以下工作
			* session的钝化：
				* 在服务器正常关闭之前，将session对象序列化到硬盘上
				* 由tomcat实现，在tomcat的work目录中生成一个.ser文件
			* session的活化：
				* 在服务器启动后，读取session文件反序列化为内存中的session对象即可。
				* 由tomcat实现，将work目录中的.ser文件加载进内存			
	3. session什么时候被销毁？
		1. 服务器关闭时被销毁
		2. session对象调用invalidate() 。
		3. session默认失效时间 30分钟,选择性配置修改，修改tomcat的web.xml文件
			```	
			<session-config>
		        <session-timeout>30</session-timeout>
		    </session-config>
			```
* session的特点
	* session的特点
		1. session用于存储一次会话的多次请求的数据，存在服务器端
		2. session可以存储任意类型，任意大小的数据
	* session与Cookie的区别：
		1. session存储数据在服务器端，Cookie在客户端
		2. session没有数据大小限制，Cookie有
		3. session数据安全，Cookie相对于不安全