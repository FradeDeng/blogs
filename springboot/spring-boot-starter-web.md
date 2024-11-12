`spring-boot-starter-web` 是 Spring Boot 提供的一个起步依赖（Starter），用于简化 Web 应用的开发。它包含了构建 Web 应用所需的所有依赖，包括 `spring-web` 和 `spring-webmvc` 模块，以及其他必要的库。下面详细介绍 `spring-boot-starter-web` 的作用及其内部操作。

### 1. 作用

- **简化依赖管理**：通过一个单一的依赖声明，自动引入构建 Web 应用所需的所有依赖。
- **自动配置**：自动配置 Web 应用的常见设置，如 `DispatcherServlet`、嵌入式服务器（如 Tomcat）、HTTP 消息转换器等。
- **提供默认配置**：提供合理的默认配置，使得开发者可以快速启动和运行 Web 应用，而无需进行大量的手动配置。

### 2. 内部操作

#### 2.1. 依赖管理

`spring-boot-starter-web` 会自动引入以下依赖：

1. **`spring-web`**：
    - 提供 Web 开发的基础支持，包括 HTTP 请求处理、消息转换等。
    - 依赖示例：
      ```xml
      <dependency>
          <groupId>org.springframework</groupId>
          <artifactId>spring-web</artifactId>
      </dependency>
      ```

2. **`spring-webmvc`**：
    - 提供完整的 MVC 框架支持，包括控制器、视图解析、表单处理等。
    - 依赖示例：
      ```xml
      <dependency>
          <groupId>org.springframework</groupId>
          <artifactId>spring-webmvc</artifactId>
      </dependency>
      ```

3. **嵌入式服务器**：
    - 默认情况下，`spring-boot-starter-web` 会引入 Tomcat 作为嵌入式服务器。
    - 依赖示例：
      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-tomcat</artifactId>
          <scope>provided</scope>
      </dependency>
      ```
    - 也可以通过排除默认的嵌入式服务器并引入其他服务器（如 Jetty 或 Undertow）来更改嵌入式服务器。

4. **JSON 处理**：
    - 引入 Jackson 作为默认的 JSON 处理库。
    - 依赖示例：
      ```xml
      <dependency>
          <groupId>com.fasterxml.jackson.core</groupId>
          <artifactId>jackson-databind</artifactId>
      </dependency>
      ```

5. **其他常用库**：
    - 引入 `spring-boot-starter-validation` 用于表单验证。
    - 依赖示例：
      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-validation</artifactId>
      </dependency>
      ```

#### 2.2. 自动配置

`spring-boot-starter-web` 通过 `@EnableAutoConfiguration` 注解自动配置以下组件：

1. **`DispatcherServlet`**：
    - 自动注册 `DispatcherServlet`，并将其映射到 `/` 路径。
    - 配置 `DispatcherServlet` 的初始化参数，如 `contextConfigLocation` 和 `contextClass`。

2. **嵌入式服务器**：
    - 自动配置嵌入式服务器（如 Tomcat），并设置默认端口（8080）和其他常用配置。
    - 通过 `ServerProperties` 类管理服务器配置。

3. **HTTP 消息转换器**：
    - 自动配置 `HttpMessageConverters`，支持多种数据格式（如 JSON、XML）的转换。
    - 引入默认的消息转换器，如 `MappingJackson2HttpMessageConverter`。

4. **静态资源处理**：
    - 自动配置静态资源的处理，支持从 `classpath:/static/`、`classpath:/public/`、`classpath:/resources/` 和 `classpath:/META-INF/resources/` 目录中提供静态资源。
    - 通过 `ResourceProperties` 类管理静态资源的配置。

5. **视图解析器**：
    - 自动配置视图解析器（如 `InternalResourceViewResolver`），支持 JSP、Thymeleaf 等视图技术。
    - 通过 `ViewResolverProperties` 类管理视图解析器的配置。

6. **错误处理**：
    - 自动配置全局错误处理机制，提供默认的错误页面和异常处理。
    - 通过 `ErrorMvcAutoConfiguration` 类管理错误处理。

7. **Web 客户端**：
    - 提供 `RestTemplateBuilder`，用于创建和配置 `RestTemplate`。
    - 通过 `RestTemplateAutoConfiguration` 类管理 `RestTemplate` 的配置。

### 3. 示例

以下是一个简单的 Spring Boot 项目示例，展示了如何使用 `spring-boot-starter-web` 模块。

1. **创建控制器**：

```java
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

    @GetMapping("/hello")
    public String hello() {
        return "Hello, World!";
    }
}
```

2. **创建主类**：

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
```

3. **pom.xml**：

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>jar</packaging>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.5.4</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```

### 总结

- **`spring-boot-starter-web`** 是一个起步依赖，用于简化 Web 应用的开发。
- **依赖管理**：自动引入 `spring-web`、`spring-webmvc`、嵌入式服务器、JSON 处理库等。
- **自动配置**：自动配置 `DispatcherServlet`、嵌入式服务器、HTTP 消息转换器、静态资源处理、视图解析器、错误处理等。
- **简化开发**：通过合理的默认配置，使得开发者可以快速启动和运行 Web 应用，而无需进行大量的手动配置。

通过使用 `spring-boot-starter-web`，开发者可以专注于业务逻辑的开发，而不用担心底层的配置和依赖管理。