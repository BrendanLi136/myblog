---
title: Spring-Web
date: 2021-01-24 14:21:16
tags: Spring
categories: Spring
---
##  Spring-Web

### 1 工程依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.example</groupId>
    <artifactId>spring-web</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>

    <dependencies>
        <!--Spring-web: Spring-web 必选-->
        <!--依赖性导入：spring-beans、spring-core-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-web</artifactId>
            <version>5.2.11.RELEASE</version>
        </dependency>

        <!--************* Java EE API *************-->
        <!-- Servlet API -->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>4.0.1</version>
        </dependency>

        <!-- JSP API-->
        <dependency>
            <groupId>javax.servlet.jsp</groupId>
            <artifactId>javax.servlet.jsp-api</artifactId>
            <version>2.3.3</version>
        </dependency>

        <!--JSTL-->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>
    </dependencies>

</project>
```

**版本对应关系：**

| Servlet Spec | JSP Spec | JSTL(jsp标准标签库) | EL Spec | WebSocket Spec | jdk                           | Apache Tomcat Version | JavaEE Version |
| :----------- | :------- | :------------------ | :------ | :------------- | :---------------------------- | :-------------------- | :------------- |
| 4.0          | 2.3      | 1.2                 | 3.0     | 1.1            | JDK8+                         | tomcat9.x+以上的配置  | Java EE 8      |
| 3.1          | 2.3      | 1.2                 | 3.0     | 1.1            | JDK7+                         | tomcat8.x+以上的配置  | Java EE 7      |
| 3.0          | 2.2      | 1.2                 | 2.2     | 1.1            | JDK6+(使用websocket需要JDK7+) | tomcat7.x             | Java EE 6      |
| 2.5          | 2.1      | 1.2                 | 2.1     | 无             | jdk5+                         | tomcat6               | Java EE 5      |

### 2 初始化Spring容器

Spring 既可以在JavaSE中使用，也可以在JavaEE环境中使用，在Web环境下可以借助Spring-Web提供的监听器初始化Spring容器。如：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <display-name>Archetype Created Web Application</display-name>

    <context-param>
        <param-name>contextConfigLocation</param-name>
        <!--    <param-value>/WEB-INF/applicaiton*.xml</param-value>-->
        <param-value>classpath:applicationContext.xml</param-value>
    </context-param>
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
</web-app>

```

### 3 在Servlet中获取容器

```java
package com.demo;

import com.sun.istack.internal.logging.Logger;
import org.springframework.web.context.WebApplicationContext;
import org.springframework.web.context.support.WebApplicationContextUtils;

import javax.servlet.ServletConfig;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

/**
 * Created by Brendan Li on 2021/1/24 11:25
 * Spring-web 的 servlet
 * @author BaoHua
 */
@WebServlet("/userServlet")
public class UserServlet extends HttpServlet {
    private static final Logger logger= Logger.getLogger(UserServlet.class);
   private WebApplicationContext applicationContext;

    /* 初始化方法 */
    @Override
    public void init(ServletConfig config) throws ServletException {
       applicationContext = WebApplicationContextUtils.getWebApplicationContext(config.getServletContext());
       applicationContext.getBean("userService");
        logger.info("----------- do init ------------");

    }

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        logger.info("----------- do Get ------------");
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        logger.info("----------- do doPost ------------");
    }

    @Override
    public void destroy() {
        logger.info("----------- destroy ------------");
    }

}
```

