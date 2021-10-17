# Spring Boot 深入学习笔记

## 1 Thymeleaf

Spring Boot 默认不支持 JSP，需要引入第三方模板引擎技术来实现页面渲染。

下面介绍使用 Thymeleaf 的方式：

#### (1) 引入启动器

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

引入 Thymeleaf 启动器后，会自动配置好 SpringTemplateEngine 和 ThymeleafViewResolver（视图解析器），我们只需要开发页面即可。

页面需要放在 resources/templates 目录中，并且以 .html 结尾。

补充：所有 Thymeleaf 的配置值都在 ThymeleafProperties 中。

#### (2) 页面

在 templates 中编写页面 helloThymeleaf.html：

```html
<!DOCTYPE html>
<!-- 加上Thymeleaf的名称空间，可以有代码提示 -->
<html lang="en" xmlns:th="http://www.thymeleaf.org">
  <head>
    <meta charset="UTF-8">
    <title>Hello Thymeleaf</title>
  </head>
  <body>
    <!-- 域中的属性取值用${} -->
    <h1 th:text="${msg}">Hello</h1>
    <h2>
      <a href="www.zoe.com" th:href="${link}">去百度-使用$</a>
      <br />
      <!-- link会被当做字符串，/表示当前项目，如果项目有前置路径，会自动加在/link前面 -->
      <a href="www.zoe.com" th:href="@{/link}">去百度-使用@</a>
    </h2>
  </body>
</html>
```

项目中前置路径配置示例：

```properties
server.servlet.context-path=/zoe
```

#### (3) 请求处理

在 controller 层中新建 ThymeleafTestController 类用于处理请求：

```java
@Controller
public class ThymeleafTestController {
  @GetMapping("/thymeleaf")
  public String thymeleaf(Model model){
    // model中的数据会被放在请求域中，相当于调用了request.setAttribute('a', aa)
    model.addAttribute("msg", "Hello Thymeleaf");
    model.addAttribute("link", "www.baidu.com");
    // templates中的helloThymeleaf.html页面
    return "helloThymeleaf";
  }
}
```

最后，访问 `http://localhost:8080/zoe/thymeleaf`，即可访问上面的页面。

## 2 Lombok 简化开发

Lombok 可以简化 JavaBean 开发。

首先需要引入依赖：

```xml
<dependency>
  <groupId>org.projectlombok</groupId>
  <artifactId>lombok</artifactId>
  <optional>true</optional>
</dependency>
```

然后安装 IDEA 的 Lombok 插件：

![image-20211010231336187](../images/image-20211010231336187.png)

最后，简化的 JavaBean 写法如下：

```java
import lombok.*;

@NoArgsConstructor
@AllArgsConstructor
@ToString
@Data
@EqualsAndHashCode
public class User {
  private String userName;
  private String password;
}
```

在编译阶段，@Data 可以生成 getter、setter 方法，@ToString 可以生成 toString 方法（用于打印日志）。

@NoArgsConstructor 表示无参构造器；@AllArgsConstructor 表示全参构造器，部分参数构造器需要手写。

@EqualsAndHashCode 用于重写 HashCode（不太懂，需要研究一下）。

打包的时候，Lombok 无需打到 jar 包中，需要排除掉：

```xml
<build>
  <plugins>
    <plugin>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-maven-plugin</artifactId>
      <configuration>
        <excludes>
          <exclude>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
          </exclude>
        </excludes>
      </configuration>
    </plugin>
  </plugins>
</build>
```

## 3 静态资源

静态资源目录：**类路径**下的 `/static`、`/public`、`/resources`、`/META-INF/resources`。

**类路径的根路径**是 `/src/main/resources`。

如果在静态资源目录中放入静态资源，比如一张图片 `xxx.jpg`，那么启动项目后，可以通过 `localhost:8080/xxx.jpg` 访问该图片。

`localhost:8080/` 是项目根路径。

请求进来，会先去看 Controller 中有没有处理，不能处理的所有请求会交给静态资源处理器，如果在静态资源目录中没有找到相应名称的静态资源，就会报 404。

在实际项目中，访问静态资源，一般会加一个路径前缀（方便拦截器拦截并处理动态请求），比如，想通过 `localhost:8080/zoe/xxx.ipg` 访问上面的图片，可以在配置文件中增加如下配置：

```properties
spring.mvc.static-path-pattern=/zoe/**
```

也可以通过配置，修改默认的静态资源目录：

```properties
spring.web.resources.static-locations=classpath:/res/
```

