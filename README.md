# 整合SSM

## 整合Spring

### 添加Spring的依赖

在pom.xml文件中添加如下依赖

```xml
<!-- ======BEGIN 单元测试 ====== -->
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
    <scope>test</scope>
</dependency>
<!-- ======END 单元测试 ====== -->


<!-- ======BEGIN Spring ====== -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>5.1.5.RELEASE</version>
</dependency>
<!-- ======END Spring ====== -->
```

### 添加Spring配置文件applicationContext.xml

在resources文件夹下添加applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">


</beans>
```

暂时不配置bean



### 修改web.xml，配置上下文参数，加载Spring配置文件

在web.xml文件下添加如下配置

```xml
<!-- 配置上下文参数，加载Spring配置文件 -->
<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:applicationContext.xml</param-value>
</context-param>

<welcome-file-list>
    <welcome-file>index.jsp</welcome-file>
</welcome-file-list>
```



## 整合Spring MVC

### 添加SpringMVC的依赖

在pom.xml文件中添加如下依赖

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.1.5.RELEASE</version>
</dependency>
```

### 配置转发Servlet

在web.xml文件下添加如下配置

```xml
<listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>

<servlet>
    <servlet-name>dispatcher</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
</servlet>

<servlet-mapping>
    <servlet-name>dispatcher</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>
```

在WEB-INF文件夹下创建dispatcher-servlet.xml，配置如下

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 配置Spring扫描组件的包路径 -->
    <context:component-scan base-package="top.alanlee.pam.*" />

    <!-- 启用注解配置 -->
    <mvc:annotation-driven />
    
    <!-- 配置视图解析器 -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/jsp/" />
        <property name="suffix" value=".jsp" />
    </bean>

</beans>
```



> 如果需要在需要返回json数据，则需要配置消息转换器，采用如下配置
>
> ```xml
> <?xml version="1.0" encoding="UTF-8"?>
> <beans xmlns="http://www.springframework.org/schema/beans"
>     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:mvc="http://www.springframework.org/schema/mvc"
>     xmlns:context="http://www.springframework.org/schema/context"
>     xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
> 
>  <!-- 配置Spring扫描组件的包路径 -->
>  <context:component-scan base-package="top.alanlee.pam.*" />
> 
>  <!-- 启用注解配置 -->
>  <!-- Start: 配置json消息转换器 & 参数解析-->
>  <bean id="objectMapper" class="com.fasterxml.jackson.databind.ObjectMapper">
>      <property name="dateFormat">
>          <bean class="java.text.SimpleDateFormat">
>              <constructor-arg index="0" type="java.lang.String" value="yyyy-MM-dd HH:mm:ss"/>
>          </bean>
>      </property>
>  </bean>
>  <mvc:annotation-driven>
>      <mvc:message-converters register-defaults="true">
>          <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
>              <property name="supportedMediaTypes">
>                  <list>
>                      <value>application/json; charset=UTF-8</value>
>                  </list>
>              </property>
>              <property name="prettyPrint" value="true"/>
>              <property name="objectMapper" ref="objectMapper"/>
>          </bean>
>      </mvc:message-converters>
>  </mvc:annotation-driven>
>  <!-- End: 配置json消息转换器 & 参数解析 -->
> 
>  <!-- 配置视图解析器 -->
>  <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
>      <property name="prefix" value="/WEB-INF/jsp/" />
>      <property name="suffix" value=".jsp" />
>  </bean>
> 
> </beans>
> ```
> 同时还需要在pom.xml中加上jackson的依赖
>
> ```xml
> <!-- ======BEGIN 处理json ====== -->
> <dependency>
>     <groupId>com.fasterxml.jackson.core</groupId>
>     <artifactId>jackson-core</artifactId>
>     <version>2.9.9</version>
> </dependency>
> <dependency>
>     <groupId>com.fasterxml.jackson.core</groupId>
>     <artifactId>jackson-annotations</artifactId>
>     <version>2.9.9</version>
> </dependency>
> <dependency>
>     <groupId>com.fasterxml.jackson.core</groupId>
>     <artifactId>jackson-databind</artifactId>
>     <version>2.9.9</version>
> </dependency>
> <!-- ======END 处理json ====== -->
> ```
>
> 

在WEB-INF文件夹下创建jsp的文件夹（用来存放jsp文件）



## 整合MyBatis

### 添加相关依赖

在pom.xml文件中添加如下依赖

```xml
<!-- ======BEGIN MyBatis ====== -->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.4.6</version>
</dependency>

