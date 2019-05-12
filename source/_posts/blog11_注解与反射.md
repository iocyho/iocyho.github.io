---
title: 反射与注解
---

### 反射
##### 什么是反射
* 将类的各个组成部分封装成其他对象

##### 如何获取Class类对象
* Class.forName("全限定类名") -- 源码阶段 
* 类名.class -- Class类对象阶段(程序启动，类在内存中被创建)
* 对象.getClass() -- Runtime运行时阶段

##### 得到类的Class对象后可以做什么
* 获取包名
	* String getPackage()
* 获取类名
	* String getName()
* 创建对象实例
	* claz.newInstcane()
* 操作类的成员变量
* 操作类的构造方法
* 操作类的成员方法
	* Method[] getMethods() 获取所有**public修饰**的成员方法
	* Method getMethod(String methodName) 根据名称获取**public修饰**的指定方法
	* method.invoke(对象实例) 反射调用对象的方法
	* 暴力反射
		* <font color=red>忽略访问权限修饰符的安全检查</font>
			* setAccessible(true)
		* 获取**任意权限**的成员变量、构造方法、成员方法
			* get**Declare**Fields()
			* get**Declare**Constructors()
			* get**Declare**Methods()
* <font color=red>参考JDK_Api</font>


### 注解
##### 什么是注解
* 一种代码级别的说明，与类、接口、枚举在同一个层次。可以声明在包、类、字段、参数等前面，用于对这些元素进行说明
* <font color=red>注解本质上就是一个接口，该接口默认继承Annotation接口</font>
* 注解与注释
	* 注解用于说明程序，给计算机看的
	* 注释用文字描述程序，给程序员看的

##### 注解的作用
* 编写文档：通过代码里标识的注解生成doc文档(就是文档注释中的注解)
* 代码分析：通过代码里标识的注解对代码进行分析(通过反射实现)
* 编译检查：通过代码里标识的注解，让编译器实现最基本的检查(如@Override)

##### 使用注解
* java的预定义注解
	* @Override 检测方法是否是继承(实现)父类(接口)的
	* @Deprecated 标注该注解的内容已过时
	* @SupperssWarnnings 压制警告
* 自定义注解
	```
	元注解
	public @interface 注解名称{
		注解属性
	}
	```


* 元注解-用于描述注解的注解
	* @Target 描述注解的作用位置
	* @Retention 描述注解被保留的阶段
	* @Documented 描述注解是否会被抽取到Api文档中
	* @Inherited 描述注解是否被子类继承
* 注解属性
	* 属性返回值类型：
		* 基本数据类型
		* String
		* 枚举
		* 注解
		* 以上类型的一维数组
	* 属性赋值
		* 定义注解时，使用default关键字给注解进行初始化赋值，使用注解时可以不对该属性赋值
		* 有且仅有一个属性，且属性名位value时，可以省value(如spring的各种注解)
		* 数组赋值时，使用{}包裹，如果数组中只有一个值，{}可以省略
* **在程序中使用注解**
	* <font color=orange>检查对象上是否有指定的注解</font>
		1. 获取注解定义位置的对象(Class、Method、Field)
		2. 对象.isAnnotationPresent(注解字节码)
	* <font color=orange>获取注解中的属性值</font>
		1. 获取注解定义位置的对象(Class、Method、Field)
		2. 获取指定注解 getAnnotation(注解的字节码)
			这一步本质是在内存中生成了一个该注解接口的子类实现对象
		3. 调用注解中的抽象方法获取配置的属性值