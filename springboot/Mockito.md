
Mockito is a powerful Java mocking framework that is widely used for unit testing. It allows you to create and configure mock objects, enabling you to isolate the code under test and verify interactions with these objects. Below is a detailed explanation of some key concepts of Mockito and common usage scenarios, along with example programs.

Certainly! Here is the detailed explanation of Mockito concepts and examples in English.

### Key Concepts

1. **Mock**: Creating a mock object that can replace a real object. The behavior of the mock object can be configured as needed.
2. **Stub**: Define the behavior of a mock object's methods. For example, specify a method to return a specific value when called.
3. **Spy**: Create a partial mock object that retains the behavior of the real object while allowing some methods to be mocked.
4. **Verification**: Verify that methods on the mock object were called, and you can also verify the number of times they were called and the arguments passed.
5. **ArgumentCaptor**: Capture the arguments passed to a mock object's methods for further verification.

### Setting Up Dependencies

Add Mockito dependencies in your `pom.xml` file:

```xml
<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-core</artifactId>
    <version>4.0.0</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <version>2.5.6</version>
    <scope>test</scope>
</dependency>
```

### Example Programs

#### 1. Basic Mock and Stub

```java
import org.junit.jupiter.api.Test;
import org.mockito.Mockito;

import static org.mockito.Mockito.*;

public class BasicMockTest {

    public static class UserService {
        public String getUser(String userId) {
            // Actual implementation
            return "Real User";
        }
    }

    @Test
    public void testMockAndStub() {
        // Create a mock object
        UserService mockUserService = Mockito.mock(UserService.class);

        // Define behavior
        when(mockUserService.getUser("123")).thenReturn("Mock User");

        // Call methods and verify behavior
        System.out.println(mockUserService.getUser("123")); // Outputs: Mock User
        System.out.println(mockUserService.getUser("456")); // Outputs: null
    }
}
```

#### 2. Using Spy

```java
import org.junit.jupiter.api.Test;
import org.mockito.Mockito;

import static org.mockito.Mockito.*;

public class SpyTest {

    public static class UserService {
        public String getUser(String userId) {
            // Actual implementation
            return "Real User";
        }
    }

    @Test
    public void testSpy() {
        // Create a spy object
        UserService realUserService = new UserService();
        UserService spyUserService = Mockito.spy(realUserService);

        // Define partial behavior
        when(spyUserService.getUser("123")).thenReturn("Spy User");

        // Call methods and verify behavior
        System.out.println(spyUserService.getUser("123")); // Outputs: Spy User
        System.out.println(spyUserService.getUser("456")); // Outputs: Real User
    }
}
```

#### 3. Verifying Method Calls

```java
import org.junit.jupiter.api.Test;
import org.mockito.Mockito;

import static org.mockito.Mockito.*;

public class VerifyTest {

    public static class UserService {
        public void addUser(String userId, String userName) {
            // Actual implementation
        }
    }

    @Test
    public void testVerify() {
        // Create a mock object
        UserService mockUserService = Mockito.mock(UserService.class);

        // Call the method
        mockUserService.addUser("123", "John");

        // Verify the method was called
        verify(mockUserService).addUser("123", "John");
    }
}
```

#### 4. Using ArgumentMatchers

```java
import org.junit.jupiter.api.Test;
import org.mockito.Mockito;

import static org.mockito.Mockito.*;
import static org.mockito.ArgumentMatchers.*;

public class ArgumentMatchersTest {

    public static class UserService {
        public String getUser(String userId) {
            // Actual implementation
            return "Real User";
        }
    }

    @Test
    public void testArgumentMatchers() {
        // Create a mock object
        UserService mockUserService = Mockito.mock(UserService.class);

        // Define behavior
        when(mockUserService.getUser(anyString())).thenReturn("Mock User");

        // Call methods and verify behavior
        System.out.println(mockUserService.getUser("123")); // Outputs: Mock User
        System.out.println(mockUserService.getUser("456")); // Outputs: Mock User
    }
}
```

#### 5. Capturing Argument Values

