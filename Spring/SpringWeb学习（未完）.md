# SpringWeb学习

## HTTP协议

### 1. HTTP请求格式

1. 请求行
2. 请求头
3. 请求体

### 2. HTTP响应格式

#### 2.1 响应格式

1. 响应行
   - 协议 状态码 描述
   - 如：HTTP/1.1 200 OK
2. 响应头
   - 第二行开始
   - 格式：key: value
3. 响应体
   - 最后一部分，存放响应数据

#### 2.2 响应状态码

| 响应状态码 |                             解释                             |    备注    |
| :--------: | :----------------------------------------------------------: | :--------: |
|  **1xx**   | 响应中，表示请求已经接收，告诉客户端应该继续请求或如果已经完成则忽略 | 临时状态码 |
|  **2xx**   |           成功，表示请求已经被成功接收，处理已完成           |            |
|  **3xx**   | 重定向，重定向到其他地方让客户端再发起一次请求以完成整个处理 |            |
|  **4xx**   | 客户端错误，责任在客户端。如：请求了不存在的资源、禁止访问等 |            |
|  **5xx**   |         服务器错误，责任在服务器，如：程序抛出异常等         |            |

常见的响应状态码：[状态 | Status - HTTP 中文开发手册 - 开发者手册 - 腾讯云开发者社区-腾讯云](https://cloud.tencent.com/developer/chapter/13553)

| 状态码  |            英文描述             |                             解释                             |
| :-----: | :-----------------------------: | :----------------------------------------------------------: |
| **200** |             **OK**              |                  客户端请求成功，即处理成功                  |
| **302** |              Found              | 指示所请求的资源已移动到由Location响应头给定的URL，浏览器会自动重新访问这个页面 |
| **304** |          Not Modified           | 告诉客户端上次资源请求后并服务器未更改，直接使用本地缓存（隐式重定向） |
| **400** |           Bad Request           |            客户端请求有语法错误，不能被服务器理解            |
| **403** |            Forbidden            |        服务器收到请求但拒绝提供服务，如：没有权限访问        |
| **404** |          **Not Found**          |    请求资源不存在，一般是URL输入有误，或网站资源被删除了     |
| **405** |       Method Not Allowed        |         请求方式有误，如：应该用GET请求，却用了POST          |
| **428** |      Precondition Required      | 服务器要求有条件的请求，告诉客户端要访问该资源，必须懈怠特定的请求头 |
| **429** |        Too Many Requests        | 给定时间内访问次数太多，配合Retry-After（多长时间）响应头一起使用 |
| **431** | Request Header Fields Too Large |                请求头太大，服务器不愿处理请求                |
| **500** |    **Internal Server Error**    |     服务器发生不可预期的错误。服务器出异常了，检查日志。     |
| **503** |       Service Unavailable       |   服务器尚未准备好处理请求，服务器刚刚启动，还没初始化好。   |

## Tomcat服务器

Tomcat是一种Web服务器，对HTTP协议的操作进行封装，使得程序员不必直接对协议进行操作

注：SpringBoot**自带Tomcat服务器**，无需额外下载

### Tomcat中的前端控制器

请求响应：

1. 请求，获取请求数据：HttpServletRequest
2. 响应，设置响应数据：HttpServletResponse

BS架构：Browser/Server，浏览器/服务器架构模式。客户端只需浏览器。

CS架构：Client/Server，客户端/服务器架构模式。需下载对应客户端。

## 请求响应

> ### postman
>
> postman：接口测试工具
>
> 网址：[Postman API Platform](https://www.postman.com/)

### 1. 简单请求参数

假设参数：name、age

1. 原始方式(了解即可)

   ```java
   @RequestMapping("/path")
   public String simpleParam(HttpServletRequest request) {
       String name = request.getParameter("name");
       String ageStr = request.getParameter("age");
       int age = Integer.parseInt(ageStr);
       System.out.println(name + ":" + age);
       return "OK";
   }
   ```

2. **SpringBoot方法**：

   ```java
   @RequestMapping("/path")
   public String simpleParam(String name, Integer age) {	// 请求参数名需与形参名一致
       System.out.println(name + ":" + age);
       return "OK";
   }
   ```

请求方式（postman中）：

1. GET请求
   - 直接在地址后写参数或在**Params**填写请求参数
2. POST请求
   - 在**请求体（Body）**中填写参数

注意：

- 请求参数需与方法形参一致

- 若不一致，使用`@RequestParam`完成映射

  ```java
  @RequestMapping("/path")
  public String simpleParam(@RequestParam(name="name")String username, Integer age) {	
      System.out.println(name + ":" + age);
      return "OK";
  }
  ```

- 若使用`@RequestParam`，其中有个属性required默认为true，代表该参数必须传输，否则报错，可手动修改。

### 2. 实体参数

#### 2.1 简单实体参数

简单实体对象：将所有参数封装到一个对象中，请求参数名与形参属性名相同，定义POJO接收

1. 定义实体类，可放在一个包中，如*pojo/user.java*，*entity/user.java*

   ```java
   @data
   public class User {
       private String name;
       private Integer age;
   }
   ```

2. 在controller中定义方法

   ```java
   @RequestMapping("/path")
   public String complexPojo(User user) {
       system.out.println(user);
       return "OK";
   }
   ```

注意：

- 请求参数需与实体对象的属性名一致

#### 2.2 复杂实体参数

复杂实体对象：请求参数名与形参对象属性名相同，即一个实体对象中的属性为另一个实体对象，按照对象层次结构关系即可接受嵌套POJO属性参数

如：

- 实体User：

  ```java
  @data
  public class User {
      private String name;
      private Integer age;
      private Address address;
  }
  ```

- 实体Address

  ```java
  @data
  public class Address {
      private String province;
      private Integer city;
  }
  ```

- 请求参数直接用`address.province`、`address.city`等

#### 2.3 数组集合参数（如复选框）

数组/集合参数:请求参数名与形参数组名称相同且请求参数为多个，定义数组类型形参即可接收参数，或`@RequestParam`绑定参数关系

1. 数组接收

   ```java
   @RequestMapping("/path")
   public String arrayParam(String[] hobby) {
       system.out.println(Arrays.toString(hobby));
       return "OK";
   }
   ```

2. 集合接收

   ```java
   @RequestMapping("/path")
   public String listParam(@RequestParam List<String> hobby) {
       system.out.println(hobby);
       return "OK";
   }
   ```

#### 2.4 日期时间类型参数

日期参数:使用 @DateTimeFormat 注解完成日期参数格式转换，如`@DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss")`

```java
@RequestMapping("/path")
public String dateParam(@DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss") LocalDateTime updateTime) {
    system.out.println(updateTime);
    return "OK";
}
```

#### 2.5 JSON参数

JSON参数:JSON数据键名与形参对象属性名相同，定义POI0类型形参即可接收参数，需要使用 `@RequestBody`标识

1. postman传递JSON参数

   - 请求方式设为**POST**，数据需放在请求体**Body**，选项选择**raw**，数据格式选择JSON

   - 后在下方填写参数

     ![image-20241207190427138](https://gitee.com/jackqueen/pictures/raw/master/202412071904474.png)

2. 服务端接收JSON参数

   - 使用复杂实体接收

   - 属性名和key一致，若有嵌套，实体对象也需要声明

   - 使用`@RequestBody`标识

     ```java
     @RequestMapping("/path")
     public String jsonParam(@RequestBody User user) {
         system.out.println(updateTime);
         return "OK";
     }
     ```

#### 2.6 路径参数

路径参数:参数含在路径中，通过请求URL直接传递参数，使用`{...}`来标识该路径参数，需要使用 `@PathVariable` 获取路径参数

如：*http://localhost:8080/page/1*，后面的1是动态变换的

需要修改`@RequestMapping("/page/{id}")`，指定路径参数的参数名，比如id，之后使用`@PathVariable`注解获取

```java
@RequestMapping("/page/{id}")
public String pathParam(@PathVariable Integer id) {
    system.out.println(id);
    return "OK";
}
```

注意，若有多个路径参数，只需再往后加，如`/page/{id}/{name}`和`@PathVariable Integer id, @PathVariable String name`

#### 2.7 常见接收参数的注解

| 常见注解            | 说明                                                         | 适用请求方法                                        |
| ------------------- | ------------------------------------------------------------ | --------------------------------------------------- |
| `@RequestParam`     | 用于绑定请求的查询参数或表单数据到方法参数                   | 适用GET和POST等，可使用method属性指定               |
| `@PathVariable`     | 用于绑定 URL 路径中的动态变量到方法参数                      | 适用于 RESTful 风格的路径参数（`GET`、`DELETE` 等） |
| `@RequestBody`      | 用于绑定请求体中的 JSON 或 XML 数据到实体类                  | 适用于 `POST` 或 `PUT` 请求，通常传递复杂对象       |
| `@RequestHeader`    | 用于绑定 HTTP 请求头中的某个值到方法参数                     | 适用于所有请求方法                                  |
| `@CookieValue`      | 用于绑定请求中的 Cookie 值到方法参数                         | 适用于所有请求方法                                  |
| `@ModelAttribute`   | 用于从请求参数中绑定多个字段到实体类（类似 `@RequestParam`） | 适用于 `GET` 或 `POST` 请求，处理表单数据           |
| `@RequestPart`      | 用于绑定 `multipart/form-data` 请求中的文件或字段            | 适用于文件上传场景（`POST` 或 `PUT`）               |
| `@SessionAttribute` | 用于从会话中获取属性值                                       | 适用于需要维护会话状态的场景                        |

#### 2.8 每种方法与注解的对应关系

1. GET请求

| **参数类型**             | **发送方式**                                | **对应注解**                    | **示例**                                       |
| ------------------------ | ------------------------------------------- | ------------------------------- | ---------------------------------------------- |
| 查询参数（key=value）    | URL 查询参数：`/user?name=John&age=30`      | @RequestParam                   | `@RequestParam("name") String name`            |
| 路径变量                 | RESTful 风格路径：`/user/123`               | @PathVariable                   | `@PathVariable("id") Long id`                  |
| HTTP 请求头              | 自定义请求头：`Authorization: Bearer token` | @RequestHeader                  | `@RequestHeader("Authorization") String token` |
| Cookie 值                | Cookie：`Cookie: sessionId=abc123`          | @CookieValue                    | `@CookieValue("sessionId") String sessionId`   |
| 表单或查询参数绑定到对象 | `/user?name=John&age=30`                    | @ModelAttribute / @RequestParam | `User user` (Spring 自动映射参数到对象字段)    |

2. POST请求



### 3. @RestController

`@ResponseBody`：

- 类型：方法注解、类注解
- 位置：Controller方法上/类上
- 作用：将方法返回值直接响应，如果返回值类型是实体对象/集合，将会转换为JSON格式响应
- 说明：**@RestController = @Controller + @ResponseBody**

`@RestController`：

- 如上所述：**@RestController = @Controller + @ResponseBody**
- 放在Controller类上方

每一个@RequestMapping注解下的方法表示一个功能接口，即API文档中描述的接口。

### 4. 统一响应结果Result

Controller方法中的返回类型用统一响应结果Result类表示

**Result**:

```java
public class Result {
    // 响应码，1表示成功，0表示失败 
    private Integer code; 
    // 提示信息
    private String msg;
    // 返回的数据
    private Object data;
    
    // 其他静态方法
    private static Object success();
}
```

> Springboot项目的静态资源（html、css、js等前端资源）默认存放目录为classpath:/static、classpath:/public、classpath:/resources
>
> 注：
>
> - classpath表示类路径，在Springboot项目中，即为resources目录

## 分层解耦

### 1. 三层架构

- 第一层：**Controller**，控制层，接收前端发送的请求，对请求进行处理，并响应数据

- 第二层：**Service**，业务逻辑层，处理具体的业务逻辑

- 第三层：**dao**，数据访问层（Data Access Object）（持久层），负责数据访问操作，包括数据的增删改查

流程：前端（浏览器）发出请求 -> Controller接收请求 -> Service层根据请求调用Dao层获取数据 -> Dao层操作文件（如数据库等）获取数据 -> Service层处理数据 -> Controller响应数据 -> 响应前端请求

#### 1.1 Dao数据访问层

为增强灵活性、拓展性，先编写接口，后编写实现类

如：

```bash
src/main/java
└── com.example.project
    └── dao
        └── UserDao.java
        └── impl
            └── UserDaoA.java
            └── UserDaoB.java
```

#### 1.2 Service逻辑处理层

为增强灵活性、拓展性，先编写接口，后编写实现类

如：

```bash
src/main/java
└── com.example.project
    └── service
        └── UserService.java
        └── impl
            └── UserServiceA.java
            └── UserServiceB.java
```

在*UserServiceA*中，使用面向接口的思想调用Dao，如：

#### 1.3 Controller控制层

接收数据，响应数据，将数据传给Service层处理

```bash
src/main/java
└── com.example.project
    └── controller
        └── UserController.java
```

### 2. 分层解耦

内聚：软件中各功能模块内部的功能联系

耦合：衡量软件中各层/模块之间的依赖、关联的程度

软件设计原则：**高内聚、低耦合**

IOC与DI

- 控制反转：Inversion Of Control，简称IOC。对象的创建控制权由程序自身转移到外部（容器）。

- 依赖注入：Dependency Injection，简称DI。容器为应用程序提供运行时，所以来的资源。

Bean对象：IOC容器中创建、管理的对象，称之为bean。

#### 2.1 IOC/DI入门

需要声明使用到哪些实现类（声明对应接口）就在哪些实现类上加上`@Component`注解（该注解又有衍生注解在下一小节讲到），并在声明上方加上`@Autowired`注解。

- `@Component`：将当前类交给IOC容器管理，成为IOC容器中的bean（IOC）
- `@Autowired`：运行时，IOC容器会提供该类型的bean对象，并赋值给该变量（DI）

1. Service层和Dao层的视线类，交给IOC容器管理，需加上`@Component`注解。

   - *UserService.java*：

     ```java
     @Component	// 将当前类交给IOC容器管理，成为IOC容器中的bean，或者使用@Service
     public class UserServiceA implements UserService {
         @Autowired	// 运行时，IOC容器会提供该类型的bean对象，并赋值给该变量（DI）
         private UserDao userDao;
         // 业务逻辑逻辑
         // ...
     }
     ```

   - *UserDao.java*：

     ```java
     @Component	// 或者使用@Respository（用得少）
     public class UserDaoA implements UserDao {
         // 业务逻辑逻辑
         // ...
     }
     ```

2. 为Controller及Service注入运行时，依赖的对象，需在声明对应接口类型上加上`@AutoWired`注解

   - *UserController.java*：

     ```java
     @RestController
     public class UserController {
         @Autowired		// 加上注解
         private UserService userService;    // 声明接口类型，暂不创建实现类
         
         @RequestMapping("/user")
         public Result users() {
             // 1. 调用Service，获取数据
             List<User> users = userService.listUser();
             return Result.success(users);
         }
     }
     ```

   - *UserService.java*：
   
     （见上方第"1."处）

#### 2.2 `@Component`的衍生注解

注意：

- 注解中有一个属性value，表示bean的类名，使用举例：`@Repository(value="daoA")`或`@Repository("daoA")`，默认为类名首字母小写
- 使用下面四个注解都可声明bean，但是在springboot集成web开发中，声明控制器bean只能用`@Controller`

| 注解          | 位置                                                         |
| ------------- | ------------------------------------------------------------ |
| `@Component`  | 不属于以下三类时，比如一些工具类                             |
| `@Controller` | 标注在Controller控制器类上，**被`@RestController`代替**（`@Controller`+`@ResponseBody`） |
| `@Service`    | 标注在Service业务类上                                        |
| `@Repository` | 标注在Dao数据访问类上（由于与mybatis整合，用得少）           |

**常用bean注解**

| 注解              | 位置                             |
| ----------------- | -------------------------------- |
| `@Component`      | 不属于以下三类时，比如一些工具类 |
| `@RestController` | Controller控制器类上             |
| `@Service`        | Service业务类上                  |
| `@Mapper`         | Dao数据访问类上                  |

#### 2.3 Bean的组件扫描

注意：

- 前面声明bean的四大注解，要想生效，还需要被组件扫描注解`@ComponentScan`扫描
- `@Componentscan`注解虽然没有显式配置，但是实际上已经包含在了启动类声明注解 `@SpringBootApplicafjon
  `中，默认扫描的范围是**启动类所在包及其子包**。

若要扫描启动类所在包外的类：

1. 启动类上方添加注解（不推荐）

   ```java
   @ComponentScan({"子包","com.example"})  // 扫描需要扫描的包及启动类所在包
   ```

2. 直接**全放在启动类所在包及其子包中**

   ```bash
   src/main/java
   └── com.example.project
       ├── service
       │   └── UserService.java
       │   └── impl
       │       └── UserServiceA.java
       ├── controller
       ├── dao
       └── SpringbootApplication.java
   ```

#### 2.4 DI详解

`@Autowired`默认是按类型进行，若有多个相同类型的bean，则`@Autowired`无法完成自动装配，程序报错

解决方案：

1. `@Primary`设置bean优先级，想让哪个生效则在该实现类上添加此注解

   ```java
   @Primary	// 此实现类优先
   @Service	// 将当前类交给IOC容器管理，成为IOC容器中的bean，或者使用@Service
   public class UserServiceA implements UserService {
       @Autowired	// 运行时，IOC容器会提供该类型的bean对象，并赋值给该变量（DI）
       private UserDao userDao;
       // 业务逻辑逻辑
       // ...
   }
   ```

2. `@Qualifier("实现类名")`手动设置，配合`@Autowired`使用，指定bean名（实现类名首字母小写）

   ```java
   @Service	// 将当前类交给IOC容器管理，成为IOC容器中的bean，或者使用@Service
   public class UserServiceA implements UserService {
       @Qualifier("userDaoA")	// 指定实现类
       @Autowired
       private UserDao userDao;
       // 业务逻辑逻辑
       // ...
   }
   ```

3. `@Resource(name = "实现类名")`手动设置，代替`@Autowired`，指定类名

   ```java
   @Component	// 将当前类交给IOC容器管理，成为IOC容器中的bean，或者使用@Service
   public class UserServiceA implements UserService {
       @Resource(name = "userDaoA")
       private UserDao userDao;
       // 业务逻辑逻辑
       // ...
   }
   ```

`@Resource`与`@Autowired`区别：

- `@Autowired`是Spring框架提供的注解，而`@Resource`是JDK提供的注解
- `@Autowired`默认是按照类型注入，而`@Resource`默认是按照名称注入

## 数据库连接池

标准接口：DataSource

- 官方（sun）提供的数据库连接池接口，由第三方组织实现此接口
- 功能：获取连接`Connection getConnection() throws SQLException;`
- 常见产品：
  - **Hikar追光者：SpringBoot默认**
  - **Druid德鲁伊**
  - DBCP
  - C3P0

### 1. 数据库连接

SpringBoot中设置数据库连接信息需在配置文件*application.properties*中

*application.properties*：

```properties
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/emrs
spring.datasource.username=root
spring.datasource.password=123456
```

### 2. 切换Druid数据库连接池

*pom.xml*：

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid-spring-boot-starter</artifactId>
    <version>1.2.8</version>
</dependency>
```

*application.properties*：

```properties
spring.datasource.druid.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.druid.url=jdbc:mysql://localhost:3306/mybatis
spring.datasource.druid.username=root
spring.datasource.druid.password=123456
```

### 3. Lombok工具库

Lombok是一个实用的java类库，能通过注解的形式自动生成构造器、getter/setter、equals、hashcode、toString等方法，并可自动化生成日志变量，简化java开发、提高效率

| 注解                  | 作用                                                         |
| --------------------- | ------------------------------------------------------------ |
| `@Getter/@Setter`     | 为所有的属性提供get/set方法                                  |
| `@ToString`           | 会给类自动生成易阅读的toString方法                           |
| `@EqualsAndHashCode`  | 根据类所拥有的非静态字段自动重写equals方法和hashCode放啊     |
| `@Data`               | **提供了更总和的生成代码功能（@Getter+@Setter+@ToString+@EqualsAndHashCode）** |
| `@NoArgsConstructor`  | **为实体类生成无参构造方法**                                 |
| `@AllArgsConstructor` | **为实体类生成除了static修饰的字段之外带有各参数的构造方法** |