<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis-spring</artifactId>
    <version>1.3.2</version>
</dependency>
<!-- ======END MyBatis ====== -->

<!-- ======BEGIN 数据库相关 ====== -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.47</version>
</dependency>

<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.21</version>
</dependency>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-tx</artifactId>
    <version>5.1.5.RELEASE</version>
</dependency>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.1.5.RELEASE</version>
</dependency>
<!-- ======END 数据库相关 ====== -->

<!-- ======BEGIN 日志 ====== -->
<!-- MyBatis 依赖log4j -->
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
<!-- ======END 日志 ====== -->
```

### 添加MyBatis配置文件mybatis-config.xml

在resources文件夹下创建mybatis-config.xml，配置如下

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <settings>
        <setting name="logImpl" value="LOG4J"/>
    </settings>
</configuration>
```

### 添加log4f配置文件log4j.properties

在resources文件夹下创建log4j.properties，内容如下

```properties
log4j.rootLogger=INFO,DEBUG,CONSOLE,file
log4j.logger.com.jingze=debug
log4j.logger.com.ibatis=info 
log4j.logger.com.ibatis.common.jdbc.SimpleDataSource=info 
log4j.logger.com.ibatis.common.jdbc.ScriptRunner=info 
log4j.logger.com.ibatis.sqlmap.engine.impl.SqlMapClientDelegate=info 
log4j.logger.java.sql.Connection=info 
log4j.logger.java.sql.Statement=info 
log4j.logger.java.sql.PreparedStatement=info 
log4j.logger.java.sql.ResultSet=info

log4j.appender.CONSOLE=org.apache.log4j.ConsoleAppender
log4j.appender.Threshold=error
log4j.appender.CONSOLE.Target=System.out
log4j.appender.CONSOLE.layout=org.apache.log4j.PatternLayout
log4j.appender.CONSOLE.layout.ConversionPattern= %-d{yyyy-MM-dd HH:mm:ss} [%5p]-[%.10t]-[%.20C] - %m%n
```

### 添加jdbc配置文件jdbc.properties

在resources文件夹下创建jdbc.properties，内容如下

```properties
jdbc.user=root
jdbc.password=123456
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/ssm_template?useUnicode=true&characterEncoding=utf-8
```

### 将MyBatis集成到Spring中

在applicationContext.xml中的beans节点下添加如下配置

```xml
<!-- 加载properties配置文件 -->
<context:property-placeholder location="classpath:jdbc.properties" />

<!-- 配置数据源 -->
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
    <property name="url" value="${jdbc.url}" />
    <property name="username" value="${jdbc.user}"/>
    <property name="password" value="${jdbc.password}"/>
</bean>

<!-- 配置SessionFactory -->
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
    <property name="dataSource" ref="dataSource" />
    <!-- 配置 xxxMapper.xml 文件的路径 -->
    <property name="mapperLocations" value="classpath*:/mapper/*Mapper.xml" />
    <!-- 配置 MyBatis配置文件 mybatis-config.xml 的路径-->
    <property name="configLocation" value="classpath:mybatis-config.xml" />
</bean>

<!-- Mapper扫描配置 -->
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
    <!-- 配置XxxMapper.java的路径 -->
    <property name="basePackage" value="top.alanlee.pam.mapper.**" />
    <!-- 配置sqlSessionFactoryBeanName -->
    <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory" />
</bean>
```

