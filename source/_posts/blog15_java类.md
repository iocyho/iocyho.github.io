---
title: java类
---

#### 类加载机制
##### 什么是类的加载
* jvm通过一个类的全限定类名来获取定义此类的二进制字节流(不仅限于.class文件，也包括网络、动态生成、数据库等渠道获取的)，将字节流读取到内存中，存放到运行时数据区的方法区内，在堆中创建一个java.lang.Class(元信息对象)，用于封装在方法区内的结构信息(如构造函数、属性方法等)。
![类加载机制](https://s2.ax1x.com/2019/06/09/VsOKZd.png)
##### 类加载过程
* 加载
* 连接
* 初始化
![类加载过程](https://s2.ax1x.com/2019/06/09/VsOMdA.png)

#### 类加载器
1. 启动类加载器 BootStrap ClassLoader
	* 用C++编写的，java虚拟机的一部分，作用是将<JAVA HOME>\lib目录下，或是被-Xbootclasspath参数所指定路径的，能被jvm识别的(仅按文件名识别，如xx.jar)类加载到内存中。
2. 扩展类加载器 Extention ClassLoader
	* 用java编写，由sun.misc.Luncher$ExtClassLoader实现，作用是加载<JAVA HOME>\lib\ext目录下，或是被java.ext.dir系统变量所指定路径中的所有类库，开发人员可以直接使用扩展类加载器
3. 应用程序类加载器 Application ClassLoader
	* 用java编写，由sun.misc.Luncher$AppClassLoader实现，这个类加载器是ClassLoader中getSystemClassLoader()方法的返回值，因此也叫系统类加载器
	* 负责加载用户类路径(ClassPath)中所指定的类库。开发人员可以直接使用这个类加载器，如果程序中没有自定义的类加载器，这个就是默认的类加载器
4. 自定义类加载器
	* 使用的程序都是三种类加载器互相配合进行加载的，如果有必要，开发者也可以自定义类加载器，继承应用程序类加载器，重写findClass(String name)方法即可
![类加载器继承关系](https://s2.ax1x.com/2019/06/09/VsOQII.png)

#### 双亲委派模型
* 什么是双亲委派模型
	* 当一个类加载器收到加载请求，它首先不会自己尝试加载这个类，而是直接委派给父类加载器完成，每一层类加载器都如此，直到所有类加载请求都传递到顶层的类加载器中。只有当父类加载器无法完成加载请求(这个要加载的类不在它的搜索范围内)，子加载器才会自己尝试加载。
* 为什么要有双亲委派模型
	* java类与它的类加载器一起，具备了一种带有优先级的层次关系，保障Java体系的一致性。
	* 每个类加载器只加载自己范围内的类，避免一个类被重复加载。
	* 比如java.lang.Object类存放在<JAVA_HOME>\lib目录中的rt.jar中，任何一个类加载器想加载它最终都会委派到顶层的启动类加载器去加载。如果不使用双亲委派模型，用户可以自定义一个java.lang.Object类，存放在ClassPath，由应用程序加载器去直接加载，就会导致系统中出现多个Object,类，造成混乱。

#### 实例化一个类
* 实例化一个类的方法(创建对象)
	1. 通过new关键字创建对象
	2. 通过工厂方法返回对象，String str = String.valueOf(23)
	3. 通过反射返回对象
	4. 通过clone()方法克隆对象
	5. 通过IO流反序列化获取对象
* instanceof 与 instance()
	* instanceof是一个运算符，obj instanceof class 判断对象是否属于该类，成立返回true，否则返回false
	* instance()是类的一个方法，claz.instance(obj)，判断当前类能不能实例化成该对象，成立返回true，否则返回false