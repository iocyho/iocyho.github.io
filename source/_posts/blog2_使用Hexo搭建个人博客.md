---
title: 通过Hexo搭建个人博客
---


![Hexo](https://raw.githubusercontent.com/hexojs/awesome-hexo/master/hexo-logo.png)

### 第一步：安装Node.js
* ##### 下载Node.js并安装
* ###### Node.js是一个javascript的运行环境，实际上是对实质是对chrome V8引擎进行了封装，并针对特殊用例提供替代API进行优化，使得V8可以在非浏览器环境很好地运行，变成一个运行在服务端框架。有了nodejs提供的环境就可以使用javascript进行服务端开发，实现php及java的servlet等开发的功能。
1. 官网下载即可: https://nodejs.org/zh-cn/download/
2. 下载完成后按引导安装
3. 安装完毕后检查环境变量是否自动配置好，没有配置手动添加
4. 命令行输入 node -v ,显示当前node的版本信息则说明安装成功

* ##### 安装npm与cnpm并进行相关配置
* ###### npm全称Node Package Manager，是node包管理和分发的工具，使用npm可以对应用的依赖进行管理。npm的功能和服务端项目构建工具maven差不多，通过npm 可以很方便地下载js库，打包js文件。
1. node.js已经默认集成了npm
2. 设置包路径，即npm从远程下载js包所存放的路径
&ensp; 在命令行输入"npm config ls"，查看当前包路径。
&ensp; npm的默认的管理包路径为C:/用户/[用户名]/AppData/Roming/npm/node_meodules。
&ensp; 为了方便对依赖包管理，可以将将包路径设置到非系统盘。
&ensp; 通过命令npm config set prefix "自定义路径名" 和 npm config set cache "自定义路径名" 修改包路径
3. 安装cnpm
&ensp; npm默认下载的是国外镜像，对网络环境要求较高，可以使用cnpm来下载国内镜像
&ensp; 命令行输入"npm install -g cnpm --registry=https://registry.npm.taobao.org "命令安装cnpm
&ensp; 安装完成后通过"cnpm -v"命令查看cnpm版本，校验是否完成安装
&ensp; 通过"nrm ls"命令查看镜像注册地址，如果cnpm安装正常镜像地址会指向taobao
&ensp; 可以使用nrm use "镜像名称" 切换镜像
&ensp; 如果没有nrm,通过cnpm install -g nrm命令安装nrm


### 第二步：安装Hexo客户端
* ##### 使用npm或cnpm安装Hexo客户端
1. 命令行输入 npm install -g hexo-cli 安装Hexo客户端
2. 命令行输入 hexo -v 查看hexo版本，校验是否安装成功


### 第三步：创建一个文件夹用于本地存储博客文件
* ##### 创建本地博客管理文件夹
&ensp;位置不限，方便管理即可
* ##### Hexo初始化
&ensp;进入创建好的文件夹，打开命令行，输入hexo init命令进行初始化


### 第四步：博客文章写作
* ##### Hexo初始化完成后，本地博客管理文件夹中会生成一些文件和文件夹
* ##### \source\_posts是存放博客原始文件的位置
* ##### 博客原始文件是md文件，使用MarkDown编辑工具可以进行编辑
* ##### MarkDown编辑工具不限，如[MarkDownPad2](http://markdownpad.com/)、[Typora](https://www.typora.io/)等，选择顺手的即可


### 第五步：博客服务本地预览
* ##### 写好的md格式的博客放进\source\_posts路径下
* ##### 回退到本地博客管理文件夹(source的上级目录)，打开命令行
* ##### 输入hexo clean，清除旧的博客页面
* ##### 输入hexo generate，生成博客页面
&ensp;完成后会生成publish文件夹，里面存放了HTML、CSS、js文件，即生成的博客页面
* ##### 输入hexo server，在本地开启博客服务
&ensp;成功后控制台会出现 http://localhost:4000 ，可以访问该链接预览博客最终效果
&ensp;预览完毕后按ctrl+c关闭本地服务，准备正式部署到外网


### 第六步：将博客部署到外网服务器
* ##### 安装git
1. 下载 https://git-scm.com/download
2. 安装，根据引导安装即可

![GitHub](https://timgsa.baidu.com/timg?image&quality=80&size=b10000_10000&sec=1553704687&di=bbc11f19add529fd91d0d79cd696e07a&src=http://bpic.588ku.com/element_origin_min_pic/00/86/44/4056eb5f135855d.jpg)


* ##### 将博客部署到GitHub
1. 创建GitHub账号
2. 创建仓库(Reposiory)
&ensp;仓库名称必须是"GitHub用户名".github.io
3. 通过git工具将博客源码推送到GitHub仓库的source分支(名字不限，不叫source也可以)
4. 将生成的博客页面通过通过Hexo客户端推送到GitHub仓库的master分支
&ensp;不叫master也行，但是必须是默认分支，否则无法通过域名浏览博客页面
* ##### 博客页面推送成功后，浏览器地址栏输入"GitHub用户名".github.io，看到自己的博客页面，搭建成功
* ##### 以后每次更新博客，重复第四到第六步操作即可


