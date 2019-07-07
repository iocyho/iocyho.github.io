---
title: RabbitMQ
---

#### AMQP与JMS
* AMQP Advanced Message Queuing Protocol ,是一套公开的消息队列协议，从协议层定义消息通信数据的标准格式，目的是解决MQ市场上消息协议不统一的问题
* JMS Java Message Service 是一套消息服务的API标准，为所有的java应用程序提供统一的消息通信规范，类似于jdbc，只要遵循jms规范的java应用程序都可以进行通信。
* JMS是在API层面定义标准，专属于java的；AMQP是在协议层定义标准的，可以跨语言。

#### RabbitMQ工作原理
* ![RabbitMQ工作原理](https://s2.ax1x.com/2019/07/07/ZDeLB8.png)
* 组成部分
	* Broker：消息队列服务进程，此进程包括两个部分：Exchange和Queue
	* Exchange：消息队列交换机，按一定的规则将消息路由转发到某个队列，对消息进行过滤
	* Queue：消息队列，存储消息的队列，消息到达队列并转发给指定的消费方
	* Producer：消息生产者，即生产方客户端，生产方客户端将消息发送到MQ
	* Consumer：消息消费者，即消费方客户端，接收MQ转发的消息
* 发布消息流程
	* 生产者和Broker建立TCP连接
	* 生产者和Broker建立通道
	* 生产者通过通道消息发送给Broker，由Exchange将消息进行转发
	* Exchange将消息转发到指定的Queue（队列）
* 接收消息流程
	* 消费者和Broker建立TCP连接
	* 消费者和Broker建立通道
	* 消费者监听指定的Queue（队列）
	* 当有消息到达Queue时Broker默认将消息推送给消费者
	* 消费者接收到消息

#### RabbitMQ工作模式
1. 工作队列模式
	* 多个消费者监听同一个队列，但一条消息不会被多个消费者消费，而是采用轮询的方式依次发给不同的消费者
2. 发布订阅模式
	* 一个生产者将消息发送给交换机
	* 交换机绑定多个队列，每个消费者监听自己的队列
	* 生产者将消息发送给交换机，交换机将消息转发到绑定此消息的每个队列，每个与此交换机绑定的队列都会收到消息
3. 路由模式
	* 每个消费者监听自己的队列，并设置routingkey
	* 生产者将消息发送给交换机，交换机根据routingkey来将消息发送给指定队列（队列的routingkey与生产者发送消息时指定的routingkey相等）
4. topic模式
	* 每个消费者监听自己的队列，并设置带通配符的routingkey
	* 单词间以"."分隔,符号*可以匹配一个词，符号#可以匹配多个词
	* 生产者将消息发送给交换机，交换机跟据routingkey来将消息发送给指定队列（队列的routingkey与生产者发送消息时指定的routingkey匹配）
5. header模式
	* 不设置路由模式的routingkey，而是设置key/value形式的header来进行队列匹配（routingkey是一个值，header是一个map，貌似没有太大的区别）
6. rpc模式
	* 使用MQ来实现的客户端远程调用服务端方法，基于Direct交换机实现（一个解决高并发问题的方案，让并发请求排队）
	* 客户端发送请求给MQ，MQ转发请求给服务端，服务接收请求后执行方法，方法执行完毕后发消息给MQ，MQ转发执行结果给客户端。

#### RabbitMQ安装
* 安装RabbitMQ对应版本的Erlang运行环境
* 安装RabbitMQ，启动RabbitMQ服务
* 创建java工程,引入RabbitMQ的java客户端依赖
* 注意事项：
	* 计算机用户名不能为中文字符，否则RabbitMQ服务会无法启动。如果Rabbit已经在用户名为中午的情况下安装了RabbitMQ,即使把用户名改为英文，用户文件夹还是中文改不过来了。
	* 解决方法：
		* 管理员运行cmd，打开RabbitMQ安装目录
		* 输入以下命令，路径可以设置成为自定义路径
		* rabbitmq-service.bat remove (移除服务)
		* set RABBITMQ_BASE=D:\install3\rabbitmq\rabbitmq_server-3.7.8\data (更改RabbitMQ默认日志和数据都存储路径)
		* rabbitmq-service.bat install (重新安装服务)
		* rabbitmq-plugins enable rabbitmq_management (激活插件管理)


#### RabbitMQ使用
* 与SpringBoot整合
	* 引入AMQP依赖
	* 定义配置类，将交换机bean加载进spring容器