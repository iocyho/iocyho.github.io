---
title: xml笔记
---

## xml的作用
  * ##### Extensible Markup Language：可扩展标记语言
  * ##### 用于数据存储
	  1. 作为配置文件(标签灵活，语法严格)
	  2. 用于网络数据中的数据传输(跨平台)
  * ##### XML与HTML
  
项目|XML|HTML
:-|:-:|:-:
标签|自定义|预定义
语法|严格|松散
用途|存储数据|展示数据
	   

## xml的组成
  * ##### 文档声明
	  * 格式：<?xml 属性列表 ?>
	  * 属性列表:
		  * version 版本号
		  * encoding 编码方式（告知解析引擎当前文档的编码，如果与实际文档编码不匹配会解析报错）默认编码 ISO-8859-1
		  * standalone 是否独立
			  * yes 不依赖其他文件
			  * no 依赖其他文件
  * ##### 标签
	  * <标签></标签>
	  * 名称：自定义
	  * 属性：自定义
  * ##### 文档
	  * <标签>文档内容</标签>
	  * 内容：自定义
	  * 数据原样展示(特殊字符不解析)：CDATA区
		  * <![CDATA[要展示的数据]]>

## xml的约束
#### 什么是约束文档 
  * ##### 规定xml的标签规则的文档
  	* 程序规定xml标签规则，用户按规则编写
  	* 用户编写xml,程序解析
 
#### 约束文档技术分类
  1. ##### DTD
  * 相对简单的约束技术
  * 引入
    * 内部引入： 
	    * 直接将约束规则定义在xml文件中<!DOCTYPE 跟标签名称 [ ...具体的约束规则... ] >
    * 外部引入：
	    * 本地引入: <!DOCTYPE SYSTEM "dtd文件位置">
	    * 网络引入: <!DOCTYPE PUBLIC "dtd文件名字" "dtd文件url"> 

  2. ##### Schema
  * 相对复杂的约束技术
    * 不仅是对标签进行约束，对内容也能进行更加细致的约束
    * 本质上也是一个xml文件
  * 引入
    * 填写xml文档的根元素
    * 引入xsi前缀(由w3c规定，有多个版本，按需引入)： 
	    * xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    * 引入xsd文件命名空间： 
	    * xsi:schemaLocation="xsd文件所在包  xsd文件url"
	* 为每一个xsd文件声明一个前缀作为标识(区分名字相同的标签属于哪个xsd文件)：
		* xmlns:xxx="xsd文件url" 
		* xxx可以留空不写，同时一般是同时引入多个约束时加上用以区分
	* 例子: spring的Schema约束：
	```
    <beans xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		xmlns="http://www.springframework.org/schema/beans" 
		xmlns:context="http://www.springframework.org/schema/context"
		xmlns:services="http://www.tesla.com/schema/services" 
		xmlns:functions="http://www.tesla.com/schema/services/functions"
		xmlns:tx="http://www.springframework.org/schema/tx"
		xsi:schemaLocation="
			http://www.springframework.org/schema/beans 
			http://localhost:8080/schema/www.springframework.org/schema/beans/spring-beans.xsd
			http://www.springframework.org/schema/tx 
			http://localhost:8080/schema/www.springframework.org/schema/tx/spring-tx-3.1.xsd
			http://www.springframework.org/schema/context 
			http://localhost:8080/schema/www.springframework.org/schema/context/spring-context.xsd
			http://www.tesla.com/schema/services 
			http://localhost:8080/schema/services.xsd
			http://www.tesla.com/schema/services/functions 
			http://localhost:8080/schema/services-functions.xsd">
    ```


