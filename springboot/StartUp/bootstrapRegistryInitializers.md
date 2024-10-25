`bootstrapRegistryInitializers` 列表的内容通常来自以下几个途径：

1. **自动配置**：
    - Spring Boot 的自动配置机制会扫描类路径中的 `BootstrapRegistryInitializer` 实现类，并将其自动添加到 `bootstrapRegistryInitializers` 列表中。这些实现类通常带有 `@AutoConfiguration` 注解，或者通过 `spring.factories` 文件声明。

2. **显式注册**：
    - 开发者可以在 `SpringApplication` 实例中显式地注册 `BootstrapRegistryInitializer` 实现类。这通常在创建 `SpringApplication` 实例时通过 `addInitializers` 方法完成。

3. **配置文件**：
    - 在 `spring.factories` 文件中声明 `BootstrapRegistryInitializer` 实现类。Spring Boot 会读取这些配置文件，并将声明的类实例化后添加到 `bootstrapRegistryInitializers` 列表中。

### 详细说明

#### 1. 自动配置

Spring Boot 的自动配置机制会扫描类路径中的 `BootstrapRegistryInitializer` 实现类，并将其自动添加到 `bootstrapRegistryInitializers` 列表中。这些实现类通常带有 `@AutoConfiguration` 注解，或者通过 `spring.factories` 文件声明。

例如，假设有一个 `MyBootstrapRegistryInitializer` 类：

```java
import org.springframework.boot.BootstrapRegistry;
import org.springframework.boot.BootstrapRegistryInitializer;

public class MyBootstrapRegistryInitializer implements BootstrapRegistryInitializer {

    @Override
    public void initialize(BootstrapRegistry registry) {
        // 在这里进行一些初始化操作
        registry.register(MyService.class, () -> new MyService());
    }
}
```

如果这个类在类路径中，Spring Boot 会自动检测并将其添加到 `bootstrapRegistryInitializers` 列表中。

#### 2. 显式注册

开发者可以在创建 `SpringApplication` 实例时显式地注册 `BootstrapRegistryInitializer` 实现类。这通常通过 `addInitializers` 方法完成。

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class MyApplication {

    public static void main(String[] args) {
        SpringApplication application = new SpringApplication(MyApplication.class);
        application.addInitializers(new MyBootstrapRegistryInitializer());
        application.run(args);
    }
}
```

#### 3. 配置文件

在 `spring.factories` 文件中声明 `BootstrapRegistryInitializer` 实现类。Spring Boot 会读取这些配置文件，并将声明的类实例化后添加到 `bootstrapRegistryInitializers` 列表中。

例如，在 `src/main/resources/META-INF/spring.factories` 文件中添加如下内容：

```
org.springframework.boot.BootstrapRegistryInitializer=com.example.MyBootstrapRegistryInitializer
```

这样，`MyBootstrapRegistryInitializer` 类就会被自动检测并添加到 `bootstrapRegistryInitializers` 列表中。

### 示例

假设我们有一个简单的 `MyBootstrapRegistryInitializer` 类：

```java
import org.springframework.boot.BootstrapRegistry;
import org.springframework.boot.BootstrapRegistryInitializer;

public class MyBootstrapRegistryInitializer implements BootstrapRegistryInitializer {

    @Override
    public void initialize(BootstrapRegistry registry) {
        // 在这里进行一些初始化操作
        registry.register(MyService.class, () -> new MyService());
    }
}
```

并且我们在 `spring.factories` 文件中声明了这个类：

```
org.springframework.boot.BootstrapRegistryInitializer=com.example.MyBootstrapRegistryInitializer
```

然后在主类中创建 `SpringApplication` 实例并运行：

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class MyApplication {

    public static void main(String[] args) {
        SpringApplication application = new SpringApplication(MyApplication.class);
        application.run(args);
    }
}
```

在这种情况下，`MyBootstrapRegistryInitializer` 会被自动检测并添加到 `bootstrapRegistryInitializers` 列表中，其 `initialize` 方法会在启动过程中被调用。

### 总结

`bootstrapRegistryInitializers` 列表的内容可以通过自动配置、显式注册和配置文件三种方式获取。这些初始化器在启动早期阶段进行一些自定义的初始化操作，确保应用能够顺利启动并进入运行状态。通过这些机制，开发者可以灵活地控制启动过程中的初始化逻辑。