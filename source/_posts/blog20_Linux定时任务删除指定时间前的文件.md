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
* 说明
	* 用户通过crontabl -e命令建立的Crontab文件存于/var/spool/cron中，其文件名与用户名一致
	* 它的格式共分为六段，前五段为时间设定段，第六段为所要执行的命令段
	* 格式如下：f1 f2 f3 f4 f5 command/program
	* f1 是表示分钟，f2 表示小时，f3 表示一个月份中的第几日，f4 表示月份，f5 表示一个星期中的第几天。program 表示要执行的命令或程序。
	* 当 f1 为 * 时表示每分钟都要执行 program，f2 为 * 时表示每小时都要执行程序，以此类推
	* 当 f1 为 a-b 时表示从第 a 分钟到第 b 分钟这段时间内要执行，f2 为 a-b 时表示从第 a 到第 b 小时都要执行，以此类推
	* 当 f1 为 */n 时表示每 n 分钟个时间间隔执行一次，f2 为 */n 表示每 n 小时个时间间隔执行一次，以此类推
	* 当 f1 为 a, b, c,… 时表示第 a, b, c,… 分钟要执行，f2 为 a, b, c,… 时表示第 a, b, c…个小时要执行，以此类推