在静态资源目录中放入 index.html 文件，启动项目后，访问 `localhost:8080` 会默认访问 index.html（欢迎页），也可以在 Controller 中通过处理 `/index` 请求来实现欢迎页。

把 favicon.ico 文件放入静态资源目录，可以实现自定义网页小图标的功能。

## 4 数据访问

导入 **JDBC 场景**和**数据库驱动**：

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-jdbc</artifactId>
</dependency>
<dependency>
  <groupId>mysql</groupId>
  <artifactId>mysql-connector-java</artifactId>
  <version>8.0.22</version>
</dependency>
```

JDBC 场景中是不包含数据库驱动的，需要根据项目中使用的具体的数据库类型以及版本，来导入相应的数据库驱动。

虽然 Spring Boot 对 MySQL 的驱动版本做了版本仲裁，但是实际开发场景中，需要保证驱动版本和数据库版本相对应。

JDBC 场景中默认使用的数据源（数据库连接池）是 HikariDataSource，它可以帮助我们连接数据库。

修改**数据源**相关的配置项是以 spring.datasource 为前缀的；在容器中没有数据源时才自动配置 HikariDataSource；在没有配置 MyBatis 的情况下，可以使用自带的 JdbcTemplate 来操作数据库（CRUD）。

比如，本地有个数据库 demo，配置文件中相关配置如下：

```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/demo?characterEncoding=UTF8&autoReconnect=true&serverTimezone=Asia/Shanghai
    username: root
    password: root
    driver-class-name: com.mysql.cj.jdbc.Driver
```

使用 JdbcTemplate 操作数据库的测试代码如下：

```java
@Slf4j
@SpringBootTest
class SpringBootAdminWebApplicationTests {

  @Autowired
  JdbcTemplate jdbcTemplate;

  @Test
  void contextLoads() {
    Long count = jdbcTemplate.queryForObject("select count(*) from test", Long.class);
    log.info("记录总数：{}", count);
  }
}
```

## 5 Druid

Druid 是阿里开源的数据库连接池，它能够提供强大的监控和扩展功能。

下面介绍两种方式整合 Druid 数据源，替换 JDBC 中默认的数据源 HikariDataSource，一种是自定义的方式，一种是使用场景启动器。

#### (1) 自定义方式

首先，引入依赖：

```xml
<dependency>
  <groupId>com.alibaba</groupId>
  <artifactId>druid</artifactId>
  <version>1.1.17</version>
</dependency>
```

然后，新建 config 目录，并在目录中新建 MyDataSourceConfig 配置类：

```java
/**
 * 注解@Configuration表示MyDataSourceConfig是一个配置类
 * 如果配置了Druid数据源，默认的HikariDataSource会失效
 */
@Configuration
public class MyDataSourceConfig {
  // 把DataSource中的属性与配置文件中的以spring.datasource开头的配置进行绑定
  @ConfigurationProperties("spring.datasource")
  @Bean
  public DataSource dataSource() throws SQLException {
    DruidDataSource druidDataSource = new DruidDataSource();
    // 打开Druid的监控统计功能，需要配置StatFilter，并配置防火墙
    // 也可以配置在配置文件中
    druidDataSource.setFilters("stat,wall");
    return druidDataSource;
  }

  /**
     * 配置Druid监控页功能，并设置登录名和密码
     */
  @Bean
  public ServletRegistrationBean statViewServlet() {
    StatViewServlet statViewServlet = new StatViewServlet();
    ServletRegistrationBean<StatViewServlet> registrationBean =  new ServletRegistrationBean<StatViewServlet>(statViewServlet, "/druid/*");
    registrationBean.addInitParameter("loginUsername", "admin");
    registrationBean.addInitParameter("loginPassword", "123456");
    return registrationBean;
  }

  /**
     * WebStatFilter用于采集web-jdbc关联监控的数据，监控web应用
     */
  @Bean
  public FilterRegistrationBean webStatFilter() {
    WebStatFilter webStatFilter = new WebStatFilter();
    FilterRegistrationBean<WebStatFilter> filterFilterRegistrationBean = new FilterRegistrationBean<WebStatFilter>(webStatFilter);
    filterFilterRegistrationBean.setUrlPatterns(Arrays.asList("/*"));
    filterFilterRegistrationBean.addInitParameter("exclusions","*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*");
    return filterFilterRegistrationBean;
  }
}
```

最后访问 `http://localhost:8080/druid`，即可访问 Druid 监控页。

#### (2) 场景启动器



























