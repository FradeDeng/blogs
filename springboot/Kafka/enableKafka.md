`@EnableKafka` 是 Spring Kafka 框架中的一个注解，用于启用 Kafka 相关的功能。它通常与 `@Configuration` 注解一起使用，以配置 Kafka 的消费者和生产者。下面详细介绍如何使用 `@EnableKafka` 注解以及一些常见的配置示例。

### 添加 Spring Kafka 依赖

首先，确保你在项目中添加了 Spring Kafka 依赖。如果你使用的是 Maven，可以在 `pom.xml` 文件中添加以下依赖：

```xml
<dependency>
    <groupId>org.springframework.kafka</groupId>
    <artifactId>spring-kafka</artifactId>
    <version>2.8.0</version> <!-- 请使用最新版本 -->
</dependency>
```

### 基本配置

#### 1. 创建 Kafka 配置类

使用 `@EnableKafka` 注解启用 Kafka 功能，并配置 Kafka 生产者和消费者的 Bean。

```java
import org.apache.kafka.clients.consumer.ConsumerConfig;
import org.apache.kafka.clients.producer.ProducerConfig;
import org.apache.kafka.common.serialization.StringDeserializer;
import org.apache.kafka.common.serialization.StringSerializer;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.kafka.annotation.EnableKafka;
import org.springframework.kafka.config.ConcurrentKafkaListenerContainerFactory;
import org.springframework.kafka.core.ConsumerFactory;
import org.springframework.kafka.core.DefaultKafkaConsumerFactory;
import org.springframework.kafka.core.DefaultKafkaProducerFactory;
import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.kafka.core.ProducerFactory;
import org.springframework.kafka.support.serializer.JsonDeserializer;
import org.springframework.kafka.support.serializer.JsonSerializer;

import java.util.HashMap;
import java.util.Map;

@Configuration
@EnableKafka
public class KafkaConfig {

    @Bean
    public ProducerFactory<String, String> producerFactory() {
        Map<String, Object> configProps = new HashMap<>();
        configProps.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
        configProps.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
        configProps.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
        return new DefaultKafkaProducerFactory<>(configProps);
    }

    @Bean
    public KafkaTemplate<String, String> kafkaTemplate() {
        return new KafkaTemplate<>(producerFactory());
    }

    @Bean
    public ConsumerFactory<String, String> consumerFactory() {
        Map<String, Object> configProps = new HashMap<>();
        configProps.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
        configProps.put(ConsumerConfig.GROUP_ID_CONFIG, "test-group");
        configProps.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
        configProps.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
        return new DefaultKafkaConsumerFactory<>(configProps);
    }

    @Bean
    public ConcurrentKafkaListenerContainerFactory<String, String> kafkaListenerContainerFactory() {
        ConcurrentKafkaListenerContainerFactory<String, String> factory = new ConcurrentKafkaListenerContainerFactory<>();
        factory.setConsumerFactory(consumerFactory());
        return factory;
    }
}
```

### 消费者配置

#### 1. 创建 Kafka 消费者

使用 `@KafkaListener` 注解来监听 Kafka 主题的消息。

```java
import org.springframework.kafka.annotation.KafkaListener;
import org.springframework.stereotype.Service;

@Service
public class KafkaConsumer {

    @KafkaListener(topics = "test-topic", groupId = "test-group")
    public void listen(String message) {
        System.out.println("Received Message: " + message);
    }
}
```

### 生产者配置

#### 1. 创建 Kafka 生产者

使用 `KafkaTemplate` 发送消息到 Kafka 主题。

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.stereotype.Service;

@Service
public class KafkaProducer {

    private final KafkaTemplate<String, String> kafkaTemplate;

    @Autowired
    public KafkaProducer(KafkaTemplate<String, String> kafkaTemplate) {
        this.kafkaTemplate = kafkaTemplate;
    }

    public void sendMessage(String message) {
        kafkaTemplate.send("test-topic", message);
    }
}
```

### 测试

#### 1. 创建测试类

创建一个测试类来发送和接收消息。

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.CommandLineRunner;
import org.springframework.stereotype.Component;

@Component
public class TestRunner implements CommandLineRunner {

    private final KafkaProducer kafkaProducer;

    @Autowired
    public TestRunner(KafkaProducer kafkaProducer) {
        this.kafkaProducer = kafkaProducer;
    }

    @Override
    public void run(String... args) throws Exception {
        kafkaProducer.sendMessage("Hello, Kafka!");
    }
}
```

### 总结

1. **添加依赖**：确保项目中添加了 Spring Kafka 依赖。
2. **创建配置类**：使用 `@EnableKafka` 注解启用 Kafka 功能，并配置 Kafka 生产者和消费者的 Bean。
3. **创建消费者**：使用 `@KafkaListener` 注解监听 Kafka 主题的消息。
4. **创建生产者**：使用 `KafkaTemplate` 发送消息到 Kafka 主题。
5. **测试**：创建一个测试类来发送和接收消息。

希望这些示例和解释对你有所帮助！如果你有任何其他问题，请随时提问。