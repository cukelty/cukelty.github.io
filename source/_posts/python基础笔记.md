---
title: python基础笔记
date: 2017-11-02 16:39:56
tags:
---
摘要：

<!--  more -->
正文：
#Python3 
Python3 不支持兼容老版本。
>python -v   查询python的版本号<br>
>python3 hello.py  执行该脚本

##基础语法：
###编码
源码默认为UTF-8,所有字符串都是unicode.
自定义编码模式
	
	# -*- coding: cp-1252 -*-
	
###标识符
>* 第一个字符必须是字母表中字母或下划线'_'
>* 标识符的其他的部分有字母、数字和下划线组成。
>* 标识符对大小写敏感

###python保留字
保留字即关键字，我们不能把它们用作任何标识符名称

Python 的标准库提供了一个 keyword 模块，可以输出当前版本的所有关键字：

	>>> import keyword
	>>> keyword.kwlist
	['False', 'None', 'True', 'and', 'as', 'assert', 'break', 'class', 'continue', 'def', 'del', 'elif', 'else', 'except', 'finally', 'for', 'from', 'global', 'if', 'import', 'in', 'is', 'lambda', 'nonlocal', 'not', 'or', 'pass', 'raise', 'return', 'try', 'while', 'with', 'yield']
	
###注释
单行注释以# 开头<br>
多行注释可以用多个# 号
###行与缩进
python使用缩进来表示代码块，不需要使用大括号。
缩进可变，同一代码块必须包含相同缩进空格数，缩进不一致会导致运行错误
###多行语句
python通常是一行写完一条语句，但语句很长，可以采用反斜杠来实现多行语句，
	
	total = item_one + \
           item_two + \
           item_three
           
 在 [], {}, 或 () 中的多行语句，不需要使用反斜杠(\)，例如：
 
 	total = ['item_one', 'item_two', 'item_three',
        'item_four', 'item_five']
       
###数据类型
python中数有四种类型：整数、长整数、浮点数和复数。
	
	* 整数  
	* 长整数  
	* 浮点数  1.23
	* 复数   1+2j

###字符串

* python中单引号和双引号使用完全相同。
* 使用三引号('''或""")可以指定一个多行字符串。
* 转义符 '\'
* 自然字符串， 通过在字符串前加r或R。 如 r"this is a line with \n" 则\n会显示，并不是换行。
* python允许处理unicode字符串，加前缀u或U， 如 u"this is an unicode string"。
* 字符串是不可变的。
* 按字面意义级联字符串，如"this " "is " "string"会被自动转换为this is string。

>
	word = ‘字符串’
	sentence = "这是一个句子。"
	paragraph = """这是一个段落，
	
### 空行

函数之间或类的方法之间用空行分割，表示一段新的代码的开始，类和函数入口之间也用一行空行分割