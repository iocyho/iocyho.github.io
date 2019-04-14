---
title：java打包
---

### 普通javaSE工程打可执行jar包
#### 方法一：eclipse导出为Runnable Jar即可

* 需要将main方法至少运行一次，否则会找不到主方法

#### 方法二：maven打包即可

### springboot工程打包
#### 1. 打jar包
* 导入依赖
* 配置文件(指定页面前后缀等)
* 配置插件如果需要访问jsp页面，需要将打包插件版本指定为1.4.2.RELEASE（其他版本有问题，会造成jsp无法访问）
* cmd使用java -jar命令即可启动打好的jar包

#### 2.打war包
##### 使用maven打包
* 启动类继承 SpringBootServletInitializer类，重写configure方法
	```
	@Override
	protected SpringApplicationBulider configure(SpringApplicactionBuilder appliction){
		return appliction.sources(当前类.class);
	}
	```
* pom文件中的打包类型修改为war
	```
	<package>war</package>
	```
* pom中配置打包插件(一般创建工程的时候会自动加入)
	```
	<plugin>
		<groudId>org.springframework.boot</groudId>
		<artifactId>springboot-maven-plugin</artifactId>
	</plugin>
	```
* 使用maven package或者 maven install，把target目录下生成的war包部署到tomcat中运行即可
* 注意：工程中application.properties/yml 关于端口和项目路径的配置只对springboot内置的tomcat有效(即只有打jar包的时候才会生效),打war包会忽略这个配置，以实际部署的tomcat为准。


### 其他：springboot访问jsp
* 创建webapp文件夹，放入jsp文件
* pom文件中配置<resources/>指定jsp文件位置
	```
	<resources>
		<resource>
			<directory>src/mian/java</directory>
			<includes>
				<include>**/*.xml</include>
			</includes>
		</resource>
		<resource>
			<directory>src/mian/resources</directory>
			<includes>
				<include>**/*.*</include>
			</includes>
		</resource>
		<!-- springboot程序中要访问jsp需要进行这个配置，并将jsp页面放进targetpath指定的文件夹中 -->
		<resource>
			<directory>src/mian/webapp</directory>
			<targetpath>META-INF/resources</targetpath>
			<includes>
				<include>**/*.*</include>
			</includes>
		</resource>
	</resources>
	```

### 参考
##### [springBoot打包](https://www.bilibili.com/video/av49037328/?p=73)