### 添加XxxMapper.java文件

在src源码目录中添加UserMapper.java

```java
package top.alanlee.pam.mapper;

import org.apache.ibatis.annotations.Mapper;
import org.springframework.stereotype.Repository;
import top.alanlee.pam.entity.User;

import java.util.List;

@Mapper
@Repository
public interface UserMapper {

    List<User> getAllUsers();
}

```

### 添加XxxMapper.xml文件

在resource目录中创建mapper文件夹，在mapper文件夹中添加UserMapper.xml，内容如下

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="top.alanlee.pam.mapper.UserMapper">
    <select id="getAllUsers"
            resultType="top.alanlee.pam.entity.User">
        SELECT *
        FROM t_user
    </select>
</mapper>
```



## 完整的配置文件

### pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>pam.alanlee.top</groupId>
  <artifactId>ssm-personal-account-management</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>war</packaging>

  <name>ssm-personal-account-management</name>


  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
  </properties>

  <dependencies>
    <!-- ======BEGIN 单元测试 ====== -->
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.12</version>
      <scope>test</scope>
    </dependency>
    <!-- ======END 单元测试 ====== -->


    <!-- ======BEGIN Spring ====== -->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>5.1.5.RELEASE</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-tx</artifactId>
      <version>5.1.5.RELEASE</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-jdbc</artifactId>
      <version>5.1.5.RELEASE</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>5.1.5.RELEASE</version>
    </dependency>
    <!-- ======END Spring ====== -->


    <!-- ======BEGIN MyBatis ====== -->
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.4.6</version>
    </dependency>

    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis-spring</artifactId>
      <version>1.3.2</version>
    </dependency>
    <!-- ======END MyBatis ====== -->


    <!-- ======BEGIN 日志 ====== -->
    <dependency>
      <groupId>log4j</groupId>
      <artifactId>log4j</artifactId>
      <version>1.2.17</version>
    </dependency>
    <!-- ======END 日志 ====== -->


    <!-- ======BEGIN 数据库相关 ====== -->
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>5.1.47</version>
    </dependency>

    <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>druid</artifactId>
      <version>1.1.21</version>
    </dependency>
    <!-- ======END 数据库相关 ====== -->

    <!-- ======BEGIN 处理json ====== -->
    <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>fastjson</artifactId>
      <version>1.2.62</version>
    </dependency>

    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-core</artifactId>
      <version>2.9.9</version>
    </dependency>
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-annotations</artifactId>
      <version>2.9.9</version>
    </dependency>
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>2.9.9</version>
    </dependency>
    <!-- ======END 处理json ====== -->

  </dependencies>

  <build>
    <finalName>ssm-personal-account-management</finalName>
    <pluginManagement><!-- lock down plugins versions to avoid using Maven defaults (may be moved to parent pom) -->
      <plugins>
        <plugin>
          <artifactId>maven-clean-plugin</artifactId>
          <version>3.1.0</version>
        </plugin>
        <!-- see http://maven.apache.org/ref/current/maven-core/default-bindings.html#Plugin_bindings_for_war_packaging -->
        <plugin>
          <artifactId>maven-resources-plugin</artifactId>
          <version>3.0.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.8.0</version>
        </plugin>
        <plugin>
          <artifactId>maven-surefire-plugin</artifactId>
          <version>2.22.1</version>
        </plugin>
        <plugin>
          <artifactId>maven-war-plugin</artifactId>
          <version>3.2.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-install-plugin</artifactId>
          <version>2.5.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-deploy-plugin</artifactId>
          <version>2.8.2</version>
        </plugin>
      </plugins>
    </pluginManagement>
    <resources>
      <resource>
        <directory>
          src/main/java
        </directory>
        <includes>
          <include>**/*.xml</include>
        </includes>
      </resource>

      <resource>
        <directory>src/main/resources</directory>
      </resource>
    </resources>
  </build>
</project>

```



