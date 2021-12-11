---
title: Java 环境配置
date: 2021-01-22 00:29:01
tags: Java
categories: Java入门
---
# 环境变量设置

## Windows 配置

> 计算机---->右键属性--->高级环境设置--->环境变量

- 在==系统变量==中新建系统变量

1. 变量名 JAVA_HOME

2. 变量值 C:\Program Files\Java\jdk1.8.0_51 `变量值是JDK安装的目录`


- 在==系统变量==中找到path

path的组成结构是 xxx;xxx; 每个系统属性之间以;隔开

注意此处的符号都是英文符号

在path的最后面添加 **;%JAVA_HOME%\bin**

win10 **%JAVA_HOME%\bin**

- 在系统变量中新建系统变量

1. 变量名 CLASSPATH

2. 变量值 .;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar;

**环境变量验证**


	打开cmd  方法win+R  输入cmd   或者点击开始运行 输入cmd
	在命令行输入  echo %JAVA_HOME%
	再输入java -version
	也可以测试下javac 回车
	java 回车


## Linux 配置

--- 

待续。。。