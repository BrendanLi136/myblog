---
title: CSS
date: 2021-01-23 15:18:31
tags: Web
categories: CSS
---
# 使用css完成首页的优化

表格布局的缺陷：
    
1.嵌套层级太多,一旦出现嵌套顺序错乱,整个页面达不到预期效果

2.采用表格布局,页面不够灵活,动其中某一块,整个表格布局的结构全都要变

## 技术分析
HTML的块标签：
    
    div标签：默认占一行,自动换行
    span标签：内容显示在同一行

CSS的概述：
    Caccading Style Sheets : 层叠样式表

主要作用：
    
    用来美化HTML页面的
    
    HTML决定网页的骨架  css美化

CSS的简单语法:

在一个style标签中,去编写CSS内容,最好将style标签写在这个head标签中

<html>

    <style>
      选择器{
        属性名称:属性的值;
        属性名称2: 属性的值2;
      }
    </style>
    
</html>

CSS选择器: 帮助我们找到我们要修饰的标签或者元素
    
    类加载器        class   以.开头
    ID加载器        id      以 # 开头     id必须唯一
    属性加载器      以属性名开头 
    
CSS的引入方式:

外部样式: 通过link标签引入一个外部的css文件

内部样式: 直接在style标签内编写CSS代码

行内样式: 直接在标签中添加一个style属性, 编写CSS样式

CSS浮动：

    float : right,left  不再占有正常文档流中的空间 , 流式布局
    clear : both right left

# 扩展内容
CSS的优先级
按照选择器搜索精准度来编写：   行内样式>ID选择器>类选择器>元素选择器

就近原则，哪个近选哪个

- CSS的其他选择器

    - 选择器分组：选择器1,选择器2{属性的名称,属性的值}
    - 属性选择器：
    ```
        a[title]
        a[title='aaa']
        a[title][href]
        a[title='aaa'][href]
    ```
    - 后代选择器：爷爷选择器 孙子选择器     找出所有子孙后代    
    - 子元素选择器：爷爷选择器 > 儿子选择器
    - 伪类选择器：通常用作a标签上   
    
# 使用DIV+CSS完成注册页面的优化
## 技术分析

CSS的盒子模型: 万物皆盒子

内边距:  

padding-top:

padding-right:

padding-bottom:

padding-left:

```html
padding:10px;  上下左右都是10px
padding:10px 20px;  上下是10px 左右是20px
padding: 10px 20px 30px;  上 10px 右20px  下30px  左20px
padding: 10px 20px 30px 40px;  上右下左, 顺时针的方向
```
外边距:

margin-top:

margin-right:

margin-bottom:

margin-left: 

CSS绝对定位:

	position: absolute
	    top: 控制距离顶部的位置
	    left: 控制距离左边的位置

