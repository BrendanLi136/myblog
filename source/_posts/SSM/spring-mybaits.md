---
title: spring-mybaits
date: 2021-01-24 14:42:37
tags: Spring
categories: Spring
---
## 与其他框架集成

### 7.1 集成 Mybatis

Spring与MyBatis的整合需要借助于MyBatis的一个子项目MyBatis-Spring。通过MyBatis-Spring可以 将 MyBatis 无缝地整合到 Spring 中，包括事务管理、Mapper生成、异常类型转化等，甚至是模板和 值支持工具类也有提供：SqlSessionTemplate、SqlSessionDaoSupport

> 官网文档： http://www.mybatis.org/spring/zh/ 
>
> 源码： https://github.com/mybatis/spring 
>
> ZIP发行包：2.0后只有源码包，没有zip文件，可在mvn仓库下载jar文件:  https://github.com/mybatis/spring/releases 
>
> 与Spring的版本配套关系： http://www.mybatis.org/spring/zh/index.html



#### 7.1.1 基于XML的配置

```xml
<!--配置数据源-->
    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <!--配置驱动，url，username，password-->
        <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/ssm?useUnicode=true&amp;characterEncoding=utf8&amp;serverTimezone=GMT%2B8"/>
        <property name="username" value="spring"/>
        <property name="password" value="spring"/>
    </bean>

    <!-- SqlSessionFactory 需要 DataSource -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="configLocation" value="classpath:com/demo/spring/user/mybatis-config.xml"/>
        <property name="dataSource" ref="dataSource"/>
        <property name="mapperLocations" value="classpath*:mapper/*.xml"/>
        <property name="databaseIdProvider" ref="databaseIdProvider"/>
    </bean>

    <!-- MapperScannerConfigurer-->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.demo" />
    </bean>

    <!-- 事务配置-->
    <bean id="transactionManager"
          class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource" />
    </bean>
    <!-- 使用annotation注解方式配置事务 -->
    <tx:annotation-driven transaction-manager="transactionManager" />

    <!--多数据库支持-->
    <bean id="databaseIdProvider"
          class="org.apache.ibatis.mapping.VendorDatabaseIdProvider">
        <property name="properties">
            <props>
                <prop key="SQL Server">sqlserver</prop>
                <prop key="DB2">db2</prop>
                <prop key="Oracle">oracle</prop>
                <prop key="MySQL">mysql</prop>
            </props>
        </property>
    </bean>
```

> - **SqlSessionFactory**：SqlSessionFactoryBean 实现了 Spring 的 FactoryBean 接口， Spring会调用他的getObject方法创建，方法中创建了SqlSessionFactory。 SqlSessionFactoryBean 唯一需要的属性是dataSource，其他属性解析： 
> - configLocation：这个配置文件不需要是一个完整的 MyBatis 配置。确切地说,任意环境,数据源 和 MyBatis 的事务管理器都会被忽略 
> - mapperLocations：Ant样式的path，从类路径下加载包和它的子包中所有的 MyBatis 映射文件。 
> - databaseIdProvider：多数据库支持的引用，可以在MyBatis 配置文件中配置，也可以在 Spring中配置，然后通过这个属性传入
>
> ```xml
> <!--多数据库支持-->
> <bean id="databaseIdProvider"
>       class="org.apache.ibatis.mapping.VendorDatabaseIdProvider">
>     <property name="properties">
>         <props>
>             <prop key="SQL Server">sqlserver</prop>
>             <prop key="DB2">db2</prop>
>             <prop key="Oracle">oracle</prop>
>             <prop key="MySQL">mysql</prop>
>         </props>
>     </property>
> </bean>
> ```
>
> - **MapperScannerConfigurer**：扫描类路径，生成Mapper接口的代理，相关属性：  
> - basePackage：多个ant分格的类路径，用逗号或者分号分割 
> - sqlSessionFactoryBeanName：指定sqlSessionFactory的name，可以不设置 
>
> ```xml
> <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
> ```
>
> 生成的映射器在Spring上下文中的ID为，首字母小写的接口名，如：UserDao接口对应bean的id 为userDao，但是如果是IUserDao，则bean的id为IUserDao
>
> - 其他注册映射器的方式:
>
>   - MapperFactoryBean 
>   - 使用 mybatis:scan/ 元素 
>   - 使用 @MapperScan 注解
>
>   ```xml
>   <bean id="userDao" class="org.mybatis.spring.mapper.MapperFactoryBean">
>   <property name="mapperInterface"
>   value="com.demo.spring.sample.step05.mybatis.dao.IUserDAO"/>
>   <property name="sqlSessionFactory" ref="sqlSessionFactory"/>
>   </bean>
>   ```
>
>   ```xml
>   <mybatis:scan base-package="org.mybatis.spring.sample.mapper"/>
>   ```
>
>   ```xml
>   @MapperScan("org.mybatis.spring.sample.mapper")
>   ```

#### 7.1.2 基于Java的配置

```java
package config;

import org.mybatis.spring.SqlSessionFactoryBean;
import org.mybatis.spring.mapper.MapperScannerConfigurer;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.io.support.PathMatchingResourcePatternResolver;
import org.springframework.jdbc.datasource.DataSourceTransactionManager;
import org.springframework.jdbc.datasource.DriverManagerDataSource;
import org.springframework.transaction.PlatformTransactionManager;

import javax.sql.DataSource;
import java.io.IOException;

/**
 * Created by Brendan Li on 2021/1/24 14:10
 *
 * @author BaoHua
 */
@Configuration
public class MybatisConfig {
    @Value("${jdbc.driverClassName}")
    private String driverClassName;
    @Value("${jdbc.ur}")
    private String url;
    @Value("${jdbc.username}")
    private String username;
    @Value("${jdbc.password}")
    private String password;

    @Bean
    public DataSource dataSource() {
        DriverManagerDataSource dataSource = new DriverManagerDataSource();
        dataSource.setDriverClassName(driverClassName);
        dataSource.setUrl(url);
        dataSource.setUsername(username);
        dataSource.setPassword(password);
        return dataSource;
    }

    @Bean
    public SqlSessionFactoryBean sqlSessionFactory() throws IOException {
        SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
        // 设置数据源
        sqlSessionFactoryBean.setDataSource(dataSource());
        // 设置mybatis的映射文件
        PathMatchingResourcePatternResolver resolver = new PathMatchingResourcePatternResolver();
        sqlSessionFactoryBean.setMapperLocations(resolver.getResources("classpath*:com/demo/spring/sample/step09/mybatis/**/*Mapper.xml"));
        return sqlSessionFactoryBean;
    }

    @Bean
    public MapperScannerConfigurer mapperScannerConfigurer() {
        MapperScannerConfigurer mapperScannerConfigurer = new MapperScannerConfigurer();
        mapperScannerConfigurer.setBasePackage("com.demo.spring.sample.step09.mybatis.**.dao");
        return mapperScannerConfigurer;
    }

    /**
     * 基于java的配置中，事务管理器的默认名称为txManager
     *
     * @return PlatformTransactionManager
     */
    @Bean
    public PlatformTransactionManager txManager() {
        return new DataSourceTransactionManager(dataSource());
    }
}

```

