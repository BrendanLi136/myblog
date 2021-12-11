---
title: main函数详解
date: 2021-01-22 22:41:15
tags: Java
categories: Java基础
---



- main方法

```java
public static void main(String[] args) {

}
```



**public**：公共的。 权限是最大，在任何情况下都可以访问。

原因：为了保证让jvm在任何情况下都可以访问到main方法。

**static** ：静态。 静态可以jvm调用main函数的时候更加方便。不需要通过对象调用。

**void** ： 无返回值。 因为返回的数据是给jvm，而jvm使用这个数据是没有意义的。所以就不要了。

**main**：函数名。 注意：main并不是关键字，只不过是jvm能识别的一个特殊的函数名而已。

**arguments**： 担心某些程序在启动需要参数。