---
title: 集合
---

#### value(Collection)
* List-存储的值有序可重复
	* ArrayList与LinkedList
		* ArrayList底层使用的是数组，数组有索引，因此可以快速查询指定位置元素，但是由于添加或是删除元素时需要移动内存空间，因此修改比较慢
		* LinkedList底层使用的是链表，增删快(修改不需要移动内存，只需要改变引用指向)，查询慢(由于元素在内存中的地址不连续，因此需要从头到尾遍历)
* Set-存储的值无序不可重复。根据hashCode(位置)和equals(内容)判断集合中的元素是否重复，如果一个对象要存储在set中，必须重写hashCode和equals方法

#### key-value(Map)
* HashMap
	* ![HashMap结构1.7](https://s2.ax1x.com/2019/06/30/Z3E59I.png)
	* 大方向上HashMap底层是一个Node数组(可能不是每个存储空间中都有实例内容)
		* capacity-数组容量，初始16，大小保持位2^n，每次*2扩容
		* loadFactor-负载因子，默认位0.75
		* threshold-扩容阀值，达到阀值后再添加一个元素就进行扩容，扩容的方式是新建一个长度为原来两倍的数组，将旧元素全部挪到新数组中
	* 数组中的元素是单向列表
		* JDK1.8进行了优化，改为链表+红黑树，节点数比较少时，仍然是链表，大于8(第9个)就转为红黑树。红黑树节点小于7(第6个)，转为链表
		* ![HashMap结构1.8](https://s2.ax1x.com/2019/06/30/Z3EI3t.png)
		* 链表/红黑树总存储的元素是Entry实例
		* 每个Entry包含key(键)，value(值)，hash(用于确定再数组中的位置)，next(链表指向)
* HashMap与HashTable
	* hashMap可以把null作为key或者value，而hashTable不可以
	* hashMap线程不安全，因此效率高，而hashTable线程安全，但是效率较低
* CurentHashMap(1.5之后)
	* 将一个大的Map分成多个Segment(类似于HashTable)，由Segment实现线程安全。并发状态下，多个线程可以同时对多个Segment进行操作，因此可以确保效率的同时实现线程安全。

#### Collection与Collections的区别
* java.util.Collection是java集合类的顶级接口，定义对集合对象进行基本操作的通用方法。直接继承的接口由List和Set
* java.util.Collections是集合类的一个工具类，提供了一系列的静态方法，可以方便地对集合进行排序、搜索(找出最大最小元素)以及实现线程安全等操作。