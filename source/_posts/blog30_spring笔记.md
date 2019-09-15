---
title: spring笔记
---

### 循环依赖
* spring容器如何解决循环依赖
	* 通过单例缓存池解决循环依赖(三级缓存池)
		* org.springframework.context.support.AbstarctApplicationContext # getBean()
		* org.springframework.bean.factory.support.AbstarctBeanFactory # doGetBean()
			1. transformeBeanName 解析别名
			2. getSingleton 尝试去缓存池中获取对象(第一次创建bean时，缓存池中没有该对象)
			3. 若出现循环依赖，判断bean是否单例，不是单例则抛出异常
			4. 当前容器是否有父工厂，有父工厂则尝试由父工厂加载
			5. 合并bean定义
			6. 判断bean定义是否为抽象的
			7. 进行dependsOn依赖检测
			8. 调用getSingleton方法
				* beforeSingletonCreation 标志当前的bean正在被创建
			9. CeateBean 创建bean
				* resolveBeforeInstantation 在aop过程中寻找切面(Aspectj)
				* doCreateBean 真正调用创建bean的方法
		* org.springframework.bean.factory.support.AbstarctAutowireCapableBeanFactory # doCreateBean()
			1. createBeanInstance() 调用构造器，创建 “早期对象”（属性未赋值）
			2. 提前暴露早期对象，存入缓存池
	* 假如InsatcneA与InstanceB互相依赖
		* 创建A时先将A的早期对象存入缓存池
		* 发现A依赖B,则走创建B的流程，将B的早期对象存入缓存池
		* 发现B又依赖A,则再次走创建A的流程，直接从缓存池中获取A对象，完成B的创建
		* 进而完成A的创建
* 什么样的循环依赖无法解决
	* 在构造器中依赖
	* 这种情况下创建实例A时，还未来得及将A的早期对象放入单例缓存池就调用了创建实例B的方法
* 为什么多例模式无法解决循环依赖
	* 因为多例模式下，对象不会被存入单例缓存池，因此无法从单例缓存池获取对象


### AOP
* 为什么通过IOC容器获取到的组件是一个动态代理对象？
* 什么时候生成的代理对象？
* 代理对象存放在哪？
