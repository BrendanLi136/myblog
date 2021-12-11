---
title: Spring-Test与Junit
date: 2021-01-24 14:28:56
tags: Spring
categories: Spring
---
## Spring Test 与 Junit

Spring Test整合使用Junit需要导入以下包：

> spring-test：spring测试模块
>
> junit-4.12.jar：Junit测试包
>
> hamcrest-core-1.3.jar：Junit依赖该包

### 1 独立使用junit

```java
package com.demo.spring;

import com.demo.spring.dao.impl.UserDao;
import org.junit.BeforeClass;
import org.junit.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

/**
 * Created by Brendan Li on 2021/1/24 10:30
 *  直接使用Junit
 * @author BaoHua
 */
public class SpringTest {
    @Autowired
    private static UserDao userDao;

    /*初始化测试环境*/
    @BeforeClass
    public static void init(){
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
        // userDao
         userDao = applicationContext.getBean("userDao", UserDao.class);
    }

    /*测试用例*/
    @Test
    public void testAddUser(){
        userDao.addUser("test",13);
    }
}

```

### 2 Spring test 集成 junit

```java
package com.demo.spring;

import com.demo.spring.dao.impl.UserDao;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

/**
 * Created by Brendan Li on 2021/1/24 10:42
 * Spring-test 整合 junit
 * @author BaoHua
 */
//RunWith(SpringJUnit4ClassRunner.class) or @RunWith(SpringRunner.class).
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext.xml")
// Java 配置方式测试上下文： @ContextConfiguration(classes:MvcConfig.class)
public class SpringTestJunit {

    @Autowired
    private UserDao userDao;

    @Test
    public void test(){
        userDao.addUser("testJunit",19);
    }
}
```

> Spring整合Junit使用方法
>
> 1. 继续使用 Junit4 测试框架，包括其 @Test 注释标签和相关的类和方法的定义，这些都不用变 
> 2. 通过 @RunWith(SpringJUnit4ClassRunner.class) 来启动 Spring 对测试类的支持 
> 3. 通过 @ContextConfiguration 注释标签来指定 Spring 配置文件或者配置类的位置 
> 4. 通过 @Transactional 来启用自动的事务管理,测试数据自动回滚 
> 5. 可以使用 @Autowired 自动织入 Spring 的 bean 用来测试
>
> 不再需要： 
>
> 1. 手工加载 Spring 的配置文件
> 2. 手工清理数据库的每次变更 
> 3. 手工获取 application context 然后获取 bean 实例