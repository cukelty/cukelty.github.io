---
title: Linux 相关基础
date: 2017-11-09 11:45:27
tags:
---
摘要: Linux相关的一些操作方法以及命令学习笔记
<!--more-->
正文：
#  Linux

## 命令行

### shell 
	linux   
	Bourne shell(sh)  C shell(csh)  Korn shell(ksh) 
	korn shell 集合了C shell和Bource shell 的优点,并兼容 pdksh，支持任务控制，可挂起、后台执行、唤醒或者终止程序。
	
	shell 可以通过控制台实现使用。用户登录linux 以后，由/etc/passwd 来决定使用哪个shell
	
###   常用命令
	用户使用系统，需要登录实现系统识别用户
	启动和登录系统， SuperUser的用户名为root， 密码安装时设定
	
重启系统 reboot

关闭系统

	shutdown [选项] [时间] [警告信息]

	-k 并不关机只是发警告给所有用户
	-r 关机并重启
	-h 关机不重启
	-f 快速关机重启跳过fsck
	-n 快速关机不经过 init程序
	-c 取消运行的 shutdown
	该命令只能通过SU 权限使用
	
文件与目录操作

	ls命令  列出文件dir
	ls -a  显示所有文件包括隐藏文件
	ls -l 	显示文件更详细的资料
	ls -F  
	
	cd命令
	进入目录
	
	mkdir [新文件名]   新建目录
	rmdir [已有文件名]  删除目录
	
	cp 拷贝命令
	cp -r  连同源文件的子目录一同拷贝
	
	rm 删除
	rm -i [文件名]  删除文件会提示
	rm -r [目录名]   
	rm -f [文件名]  删除文件名不提示（强制删除）
	
	mv 移动目录或者文件
	mv  [源文件目录] [目标文件目录] 移动
	mv  [原文件名] [文件名]  改名
	
	cat 显示一般的ASCII文本文件
	cat text  显示text文件内容
	cat file1 file2 依次显示file1和file2
	cat file1 file2>file3  将File1和file2结合起来再重定向到file3
	
	pwd 显示用户当前工作路径 输入pwd 即可
	

用户和用户组命令
	
	useradd newuser 新建newuser，该用户Home目录为/home/newuser
	
	useradd 用户名  -g 组名 -G 组名 Home 目录名 -p 密码
	其中：
	 -g 指定该用户的首要组
	 -G 指定该用户的次要组
	 -d 指定该用户的Home目录
	 -p 指定该用户的密码
	 Sample：useradd oracle -g oinstall -G dba -d /home/oracle -p ora123
	 
	 userdel username  删除username 账户
	 groupadd  newgroupname 创建组名
	 groupdel groupname  删除用户组
	 passwd username  修改用户密码
	 
	 su 让普通用户变超级用户
	 su - 用户名
	 
	 chmod  用于改变文件或者目录的访问权限
	 chmod [who] [+|-|=] [mode] 文件名
	 命令中各项的含义：
	 who 为一个组合：
	 	u 表示用户
	 	g 表示同组用户
	 	o 表示others 用户
	 	a 表示 all 用户（系统默认值）
	 操作符：
	 	+ 表示添加权限
	 	- 表示取消权限
	 	= 赋予给定权限，并取消其他所有权限
	 mode 表示权限
	 	r 表示可读
	 	w 表示可写
	 	x 表示可执行
	 sample：
	 1. 文件script 的权限设为可执行
	 chmod =rx text  执行成功后
	 chmod u=rex,g=r,o= text
	 	
	 chown 用于更改某文件或者目录的属主和屬组
	 chown[用户：组] 文件
	 sample：chown oracle：dba text
	 该命令将text文件的属主和分改为oracle和dba
	 