```java
import org.junit.jupiter.api.Test;
import org.mockito.ArgumentCaptor;
import org.mockito.Mockito;

import static org.mockito.Mockito.*;

public class ArgumentCaptorTest {

    public static class UserService {
        public void addUser(String userId, String userName) {
            // Actual implementation
        }
    }

    @Test
    public void testArgumentCaptor() {
        // Create a mock object
        UserService mockUserService = Mockito.mock(UserService.class);

        // Call the method
        mockUserService.addUser("123", "John");

        // Capture argument values
        ArgumentCaptor<String> userIdCaptor = ArgumentCaptor.forClass(String.class);
        ArgumentCaptor<String> userNameCaptor = ArgumentCaptor.forClass(String.class);
        verify(mockUserService).addUser(userIdCaptor.capture(), userNameCaptor.capture());

        // Verify argument values
        System.out.println(userIdCaptor.getValue()); // Outputs: 123
        System.out.println(userNameCaptor.getValue()); // Outputs: John
    }
}
```

#### 6. Using `@InjectMocks` and `@Mock`

```java
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

import static org.mockito.Mockito.*;

public class InjectMocksTest {

    public static class UserRepository {
        public String findUserById(String userId) {
            // Actual implementation
            return "Real User";
        }
    }

    public static class UserService {
        private UserRepository userRepository;

        public UserService(UserRepository userRepository) {
            this.userRepository = userRepository;
        }

        public String getUser(String userId) {
            return userRepository.findUserById(userId);
        }
    }

    @InjectMocks
    private UserService userService;

    @Mock
    private UserRepository userRepository;

    @BeforeEach
    public void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    public void testInjectMocks() {
        when(userRepository.findUserById("123")).thenReturn("Mock User");

        System.out.println(userService.getUser("123")); // Outputs: Mock User

        verify(userRepository).findUserById("123");
    }
}
```

### Explanation

1. **Mock and Stub**: Use the `mock()` method to create a mock object and the `when().thenReturn()` method to define its behavior.
2. **Spy**: Use the `spy()` method to create a partial mock object that can have some methods mocked while retaining the behavior of the real object.
3. **Verifying Method Calls**: Use the `verify()` method to check if methods on the mock object were called.
4. **ArgumentMatchers**: Use methods like `anyString()` to match any argument of a specified type.
5. **Capturing Argument Values**: Use the `ArgumentCaptor` class to capture the arguments passed to a mock object's method.
6. **`@InjectMocks` and `@Mock`**: Use annotations to simplify dependency injection and the creation of mock objects.

By following these detailed examples, you can gain a solid understanding of the basic concepts and common use cases of Mockito, enabling you to mock object behavior and verify interactions more effectively in your unit tests.

***

Mockito 是一个强大的 Java mocking 框架，广泛用于单元测试。它允许您创建和配置模拟对象，从而使您可以隔离测试代码，并验证与这些对象的交互。以下是 Mockito 的一些关键概念和常见使用场景的详细解释及示例程序。

### 关键概念

1. **Mock（模拟）**：创建一个模拟对象，它可以替代真实对象。模拟对象的行为可以根据需要进行配置。
2. **Stub（存根）**：定义模拟对象的方法行为。例如，指定一个方法在被调用时返回特定的值。
3. **Spy（间谍）**：创建一个部分模拟对象，保留真实对象的行为，同时可以对其部分方法进行模拟。
4. **Verification（验证）**：验证模拟对象的方法是否被调用，并且可以验证调用的次数和参数。
5. **ArgumentCaptor（参数捕获器）**：捕获传递给模拟对象的方法参数，以便进行进一步的验证。

### 设置依赖

在 `pom.xml` 文件中添加 Mockito 依赖：

```xml
<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-core</artifactId>
    <version>4.0.0</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <version>2.5.6</version>
    <scope>test</scope>
</dependency>
```

### 示例程序

#### 1. 基本 Mock 和 Stub

```java
import org.junit.jupiter.api.Test;
import org.mockito.Mockito;

import static org.mockito.Mockito.*;

public class BasicMockTest {

    public static class UserService {
        public String getUser(String userId) {
            // 实际实现
            return "Real User";
        }
    }

    @Test
    public void testMockAndStub() {
        // 创建 Mock 对象
        UserService mockUserService = Mockito.mock(UserService.class);

        // 定义行为
        when(mockUserService.getUser("123")).thenReturn("Mock User");

        // 调用方法并验证行为
        System.out.println(mockUserService.getUser("123")); // 输出: Mock User
        System.out.println(mockUserService.getUser("456")); // 输出: null
    }
}
```

#### 2. 使用 Spy

