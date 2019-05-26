---
title: dubbo
---

### dubbo与zookeeper
##### dubbo
  * 为了实现远程服务调用的分布式框架，致力于提供高性能和透明化的RPC远程服务调用方案，以及SOA服务治理方案
  ![dubbo](https://s2.ax1x.com/2019/05/26/VVERdx.png)

##### zookeeper
  * Haddop的子项目，dubbo推荐使用作为注册中心,对服务的提供方与调用方进行统一的管理

### dubbo环境搭建
##### 安装注册中心
  * linux环境配置(安装jdk)
  * linux环境下安装zookeeper
	  * 下载zookeeper压缩包/将zookeeper压缩包上传到服务器
	  * 解压zookeeper压缩包
	  * 在解压后得到的zookeeper文件目录中创建data文件夹
	  * 修改config目录下的zoo_sample.cfg，将文件名更改为zoo.cfg
	  * 修改zoo.cfg的内容，将dataDir的值修改为data文件的路径
	  * 进入bin目录，执行./zkServer.sh start，启动zookeeper
  * 搭建本地服务
	  * 下载dubbo依赖jar包
	  * 手动将jar包安装到maven依赖仓库
		`mvn install:install-file -Dfile=d:\setup\dubbo-2.8.4.jar -DgroupId=com.alibaba -DartifactId=dubbo -Dversion=2.8.4 -Dpackaging=jar`
  * ide中配置dubbo的离线约束
	  * eclipse -> window -> preference -> xml -> add
  * 创建dubbo服务端
	  * 引入依赖
	  * 注册中心配置
		  * 第一步：dubbo服务起名称，一般与命名为和项目同名的
		  * 第二步：配置注册中心
			  * 地址和协议可以不用写死在xml，可以通过properties定义
			  * 集群
				`<dubbo:registry address="192.168.25.133:2181,192.168.200.134:2181,192.168.200.135:2181" protocol="zookeeper"/>`
			  * 单机（address:注册中心地址，protocol：因为使用了zookeeper作为注册中心，因此通信协议选择zookeeper）
				`<dubbo:registry address="192.168.25.133:2181" protocol="zookeeper"/>`
			  * 也可以写成这样的形式
				`<dubbo:registry address="zookeeper://192.168.25.133:2181"/>`
		  * 第三步：设置dubbo端口号(不显式设置的话端口号默认就是20880)
			  * 存在多个服务提供者时，就要为每个服务提供者设置监听不同dubbo端口号
			   `<dubbo:protocol name="dubbo" port="20880"/>`
		  * 第四步：设置服务提供方暴露的接口

		  ![服务提供方配置](https://s2.ax1x.com/2019/05/26/VVZ7VI.png)
	  * 服务代码
		  * 对外暴露，提供远程调用服务的类上要加上@Service注解（dubbo包，不是spring包）
  * 创建dubbo消费端
	  * 引入依赖
	  * 注册中心配置
	    
	    ![服务消费方配置](https://s2.ax1x.com/2019/05/26/VVeZM4.png)
	  * 消费代码
		  * 由于是远程调用，服务接口的实现类不在本工程，需要使用@Reference注解(dubbo包)来注入接口实现类
  * dubbo管理中心部署
	  * 方法一：
		  * 使用maven编译dubbox-admin源码,获得war包(打包前确认dubbo的jar包已经安装到本地的maven仓库)
			  * 进入dubbox-admin目录下，打开cmd,输入“mvn package -Dmaven.skip.test=true”(这样可以跳过测试，直接打包)
		  * 将duobbox-admin的war包部署到服务器的web容器中，启动tomcat即可
	  * 方法二：
		  * 直接将dubbo-admin作为本地项目导入IDE，通过IDE启动
	  * 扩展
		  * 如何将dubbo-admin配置到与zookeeper不同的服务器中(这是集群的前提)
			  * duobbo-admin的源码包的webapp\WEB-INF文件夹中dubbo.properties记录的注册中心地址是本机地址 dubbo.registry.address=zookeeper://127.0.0.1:2181 
			  * 修改为实际的注册中心地址即可
  * zookeeper集群
	  * 在zoo.config文件中配置集群节点信息（谁是主？谁是从？选举机制?怎么选的？集群一般部署多少才能尽可能避免平票？）
		  * 在zoo.config文件中配置集群节点信息（谁是主？谁是从？选举机制?怎么选的？集群一般部署多少才能尽可能避免平票？）
		  * dataDir=D:/devtools/zookeeper-3.2.2/build--保存数据的目录，日志也在这个目录里
		  * clientPort=2181--zookeeper监听的客户端端口号
		  * initLimit=5 --zookeeper主节点与从节点建立初始化连接时，容忍从节点的心跳时间数，比如心跳时间设置为2000(毫秒)，initLimit设置为5,超过5*2000，即10秒无法建立连接，则判定连接失败
		  * syncLimit=2--主从节点之间通信的应答时间限制
		  * server.A=B：C：D--集群节点信息，A表示该服务是第几号服务器，B表示该服务器的ip，C表示该服务器与Leader通信的端口，D表示如果leader挂了进行重新选举的端口
	  * 集群模式下还要配置一个文件 myid，这个文件在 dataDir 目录下，里面就有一个数据就是 A 的值，Zookeeper 启动时会读取这个文件，获取数据与 zoo.cfg 里面的配置信息比较从而判断是哪个 server
	  * 注册中心采用集群部署，多个服务实例在配置各个注册中心地址时，也要配置集群地址(zookeeper集群中所有实例的地址)
	 
### 扩展
##### 环境搭建过程中的常见问题
* 在注册中心找不到服务
	* 确认使用的@Service注解是com.alibaba.dubbo包下的，而不是spring包下的
	* 检查加了@Service注解的方法位置与配置文件定义的位置一致
	* 检查服务工程是否正常启动
* 无法连接到注册中心
	* 检查注册中心是否成功启动
	* 检查ip地址是否配置正确

##### dubbo的远程调用是如何实现的？
* 参考[《RPC框架》](./)观察以下实例
* 服务消费方地址
`consumer://192.168.12.1/cn.cyh.dubbox.service.TestService?application=dubbox-demo-consumer&category=consumers&check=false&dubbo=2.8.4&interface=cn.cyh.dubbox.service.TestService&methods=sayHello&pid=6336&side=consumer×tamp=1552551974936`
* 服务提供方地址
`dubbo://192.168.12.1:20880/cn.cyh.dubbox.service.TestService?anyhost=true&application=dubbox-demo-provider&dubbo=2.8.4&generic=false&interface=cn.cyh.dubbox.service.TestService&methods=sayHello&pid=16544&side=provider×tamp=1552551119194`
##### @Reference注解
* dubbo与spring集成
* spring进行报扫描，扫描到加了该注解的接口后，生成动态代理对象加入spring容器，这个动态代理对象专门负责远程通讯
	* 要实现动态代理，需要所有的远程调用都遵循一套模板，把远程调用的信息放到一个RpcRequest对象里面，发给Server端
	* Server端解析之后就知道消费端要调用的是哪个RPC接口、以及入参是什么类型、入参的值又是什么
	* 比如Dubbo的远程调用RpcInvocation
	![RpcInvocation](https://s2.ax1x.com/2019/05/26/VVn6Vs.png)

	