## 操作xml

  * #### 解析方式
	  *  DOM 将标记语言文档一次性加载进内存，在内存中形成一棵DOM树
		  *  优点：操作方便，可以进行CRUD所有操作
		  *  缺点：占用大量内存资源
	  *  SAX 逐行读取(读一行释放一行)，基于事件驱动(监听器)
		  *  优点：内存占用小
		  *  缺点：只能读，不能增删改
  * #### 常见解析工具
	  *  JAXP 同时支持DOM和SAX两种思想
	  *  Dom4j 常用
	  *  Jsoup HTML的解析器，也支持解析XML，常用
	  *  PULL 安卓内置，SAX方式

  * #### Jsoup
	  * ##### 解析：将xml文档中的内容加载到内存
		  * ###### 获取对象
			  1. Jsoup 工具类，可以解析HTML或HTML,返回Document对象
				  * parse 解析HTML或HTML，返回Document对象
					  * 读取HTML或XML文件
						  *  parse​(File in, String charsetName) 
					  * 解析HTML或XML字符串
						  * parse(String html) 
					  * 通过url获取HTML或XML，进行解析
						  * parse(URL url, int timeoutMillis) 
				 
			  2. Document 文档对象，代表内存中的DOM树
				  * 获取Element对象(类似于js的语法)
					  * getElementByXXX()
						  * getElementById​(String id)：根据id属性值获取唯一的element对象
					  * getElementsByXXX()
						  * getElementsByTag​(String tagName)：根据标签名称获取元素对象集合
						  * getElementsByAttribute​(String key)：根据属性名称获取元素对象集合
						  * getElementsByAttributeValue​(String key, String value)：根据对应的属性名和属性值获取元素对象集合
			  3. Elements
				  * Element对象的集合，当作ArrayList<Element>使用即可
			  4. Element
				  * 获取子元素对象
					  * 同Document的API，getElementByXXX()/getElementsByXXX()
				  * 获取属性值
					  * String attr(String key) 根据属性名称获取属性值
				  * 获取文本内容
					  * String text() 获取文本内容
					  * String html() 获取标签内的所有内容(包括子标签，以字符串的形式)
			  5. Node
				  * Document和Element的父类
		  *  ###### 快捷查询：快速获取对象，而不是一层一层剥离
			  1. selector: 选择器
				  * 使用方法参考Selector类的API
			  2. XPath: XML路径语言,用于快速定位XML中某部分文档的位置
				  *  使用方法参考 [w3school](http://www.w3school.com.cn/x.asp)
				  
	  * ##### 写入：将内存中的数据保存到xml文档，持久化存储
		  * 
				  
  * #### Dom4j
	  * ##### 解析
		  * ###### 获取对象
			  1. Doument
				  * 读取XML文件获得Document
					  * SAXReader reader = new SAXReader()；
					  * Document document = reader.read(new File("xxx.xml"))；
				  * 解析XML字符串获得Document
					  * Document document = DocumentHelper.parseText("<abc></abc>")；
				  * 主动创建Document
					  * Document document = DocumentHelper.createDocument()；
			  2. Element
				  * 获取文档根节点
					  * Element root = document.getRootElement()；
				  * 根据名称获取子节点
					  * Element element = node.element("节点名称")；
				  * 获取子节点列表
					  * List nodes = node.elements("可以指定名称")；
				  * 获取节点文本内容
					  * String text = node.getText();
			  3. Attribute
				  * 获取属性对象
					  * Attribute attribute= node.attribute("属性名称");

		  * ###### 操作对象
			  * 新增
				  * 添加子节点
					  * Element newElm = element.addElement("名称")；
				  * 添加CDATA节点
					  * newElm.addCDTAA("具体内容")
			  * 修改
				  * 设置节点属性
					  * element.setXXX("属性值")；
			  * 删除
				  * 删除子节点
					  * parentElement.remove(childElment);
				  * 删除属性
					  * element.remove(要删除的attribute对象)；
				  * 
	  * ##### 写入
		  1. 将Document对象写入XML文件
			  ```
			  // 文件输出的时候自动缩进
			  OutputFormat format = OutputFormat.cretaePrettyPrint()；
              // 设置文档编码格式
			  format.setEncoding("UTF-8");
              XMLWriter writer = new XMLWriter(new FileWriter("xxx.xml"),format);
			  writer.write(document);
			  writer.close;
			  ``` 

		  2. 将Document和Element对象转换为字符串
			  ```
			  // 获取Document对象
			  SAXReader reader = new SAXReader();
			  Document document = reader.read(new File("xxx.xml"));
			  // 获取根节点对象
			  Element root = document.getRootElement();
			  // 将document转换为字符串
			  String docXmlText = document.asXML();
			  // 将根节点转换为字符串
			  String elmXmlText = root.asXML();
              ```


## 参考资料
###### [w3school|XML 系列教程](http://www.w3school.com.cn/x.asp)
###### [使用Dom4j解析XML](https://blog.csdn.net/redarmy_chen/article/details/12969219)
