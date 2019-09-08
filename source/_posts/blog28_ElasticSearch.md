---
title: ElasticSearch
---

### 说明
* 基于lucene的搜索引擎，可以实现全文检索和及时搜索
* 优势：
	* 基于RESTFUL web的接口
	* 分布式、高可扩展性
* 逻辑结构：倒排索引表
	1. 将要搜索的文档内容分词，所有不重复的词组成分词列表。
	2. 将搜索的文档最终以Document方式存储起来。
	3. 每个词和docment都有关联。
	* 根据分词查询对应的文档，查询到多个文档时，根据一定的算法(如：相似性算法，仅计算匹配词条的数量)选择最优的一个


### 安装
* ElasticSearch 服务
	* 下载 https://www.elastic.co/downloads/past-releases
	* 解压
	* 修改配置文件 /
* ElasticSearch-head 可视化插件(基于node.js)
	* git clone git://github.com/mobz/elasticsearch-head.git
	* npm install
	* npm run start


#### 集群
* 集群说明
	1. 结点
		* ES集群由多个服务器组成，每个服务器即为一个Node结点(该服务只部署了一个ES进程)。
	2. 分片
		* 当文档量很大时，由于内存和硬盘的限制，同时也为了提高ES的处理能力、容错能力及高可用能力，将索引分成若干分片(每个分片都是一个lucene实例)
		* 每个分片可以放在不同的服务器，实现多个服务器共同对外提供索引及搜索服务
		* 当一个搜索请求过来，会分别从各各分片去查询，最后将查询到的数据合并返回给用户
	3. 副本
		* 为了提高ES的高可用同时也为了提高搜索的吞吐量，将分片复制一份或多份存储在其它的服务器，
		即使当前的服务器宕机，拥有副本的服务器照常可以提供服务。
	4. 主结点
		* 一个集群中会有一个或多个主结点，主结点的作用是集群管理，比如增加节点，移除节点等，主结点挂掉后ES会重新选一个主结点。
	5. 结点转发
		* 每个结点都知道其它结点的信息，因此可以对任意一个结点发起请求，接收到请求的结点会转发给其它结点查询数据。
* 节点角色
	1. 主节点 master
		* 用于集群的管理及索引，比如新增结点、分片分配、索引的新增和删除等
	2. 数据节点 data
		* 保存了数据分片，负责索引和搜索操作
	3. 客户端节点 client
		* 仅作为请求客户端存在，client也作为负载均衡器，client节点不存数据，只是将请求均衡转发到其它结点

### 使用
1. 索引管理
	* 每个索引包含term(分词)和document(文档)
	* 对索引进行CRUD操作(通过发送HTTP请求实现)
		* 创建索引
		```
		put http://el的ip:端口/索引名称

		{
			"settings":{
				"index":{
					"number_of_shards":1,#分片的数量
					"number_of_replicas":0#副本数量
				}
			}
		}
		```
2. 文档管理
	* 对文档进行CRUD操作(通过发送HTTP请求实现)
		* 添加文档
			* PUT /{index}/{type}/{id} { "field": "value", ... } 如果不指定id，ES会自动生成。
		* 查询文档
			* GET /{index}/{type}/{id}
		* 修改文档
			* ES更新文档的顺序是 先检索到文档、将原来的文档标记为删除、创建新文档、删除旧文档，创建新文档就会重建索引
			* 通过请求Url有两种方法：
				1. 完全替换 POST：/{index}/{type}/{id} 根据id删除
				2. 局部更新 POST: /{index}/{type}/{id}/_update 搜索匹配删除，将搜索出来的记录删除
		* 删除文档
			1. DELETE /{index}/{type}/{id} 
			2. POST /{index}/{type}/_delete_by_query
3. 映射管理
	* 每个document中包含一个或多个field
	* 对映射进行CRUD操作(通过发送HTTP请求实现)
		* 创建映射
		```
		put http://localhost:9200/索引库名称/类型名称/_mapping

		{
			"properties": {
					"name": {
					"type": "text",
					"analyzer":"ik_max_word",
					"search_analyzer":"ik_smart"
				},
				"description": {
					"type": "text",
					"analyzer":"ik_max_word",
					"search_analyzer":"ik_smart"
				},
				"studymodel": {
					"type": "keyword"
				},
				"price": {
					"type": "float"
				},
				"timestamp": {
					"type": "date",
					"format": "yyyy‐MM‐dd HH:mm:ss||yyyy‐MM‐dd||epoch_millis"
				}
			}
		}
		```
* 注意！：
	* 6.0之前的版本有type（类型）概念，type相当于关系数据库的表，ES官方将在ES9.0版本中彻底删除type。
	* 创建索引库相当于关系数据库中的数据库还是表？
		* 如果相当于数据库就表示一个索引库可以创建很多不同类型的文档，这在ES中也是允许的
		* 如果相当于表就表示一个索引库只能存储相同类型的文档，ES官方建议 在一个索引库中只存储相同类型的文档
		
* 映射(field)
	* document中除了可以保存字符串类性的field，还能保存数值、日期等类型
	* 常用映射数据类型
		1. 字符串：
			* text 会分词
			* keyword 不分词，keyword字段为关键字字段，通常按照整体搜索，因此创建keyword字段的索引时是不进行分词的，比如：邮政编码、手机号码、身份证等。keyword字段通常用于过虑、排序、聚合等		
		2. 数值
			byte、short、integer、long、float、double、half_float、scaled_float
			与一般的数值基本类型类似
			*(优化) 尽量选择范围小的类型，提高搜索效率
			*(优化) 对于浮点数尽量用比例因子scaled_float()，把浮点变为整型再存储，好处是整型比浮点型更易压缩，节省磁盘空间，没有适合的比例因子时再选择范围最小的
			* half_float < float < double		
		3. 日期
		4. 布尔
		5. 二进制
		6. 等级 range（用于范围过虑）

### 分词器
* 什么是分词器？
	* 添加文档时会进行分词，索引中存放的就是一个一个的词（term），当搜索时就是用关键字匹配词(term)，最终找到词关联的文档。
	* 因此对一段内容创建索引，首先就要对该内容进行分词，分词通过分词器实现
	* ElasticSearch默认使用的分词器- analyze 是将单个的字作为分词条件，针对中文分词时不适用
	* 中文分词使用IK作为分词器，需要下载IK，解压后复制到ElasticSearch安装目录的plugins下的ik目录下
* 分词模式：
	* ik_max_word：会将文本做最细粒度的拆分，比如会将“中华人民共和国人民大会堂”拆分为 中华人民共和国、中华人民、中华、华人、人民共和国、人民、共和国、大会堂、大会、会堂等
	* ik_smart：会做最粗粒度的拆分，比如会将“中华人民共和国人民大会堂”拆分为 中华人民共和国、人民大会堂
	
	*(优化) 建议创建索引时使用ik_max_word将搜索内容进行细粒度分词，搜索时使用ik_smart提高搜索精确性
		
* 自定义词库：让分词器支持一些专有词语(扩展默认字典)
	* iK分词器自带一个main.dic的文件，此文件为词库文件
	* 在目录中新建一个my.dic文件，作为扩展词库
	* 在配置文件中添加扩展词库配置，引入my.dic

### 过滤器
* 过虑是针对搜索的结果进行过虑，过虑器主要判断的是文档是否匹配，不去计算和判断文档的匹配度得分，所以过虑器性能比查询要高，且方便缓存
* (优化) 推荐尽量使用过虑器去实现查询或者过虑器和查询共同使用