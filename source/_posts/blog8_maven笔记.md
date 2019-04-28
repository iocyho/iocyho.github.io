---
title: maven笔记
---

### maven的作用
* 管理项目依赖
* 管理项目插件
* 项目打包部署

### maven生命周期
* Clean LifeCycle 在进行真正的构建之前进行一些清理工作
* Default LifeCycle 构建的核心部分，编译、测试、打包、部署等
	1 validate - 验证项目是否正确，所有必要的信息均可用
	2 **compile** - 编译项目的源代码
	3 test - 使用适合的单元测试框架测试编译的源代码(这些测试不应该要求代码被打包或部署)
	4 **package** - 对编译后的代码以指定的格式(如war或jar)进行打包 
	5 verify - 对集成测试的结果执行检查，确保满足质量标准
	6 **install** - 将软件包安装到本地仓库中，作为本地其他项目的依赖
	7 deploy - 发布，在构建环境中完成，将最终的包复制到远程仓库与其他开发人员或项目共享
* Site LifeCycle 生成项目报告，站点、发布站点
 
### maven命令
	* 安装本地jar包到本地maven仓库
	```
	mvn install:install-file-Dfile=jar包路径 -DgroupId=组织机构名称 -DartifactId=应用名称 -Dversion=版本号 -Dpackaging=包格式
	```