### web.xml

```xml
<!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>
    <!-- 配置上下文参数，加载Spring配置文件 -->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:applicationContext.xml</param-value>
    </context-param>

    <!-- 配置上下文加载监听器 -->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <!-- 配置DispatcherServlet -->
    <servlet>
        <servlet-name>dispatcher</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>dispatcher</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
    
    <welcome-file-list>
        <welcome-file>index.jsp</welcome-file>
    </welcome-file-list>
</web-app>

```



### applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 加载properties配置文件 -->
    <context:property-placeholder location="classpath:jdbc.properties" />

    <!-- 配置数据源 -->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="url" value="${jdbc.url}" />
        <property name="username" value="${jdbc.user}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>

    <!-- 配置SessionFactory -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource" />
        <!-- 配置 xxxMapper.xml 文件的路径 -->
        <property name="mapperLocations" value="classpath*:/mapper/*Mapper.xml" />
        <!-- 配置 MyBatis配置文件 mybatis-config.xml 的路径-->
        <property name="configLocation" value="classpath:mybatis-config.xml" />
    </bean>

    <!-- Mapper扫描配置 -->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <!-- 配置XxxMapper.java的路径 -->
        <property name="basePackage" value="top.alanlee.pam.mapper.**" />
        <!-- 配置sqlSessionFactoryBeanName -->
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory" />
    </bean>

</beans>
```



### mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <settings>
        <setting name="logImpl" value="LOG4J"/>
    </settings>
    <typeAliases>
        <!-- 包别名 -->
        <package name="top.alanlee.pam.entity" />
    </typeAliases>
</configuration>
```



### jdbc.properties

```properties
jdbc.user=root
jdbc.password=123456
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/ssm_template?useUnicode=true&characterEncoding=utf-8
```



### log4j.properties

```pro
log4j.rootLogger=INFO,DEBUG,CONSOLE,file
log4j.logger.com.jingze=debug
log4j.logger.com.ibatis=info 
log4j.logger.com.ibatis.common.jdbc.SimpleDataSource=info 
log4j.logger.com.ibatis.common.jdbc.ScriptRunner=info 
log4j.logger.com.ibatis.sqlmap.engine.impl.SqlMapClientDelegate=info 
log4j.logger.java.sql.Connection=info 
log4j.logger.java.sql.Statement=info 
log4j.logger.java.sql.PreparedStatement=info 
log4j.logger.java.sql.ResultSet=info

log4j.appender.CONSOLE=org.apache.log4j.ConsoleAppender
log4j.appender.Threshold=error
log4j.appender.CONSOLE.Target=System.out
log4j.appender.CONSOLE.layout=org.apache.log4j.PatternLayout
log4j.appender.CONSOLE.layout.ConversionPattern= %-d{yyyy-MM-dd HH:mm:ss} [%5p]-[%.10t]-[%.20C] - %m%n
```



## 运行

### 导入sql

将resource/sql目录下的sql脚本ssm_template.sql导入自己的数据库

### IDEA配置启动参数

![image-20200413170712565](https://alanlee-image-bed.oss-cn-shenzhen.aliyuncs.com/note_images/20200413170714-647525.png)

![image-20200413170803452](E:/%E6%88%91%E7%9A%84%E5%9D%9A%E6%9E%9C%E4%BA%91/OneDrive/%E5%AD%A6%E4%B9%A0/%E7%AC%94%E8%AE%B0/%E5%9B%BE%E7%89%87/note_images/image-20200413170803452.png)



### 浏览器输入http://localhost:8081/ssm/

![image-20200413170139159](https://alanlee-image-bed.oss-cn-shenzhen.aliyuncs.com/note_images/20200413170139-593578.png)

### 浏览器输入http://localhost:8081/ssm/user/get/all，输出json数据

![image-20200413170412413](https://alanlee-image-bed.oss-cn-shenzhen.aliyuncs.com/note_images/20200413170419-946799.png)