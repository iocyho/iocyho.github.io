---
title: jsp
---

### jsp
* 什么是jsp：Java Server Pages： java服务器端页面
	* 个特殊的页面，其中既可以指定定义html标签，又可以定义java代码，出现的初衷是用于简化书写
* jsp原理
	* 在程序运行过程中，由web容器在work目录下生成一个对应的xx_jsp.java类，该类继承了HttpJspBase
	* HttpJspBase继承HttpServlet，因此JSP本质上就是一个Servlet
* jsp的脚本：JSP定义Java代码的方式
	1. <%  代码 %>：定义的java代码，在service方法中。service方法中可以定义什么，该脚本中就可以定义什么。
	2. <%! 代码 %>：定义的java代码，在jsp转换后的java类的成员位置。
	3. <%= 代码 %>：定义的java代码，会输出到页面上。输出语句中可以定义什么，该脚本中就可以定义什么。
* jsp指令
	* 作用：用于配置JSP页面，导入资源文件
	* 格式：
		<%@ 指令名称 属性名1=属性值1 属性名2=属性值2 ... %>
	* 分类：
		1. page : 配置JSP页面
			* contentType：等同于response.setContentType()
				1. 设置响应体的mime类型以及字符集
				2. 设置当前jsp页面的编码（只能是高级的IDE才能生效，如果使用低级工具，则需要设置pageEncoding属性设置当前页面的字符集）
			* import：导包
			* errorPage：当前页面发生异常后，会自动跳转到指定的错误页面
			* isErrorPage：标识当前也是是否是错误页面。
				* true：是，可以使用内置对象exception
				* false：否。默认值。不可以使用内置对象exception
		2. include ：页面嵌套。导入页面的资源文件
			* <%@include file="top.jsp"%>
		3. taglib ：导入资源
			* <%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
				* prefix：前缀，可以自定义
* jsp注释:
	1. html注释：
		<xmp><!-- -->: 只能注释html代码片段</xmp>
	2. jsp注释：推荐使用
		<%-- --%>：可以注释所有
* JSP的内置对象：
	* 在jsp页面中不需要获取和创建，可以直接使用的对象
	* jsp的9个内置对象:
		1. pageContext		PageContext					当前页面共享数据，还可以获取其他八个内置对象
		2. request			HttpServletRequest			一次请求访问的多个资源(转发)
		3. session			HttpSession					一次会话的多个请求间
		3. application		ServletContext				所有用户间共享数据
		3. response			HttpServletResponse			响应对象
		4. page				Object						当前页面(Servlet)的对象  this
		5. out				JspWriter					输出对象，数据输出到页面上
			* response.getWriter()和out.write()的区别：
			* 在tomcat服务器真正给客户端做出响应之前，会先找response缓冲区数据，再找out缓冲区数据。
			* response.getWriter()数据输出永远在out.write()之前
		6. config				ServletConfig			Servlet的配置对象
		7. exception			Throwable					异常对象

### EL表达式
* 什么是EL表达式：
	* Expression Language 表达式语言
* EL表达式作用：
	* 替换和简化jsp页面中java代码的编写
* EL表达式语法：
	* ${表达式}
	* 注意事项：jsp默认支持el表达式。如果要忽略el表达式
		1. 设置jsp中page指令中：isELIgnored="true" 忽略当前jsp页面中所有的el表达式
		2. \${表达式} ：忽略当前这个el表达式
* EL表达式语法：
	1. 运算：
		1. 算数运算符： + - * /(div) %(mod)
		2. 比较运算符： > < >= <= == !=
		3. 逻辑运算符： &&(and) ||(or) !(not)
		4. 空运算符： empty
			* 功能：用于判断字符串、集合、数组对象是否为null或者长度是否为0
			* ${empty list}:判断字符串、集合、数组对象是否为null或者长度为0
			* ${not empty str}:表示判断字符串、集合、数组对象是否不为null 并且 长度>0
	2. 获取值
		1. el表达式只能从域对象中获取值
		2. 语法：
			1. ${域名称.键名}：从指定域中获取指定键的值
				* 域名称：
					1. pageScope		--> pageContext
					2. requestScope 	--> request
					3. sessionScope 	--> session
					4. applicationScope --> application（ServletContext）
				* 举例：在request域中存储了name=张三
				* 获取：${requestScope.name}
			2. <font color=red>${键名}：表示依次从最小的域中查找是否有该键对应的值，直到找到为止。</font>
			3. 获取对象、List集合、Map集合的值
				1. 对象：${域名称.键名.属性名}
					* 本质上会去调用对象的getter方法
				2. List集合：${域名称.键名[索引]}
				3. Map集合：
					* ${域名称.键名.key名称}
					* ${域名称.键名["key名称"]}
	3. 隐式对象：
		* el表达式中有11个隐式对象
		* pageContext：
			* 获取jsp其他八个内置对象
				* ${pageContext.request.contextPath}：动态获取虚拟目录

### JSTL
* 什么是JSTL：
	* JavaServer Pages Tag Library  JSP标准标签库
	* 由Apache组织提供的开源的免费的jsp标签
* JSTL的作用：
	* 把jsp页面上的java代码替换成标签
	* 简化jsp页面
* JSTL使用步骤：
	1. 导入jstl相关jar包
	2. 引入标签库：taglib指令：  <%@ taglib %>
	3. 使用标签
* 常用的JSTL标签
	1. if:相当于java代码的if语句
		1. 属性：
            * test 必须属性，接受boolean表达式
                * 如果表达式为true，则显示if标签体内容，如果为false，则不显示标签体内容
                * 一般情况下，test属性值会结合el表达式一起使用
   		 2. 注意：
       		 * c:if标签没有else情况，想要else情况，则可以再定义一个c:if标签
	2. choose:相当于java代码的switch语句
		1. 使用choose标签声明         			相当于switch声明
        2. 使用when标签做判断         			相当于case
        3. 使用otherwise标签做其他情况的声明    	相当于default
	3. foreach:相当于java代码的for语句
		1. 完成重复的操作
			```
			<c:forEach begin="1" end="10" var="i" step="2" varStatus="s">
    			${i} <h3>${s.index}<h3> <h4> ${s.count} </h4><br>
			</c:forEach>
			```
			* begin：开始值
            * end：结束值
            * var：临时变量
            * step：步长
            * varStatus:循环状态对象
                * index:容器中元素的索引，从0开始
                * count:循环次数，从1开始
        2. 遍历容器
	        ```
		    <c:forEach items="${list}" var="str" varStatus="s">
		      ${s.index} ${s.count} ${str}<br>
		    </c:forEach>	
			```
			* items:容器对象
            * var:容器中元素的临时变量
            * varStatus:循环状态对象
	            * index:容器中元素的索引，从0开始
	            * count:循环次数，从1开始
