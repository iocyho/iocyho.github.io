---
title : Linux定时任务删除指定时间前的文件
---

#### 新建一个文件
```
touch /xxx/xxx/clearfiles
```

#### 赋予文件权限
```
chomd 755 /xxx/xxx/clearfiles
```

#### 编辑文件,在新建的clearfiles中按需求添加删除命令
```
#!/bin/sh
find /mnt/www/Application/Runtime/Logs  -mtimes +10 -name *.* -exec rm -rf {} \;
find /tmp  -type f -mmin 120 -name *.log -exec rm -rf {} \;
```
* 说明：
	*  find 后面紧跟的是要查找的目录，. 表示当前目录
 	* -type f：	指定查找对象为文件
	* -name *.log：	指定查找对象名称以.log结尾
 	* -mtime +10:	 查找10天以前的老文件
 	* -mmin +120:	 查找120分钟（两小时）以前的老文件
 	* -exec rm -rf {} /;  :对查找到的文件执行删除命令
* 注意：
	* -exec rm -rf {} /; 中{}之间不能有空格
	* {}与/之间必须带空格
	* /是转译符，这里与;配对使用
	* 新建的clearfiles这里不能是.sh后缀结尾，否则执行会报错


#### 将clearfiles加入定时任务
* 执行命令，打开crontab配置文件
```
crontab -e
```
* 在配置文件中添加配置，保存
```
# 每分钟执行一次
*/1 * * * * /usr/local/bin/clear_log
```
* 完成配置后重启crond服务
```
service crond restart
```
