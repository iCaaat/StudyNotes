# SpringBoot学习

---

---

# 1.SpringBoot入门

## 1.1 创建SpringBoot项目

### 1.1.1 idea创建

注意，需联网，（使用的也是spring官网https://start.spring.io/）

创建项目选择Spring Boot或Spring Initializr

需要什么功能勾选

### 1.1.2 官网Spring Initializr

官方地址：[Spring Initializr](https://start.spring.io/)

### 1.1.3阿里云版

idea创建，将URL改为：https://start.aliyun.com/

特点：模块选择中有中文

### 1.1.4手工制作

1. 创建maven项目

   pom.xml中添加以下内容

   ```xml
   // 继承父工程
   <parent>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-parent</artifactId>
       <version>3.4.0</version>
       <relativePath/> <!-- lookup parent from repository -->
   </parent>
   
   // 依赖
   <dependencies>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-web</artifactId>
       </dependency>
       <dependency>
           <groupId>org.mybatis.spring.boot</groupId>
           <artifactId>mybatis-spring-boot-starter</artifactId>
           <version>3.0.4</version>
       </dependency>
   </dependencies>
   ```

2. 创建com/example/Application.java

   ```java
   package com.example;
   
   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   
   @SpringBootApplication
   public class Application {
       public static void main (String[] args) {
           SpringApplication.run(Application.class);
       }
   }
   ```

## 1.2 入门程序解析

Spring Boot项目的pom.xml中：

### 1.2.1 < parent >版本统一管理

> ```xml
> <parent>
>  <groupId>org.springframework.boot</groupId>
>  <artifactId>spring-boot-starter-parent</artifactId>
>  <version>3.4.0</version>
>  <relativePath/> <!-- lookup parent from repository -->
> </parent>
> ```

**依赖版本管理（不是依赖）**：整合了所需依赖的版本信息，后续导入已整合到Spring的依赖**无需写版本信息**（除非其中没有定义），只需关注**SpringBoot主版本（< parent >）**，减少依赖冲突

### 1.2.2 starter起步依赖

> ```xml
> <dependency>
>  <groupId>org.springframework.boot</groupId>
>  <artifactId>spring-boot-starter-web</artifactId>
> </dependency>
> <dependency>
>     <groupId>org.mybatis.spring.boot</groupId>
>     <artifactId>mybatis-spring-boot-starter</artifactId>
>     <version>3.0.4</version>
> </dependency>
> ```

**项目依赖**：包含了大量当前项目需要的依赖信息

1. 开发SpringBoot程序需要导入坐标时通常导入对应的starter
2. 每个不同的starter根据功能不同，通常包含多个依赖坐标
3. 使用starter可以实现快速配置的效果，达到简化配置的目的

### 1.2.3 引导类Application

> ```java
> @SpringBootApplication
> public class Application {
>     public static void main (String[] args) {
>         SpringApplication.run(Application.class);
>     }
> }
> ```

初始化Spring容器，加载JavaBean，整个程序的入口

注意：类名（Application可改）

### 1.2.4 内嵌tomcat

> ```xml
> <dependency>
>     <groupId>org.springframework.boot</groupId>
>     <artifactId>spring-boot-starter-web</artifactId>
> </dependency>                                          
> ```

其中内嵌web服务器依赖，即内嵌web服务器

注意：可换web服务器，使用maven排出tomcat服务器，而引入其他web服务器

SpringBoot内置服务器：

1. **tomcat（默认）**
2. jetty
3. undertow

> ### （知识补充：REST开发）
>
> #### 1. REST风格
>
> **REST风格**，表现形式状态转换，描述的是资源的访问形式
>
> 1. 传统风格资源描述形式：
>
>    - http://localhost/user/getById?id=1
>
>    - http://localhost/user/saveUser
>
> 2. REST风格描述形式：
>
>    - http://localhost/user/1
>
>    - http://localhost/user
>
> 优点：
>
> - 隐藏资源访问行为
> - 简化
>
> ![image-20241207015138494](https://gitee.com/jackqueen/pictures/raw/master/202412070151773.png)
>
> **RESTful**：根据REST风格对资源进行访问
>
> #### 2. RESTful
>
> 1. 设置http请求动作
>
>    ![image-20241207020258059](https://gitee.com/jackqueen/pictures/raw/master/202412070203966.png)
>
> 2. 设定请求参数（路径变量）
>
>    ![image-20241207020355301](https://gitee.com/jackqueen/pictures/raw/master/202412070203125.png)
>
> 3. ![image-20241207020449104](https://gitee.com/jackqueen/pictures/raw/master/202412070204454.png)

---

---

# 2.SpringBoot配置

## 2.1 application配置文件

SpringBoot提供两种配置文件格式，application.properties或application.yml(yaml)进行配置

优先级：properties > yml > yaml



> ## YAML
>
> ### 基本语法
>
> * 大小写敏感
> * 数值前**必须有空格**，作为分隔符
> * 使用缩进表示**层级关系**
> * 缩进时不允许使用Tab键，只允许使用空格（各系统对应Tab空格数不同），idea无需担心，会**自动转换**
> * 缩进的**空格数不重要**，只要**相同层级左侧元素对齐即可**
> * “#“ 表示行注释
>
> ### 数据格式
>
> * 对象（map）：键值对的集合
>
>   ```yaml
>   person:
>     name: zhangsan
>   
>   # 行内写法：
>   person: {name: zhangsan}
>   ```
>
> * 数组：一组按次序排列的值
>
>   ```yaml
>   address:
>     - beijing
>     - shanghai
>     
>   # 行内写法
>   address: [beijing,shanghai]
>   ```
>
> * 纯量：单个的、不可再分的值
>
>   ```yaml
>   msg1: 'hello \n world' # 单引号忽略转义字符
>   msg2: "hello \n world" # 双引号识别转义字符
>   ```
>
> ### 参数引用
>
> * ```yaml
>   name: lisi
>   
>   person:
>     name: ${name} # 引用上边定义的name值
>   ```
>
> * 

### 2.1.1 端口修改

properties:

`server.port=8080`

yml:

```yml
server:
	port: 8080
```

### 2.1.2 数据库配置

* spring.datasource: Spring Boot用来配置数据库连接的数据源属性

* driver-class-name: 指定 MySQL 数据库连接所需的驱动类。`com.mysql.cj.jdbc.Driver` 是 MySQL Connector/J 8.x 以后的驱动类名。

* url: 是数据库的连接地址，格式如下

  ```php-template
  jdbc:mysql://<主机名>:<端口>/<数据库名>?参数...
  ```

* username: 数据库登录用户名

* password：数据库登录密码

```yaml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/mris_database?useUnicode=true&characterEncoding=utf-8&useSSL=false&serverTimezone=UTC
    username: root
    password: 123456
```

> ## mybatis的配置
>
> `mapper-locations`: 
>
> * 指定 MyBatis 的 XML 映射文件（`*.xml`）的位置
> * `classpath:` 表示从 `resources` 目录下找文件
> * 示例：`classpath:mapper/*.xml` → 查找 `src/main/resources/mapper` 下所有 `.xml` 文件。
>
> `type-aliases-package:`
>
> * 指定实体类所在的包路径，MyBatis 会自动为包中的类生成别名（简化 `resultType` 写法）。
> * 示例：`com.example.project.model` → 类名 `User` 在 XML 中可以直接写 `User` 而不是全限定名。
>
> `mybatis.configuration.map-underscore-to-camel-case`:
>
> * 是否开启自动驼峰命名映射（例如 `user_name` → `userName`）。
> * 设置为 `true` 后可以减少手动编写 `<resultMap>` 的工作。
>
> `log-impl: org.apache.ibatis.logging.stdout.StdOutImpl`
>
> * 设置 MyBatis 使用哪种日志实现类
> * **`org.apache.ibatis.logging.stdout.StdOutImpl`**：表示使用标准输出（`System.out.println`）的方式打印日志，也就是会把 SQL 打印在控制台。
>
> **示例**
>
> ```yaml
> mybatis:
>   mapper-locations: classpath:mapper/*.xml
>   type-aliases-package: com.example.project.model
>   configuration:
>     map-underscore-to-camel-case: true
>     log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
> 
> ```



 