进程与任务管理命令 

	ps  查看进程的命令
	ps [选项]
	常用选项：
	-e 显示所有进程
	-f 全格式
	-l 长格式
	
	top 基本与ps相同 显示系统当前的进程和其他状态
	但是top 是一个动态显示过程。
	常用选项：
	<空格> 立即刷新显示
	h 或者？ 显示帮助画面给出简单命令总结说明
	m  切换内存信息
	t 切换显示进程和cpu状态
	c 切换显示命令名称和完整命令行
	M 根据驻留内存大小排序
	P 根据CPU百分比排序
	q 退出
	
	kill结束进程  通过向进程发送指定信号结束进程
	kill [-s 信号] 进程号   
	sample：kill -9 1234 命令 将PID为1234的进程终止
	kill -l  信号列表
	
	corn 实现定时任务的完成，如每天一次的任务（进程）corn命令启动时由一个shell脚本自动启动，启动后，corn命令会搜索 /var/spool/cron 目录，寻找、etc/passwd文件中的用户名命名的文件crontab，载入内存。
	crontab -l 命令查看已经存在的cron任务
	
	
磁盘以及文件系统管理命令
	
	df命令可以显示目前磁盘剩余的磁盘空间，常用参数为-k  
	df -k 显示各分区的磁盘空间使用情况
	
	mount 和umount
	mount命令有很多参数，大多数都不会在日常工作中用到
	mount [选项] 设备 目录
	-a 把/etc/fstab 文件中列出的文件系统都挂装上
	
	umount 用于卸载文件系统
	umount [-f] directory 
	
软件安装命令

	tar 命令 用于把多个文件合并于一个档案文件中，并提供分解的合并后的文件功能
	tar [选项] 文件名
	-c 创建一个新的档案文件
	-t 查看档案文件的内容
	-x 分解档案文件的内容
	-f 指定档案文件名称
	-v 显示过程信息
	-z 采用压缩方式
	
	
	rpm 安装和查询
	rpm -i tomcat4.rpm 将安装Tomcat并进行相关系统配置
	rpm -U tomcat4.rpm 升级tomcat如那件并进行系统配置
	rpm -qa  查询已经安装的软件包
	rpm -qa|grep ‘tomcat’ 列出包名含有tomcat的软件包
	rpm -qi packagename  查找已经安装的软件包名称
	rpm -e packagename  查看删除的软件包名称
	
##文件查看/编辑
cat命令 链接多个文件内容并输出到标准输出流中

vi   visual interface的简称，可以执行输出、删除、查找、替换、块操作等众多文本操作。

	vi example.txt 进入文件编辑
	vi +5 example.txt 进入文件第5行开始显示。
	
	进入编辑后
	：q   直接退出
	：wq  保存后退出
	：x   保存后退出
	：q！ 不保存强制退出
	
	在末行模式下，输入set number 可以实现显示行数
	
	光标移动：
	在命令模式下：
	h  光标左移动
	l  光标右移动
	j  光标上移动
	k  光标下移动
	0  光标移动到行尾
	$ 光标移到行尾
	H 光标移到屏幕上显示的第一行 并不一定是文件头
	L 光标移到屏幕上显示的最后一行 并不一定是文件尾
	M 光标移到屏幕的中间一行
	nG   光标移到第 n 行
	w 或 W 将光标右移至下一个单词的词首
	e 或 E 如果光标起始位置处于单词内   即非单词尾处     则该命令将把光 标移到本单词词尾 如果光标起始位置处于单词尾 则该命令将把光标移动 到下一个单词的词尾
	b 或 B 如果光标处于所在单词内   即非单词首     则该命令将把光标移至 本单词词首 如果光标处于所在单词的词首 则该命令将把光标移到上一个 单词的词首
	Ctrl+G 状态命令 显示当前编辑文档的状态 包括正在编辑的文件名 是 否修改过 当前行号 文件的行数以及光标之前的行占整个文件的百分比
	此外 也可以通过以下末行模式下的命令完成光标在整个文件中的移动操作
	:n  光标移到文件的第 n 行
	:$  光标移到文件的最后一行
	 
	
	 
	 
	

	