要在 Spring Boot 项目中支持每个接口独立限频，并且可以针对接口中的某个参数独立限频，可以使用 `RateLimiter` 结合 `AOP`（面向切面编程）和 `Spring Cloud Gateway` 或 `Spring Boot` 的过滤器机制来实现。以下是一个完整的示例，展示如何实现这一目标。

### 1. 添加依赖

首先，在 `pom.xml` 文件中添加必要的依赖，包括 Spring Boot、Guava 和 Nacos。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>rate-limiter-example</artifactId>
    <version>1.0-SNAPSHOT</version>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.5.4</version>
    </parent>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>com.google.guava</groupId>
            <artifactId>guava</artifactId>
            <version>31.0.1-jre</version>
        </dependency>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
            <version>2021.1</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-aop</artifactId>
        </dependency>
    </dependencies>
</project>
```

### 2. 配置 Nacos

在 `bootstrap.yml` 文件中配置 Nacos 的连接信息。

```yaml
spring:
  application:
    name: rate-limiter-example
  cloud:
    nacos:
      discovery:
        server-addr: 127.0.0.1:8848
      config:
        server-addr: 127.0.0.1:8848
        file-extension: yml
```

### 3. 创建配置类

创建一个配置类来读取 Nacos 配置中的限频设置，并初始化 `RateLimiter`。

```java
import com.google.common.util.concurrent.RateLimiter;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.cloud.context.config.annotation.RefreshScope;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
@RefreshScope
public class RateLimiterConfig {

    @Value("${ratelimiter.default-max-per-second:5}")
    private double defaultMaxPerSecond;

    @Bean
    public RateLimiter defaultRateLimiter() {
        return RateLimiter.create(defaultMaxPerSecond);
    }
}
```

### 4. 创建自定义注解

创建一个自定义注解 `@RateLimit`，用于标记需要限流的接口和参数。

```java
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target({ElementType.METHOD, ElementType.PARAMETER})
@Retention(RetentionPolicy.RUNTIME)
public @interface RateLimit {
    double maxPerSecond() default 5.0;
}
```

### 5. 创建 AOP 切面

创建一个 AOP 切面来拦截带有 `@RateLimit` 注解的方法，并应用限流逻辑。

```java
import com.google.common.util.concurrent.RateLimiter;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.reflect.MethodSignature;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import java.lang.reflect.Method;
import java.util.concurrent.ConcurrentHashMap;

@Aspect
@Component
public class RateLimitAspect {

    @Autowired
    private RateLimiter defaultRateLimiter;

    private final ConcurrentHashMap<String, RateLimiter> rateLimiters = new ConcurrentHashMap<>();

    @Around("@annotation(rateLimit)")
    public Object aroundAdvice(ProceedingJoinPoint joinPoint, RateLimit rateLimit) throws Throwable {
        MethodSignature signature = (MethodSignature) joinPoint.getSignature();
        Method method = signature.getMethod();

        // 获取方法级别的限流设置
        double maxPerSecond = rateLimit.maxPerSecond();

        // 获取方法参数
        Object[] args = joinPoint.getArgs();
        for (int i = 0; i < method.getParameterAnnotations().length; i++) {
            for (Annotation annotation : method.getParameterAnnotations()[i]) {
                if (annotation instanceof RateLimit) {
                    RateLimit paramRateLimit = (RateLimit) annotation;
                    maxPerSecond = paramRateLimit.maxPerSecond();
                    break;
                }
            }
        }

        // 获取或创建 RateLimiter
        String key = method.getName() + "-" + maxPerSecond;
        RateLimiter rateLimiter = rateLimiters.computeIfAbsent(key, k -> RateLimiter.create(maxPerSecond));

        // 获取令牌
        double waitTime = rateLimiter.acquire();
        if (waitTime > 0) {
            System.out.println("Request delayed by " + waitTime + " seconds");
        }

        return joinPoint.proceed();
    }
}
```

### 6. 使用 RateLimit 注解

在需要限流的接口和参数上使用 `@RateLimit` 注解。

#### 示例控制器

```java
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class MyController {

    @GetMapping("/perform")
    @RateLimit(maxPerSecond = 5.0)
    public String performAction(@RequestParam @RateLimit(maxPerSecond = 2.0) String param) {
        System.out.println("Performing action with param: " + param);
        return "Action performed successfully with param: " + param;
    }
}
```

### 7. 启动类

确保你的启动类上有 `@EnableDiscoveryClient` 注解，以便启用 Nacos 配置中心。

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;

@SpringBootApplication
@EnableDiscoveryClient
public class RateLimiterExampleApplication {

    public static void main(String[] args) {
        SpringApplication.run(RateLimiterExampleApplication.class, args);
    }
}
```

### 8. 测试

启动 Spring Boot 应用程序，并多次调用 `/perform` 端点，观察限流效果。你可以通过修改 Nacos 中的配置来动态调整限频设置，Spring Boot 应用程序会自动重新加载配置并应用新的限频设置。

### 总结

通过以上步骤，你可以在 Spring Boot 项目中实现每个接口独立限频，并且可以针对接口中的某个参数独立限频。使用 AOP 切面和自定义注解可以灵活地管理限流逻辑，确保系统的稳定性和性能。如果有任何问题或需要进一步的帮助，请随时提问。