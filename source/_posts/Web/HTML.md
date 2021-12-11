---
title: HTML
date: 2021-01-23 15:11:27
tags: Web
categories: HTML
---
# 1.网站信息页面
## HTML概述：
HTML：Hyper Text Markup Language 超文本标记语言

超文本：比普通文本功能更强大，可以添加各种样式

标记语言：通过一组标签，对内容进行描述，<关键字>，由浏览器解释执行
## HTML的主要作用：设计网页的基础  HTML5
## HTML语法规范


	<!--
		1. 上面是一个文档声明 
		2. 根标签 html
		3. html文件主要包含两部分. 头部分和体部分
			头部分 : 主要是用来放置一些页面信息
			体部分 : 主要来放置我们的HTML页面内容
		4. 通过标签来对内容进行描述,标签通常都是由开始标签和结束标签组成  
		5. 标签不区分大小写, 官方建议使用小写
	-->

### 扩展内容

    b:加粗
    i:斜体
    strong:加粗，带语义标签
    em:斜体，带语义

# 2.网站图片信息

## 2.1技术分析
img标签：

常用的属性：
    
    width:宽度
    height:高度
    src:指定文件路径
    alt:图片加载失败显示的提示内容
    
## 2.2路径问题
    - 相对路径
        ./		代表的是当前路径
		../ 	代表的上一级路径
		../../	上上一级路径
		
# 3.网站友情链接页面
## 3.1 需求分析
- 百度
- 搜狐
- 新浪微博

## 3.2 技术分析

列表标签：
       
    无序列表：ul
        type:disc  suqare   circle
    有序列表：ol
        type:1,a,A,i,I

## 3.3扩展内容

    点击链接，跳转
  
    a 超链接标签
    
        常用属性：
        href：指定要跳出去的地址
                如果是网络地址，需要加上http协议
                如果是本网站的html文件，可以直接写文件路径
        target：打开方式
                _blank      新窗口打开
                _self       本窗口打开
# 4.网站首页

## 表格标签table

    table标签：
        bgcolor：背景色     
        width：    宽度
        height：   高度 
        border：   边框
    tr 标签
    td 标签
    
##  合并单元格
    colspan：跨列           rowspan：跨行
## 表格的嵌套
    在td中可以嵌套一个表格

# 5.网站注册案例
## 表单标签

    form 表单标签
        action:直接提交的地址
        method:
            get方式：默认提交方式，会将参数拼接在链接后面，限制大小，4k
            post方式： 会将参数封装在请求体中，没有这样的限制
    input:
        type:指定输入项的类型
            text:文本
            password:密码框
            button:普通按钮
            submit:提交按钮
            reset:重置按钮
            radio:单选按钮
            checkbox:复选按钮
            file：上传文件
            hidden：隐藏域      主要用于存放页面上的id信息
            
            date：日期类型
            tel：手机号
            number：只允许数字
        placeholder:指定默认的提示信息
        name:在表单提交的时候，作为参数的名称
        id：给输入项起名字，便于后期查找和操作
    textarea:文本域，可以输入一段文字
        cols：指定宽度
        rows：指定高度
    select：下拉列表
        option：选择项

# 6.网站后台页面

## 框架标签
frameset
    
>     注意：使用了frameset必须将body去掉
在框架中点击跳转

frame
    
    常用属性：
        src:引入html文件路劲
        name：指定框架的名称
        
# HBuilder常用的快捷键

```html
Ctrl + D 					删除光标当前所在的行
Ctrl + Shift + R 			复制当前行到下一行
Ctrl + Enter 				将光标移动到下一行
Ctrl + Shift + Enter 		将光标定位在上一行
Ctrl + Shift + /            注释当前行
Ctrl + R  					运行当前网页/刷新当前网页
```