```java
import org.junit.jupiter.api.Test;
import org.mockito.Mockito;

import static org.mockito.Mockito.*;

public class SpyTest {

    public static class UserService {
        public String getUser(String userId) {
            // 实际实现
            return "Real User";
        }
    }

    @Test
    public void testSpy() {
        // 创建 Spy 对象
        UserService realUserService = new UserService();
        UserService spyUserService = Mockito.spy(realUserService);

        // 定义部分行为
        when(spyUserService.getUser("123")).thenReturn("Spy User");

        // 调用方法并验证行为
        System.out.println(spyUserService.getUser("123")); // 输出: Spy User
        System.out.println(spyUserService.getUser("456")); // 输出: Real User
    }
}
```

#### 3. 验证方法调用

```java
import org.junit.jupiter.api.Test;
import org.mockito.Mockito;

import static org.mockito.Mockito.*;

public class VerifyTest {

    public static class UserService {
        public void addUser(String userId, String userName) {
            // 实际实现
        }
    }

    @Test
    public void testVerify() {
        // 创建 Mock 对象
        UserService mockUserService = Mockito.mock(UserService.class);

        // 调用方法
        mockUserService.addUser("123", "John");

        // 验证方法是否被调用
        verify(mockUserService).addUser("123", "John");
    }
}
```

#### 4. 使用 ArgumentMatchers

```java
import org.junit.jupiter.api.Test;
import org.mockito.Mockito;

import static org.mockito.Mockito.*;
import static org.mockito.ArgumentMatchers.*;

public class ArgumentMatchersTest {

    public static class UserService {
        public String getUser(String userId) {
            // 实际实现
            return "Real User";
        }
    }

    @Test
    public void testArgumentMatchers() {
        // 创建 Mock 对象
        UserService mockUserService = Mockito.mock(UserService.class);

        // 定义行为
        when(mockUserService.getUser(anyString())).thenReturn("Mock User");

        // 调用方法并验证行为
        System.out.println(mockUserService.getUser("123")); // 输出: Mock User
        System.out.println(mockUserService.getUser("456")); // 输出: Mock User
    }
}
```

#### 5. 捕获参数值

```java
import org.junit.jupiter.api.Test;
import org.mockito.ArgumentCaptor;
import org.mockito.Mockito;

import static org.mockito.Mockito.*;

public class ArgumentCaptorTest {

    public static class UserService {
        public void addUser(String userId, String userName) {
            // 实际实现
        }
    }

    @Test
    public void testArgumentCaptor() {
        // 创建 Mock 对象
        UserService mockUserService = Mockito.mock(UserService.class);

        // 调用方法
        mockUserService.addUser("123", "John");

        // 捕获参数值
        ArgumentCaptor<String> userIdCaptor = ArgumentCaptor.forClass(String.class);
        ArgumentCaptor<String> userNameCaptor = ArgumentCaptor.forClass(String.class);
        verify(mockUserService).addUser(userIdCaptor.capture(), userNameCaptor.capture());

        // 验证参数值
        System.out.println(userIdCaptor.getValue()); // 输出: 123
        System.out.println(userNameCaptor.getValue()); // 输出: John
    }
}
```

#### 6. 使用 `@InjectMocks` 和 `@Mock`

```java
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

import static org.mockito.Mockito.*;

public class InjectMocksTest {

    public static class UserRepository {
        public String findUserById(String userId) {
            // 实际实现
            return "Real User";
        }
    }

    public static class UserService {
        private UserRepository userRepository;

        public UserService(UserRepository userRepository) {
            this.userRepository = userRepository;
        }

        public String getUser(String userId) {
            return userRepository.findUserById(userId);
        }
    }

    @InjectMocks
    private UserService userService;

    @Mock
    private UserRepository userRepository;

    @BeforeEach
    public void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    public void testInjectMocks() {
        when(userRepository.findUserById("123")).thenReturn("Mock User");

        System.out.println(userService.getUser("123")); // 输出: Mock User

        verify(userRepository).findUserById("123");
    }
}
```

### 解释

1. **Mock和Stub**：使用 `mock()` 方法创建一个模拟对象，并使用 `when().thenReturn()` 定义其行为。
2. **Spy**：使用 `spy()` 方法创建一个部分模拟对象，可以对其部分方法进行模拟。
3. **验证方法调用**：使用 `verify()` 方法验证模拟对象的方法是否被调用。
4. **ArgumentMatchers**：使用 `anyString()` 等方法定义任意参数匹配。
5. **捕获参数值**：使用 `ArgumentCaptor` 捕获传递给模拟对象的方法参数。
6. **`@InjectMocks` 和 `@Mock`**：使用注解简化依赖注入和模拟对象的创建。

通过这些详细的示例，您可以掌握 Mockito 的基本概念和常见使用场景，从而在单元测试中更加灵活地模拟对象行为并验证交互。