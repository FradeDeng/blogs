`elasticsearch-rest-client` 是一个低级别的 REST 客户端，用于直接与 Elasticsearch 的 REST API 进行交互。它提供了基本的 HTTP 请求和响应处理功能，适用于需要细粒度控制的场景。下面是一个详细的使用示例，展示如何使用 `elasticsearch-rest-client` 进行常见的操作，如索引创建、文档索引、文档检索和文档删除。

### 1. 添加依赖

首先，你需要在项目的 `pom.xml` 文件中添加 `elasticsearch-rest-client` 的依赖。假设你使用的是 Maven：

```xml
<dependency>
    <groupId>org.elasticsearch.client</groupId>
    <artifactId>elasticsearch-rest-client</artifactId>
    <version>7.10.2</version> <!-- 请使用最新版本 -->
</dependency>
```

### 2. 创建客户端

创建一个 `RestClient` 实例来连接到 Elasticsearch 集群。

```java
import org.elasticsearch.client.RestClient;
import org.apache.http.HttpHost;

public class ElasticsearchClientExample {
    public static void main(String[] args) {
        // 创建 RestClient 实例
        RestClient restClient = RestClient.builder(
                new HttpHost("localhost", 9200, "http")).build();

        // 在 finally 块中关闭客户端
        try {
            // 执行操作
            createIndex(restClient);
            indexDocument(restClient);
            getDocument(restClient);
            deleteDocument(restClient);
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try {
                restClient.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

### 3. 创建索引

使用 `performRequest` 方法发送 HTTP 请求来创建索引。

```java
import org.elasticsearch.client.Response;
import org.elasticsearch.client.RestClient;
import org.apache.http.entity.ContentType;
import org.apache.http.entity.StringEntity;

public class ElasticsearchClientExample {
    public static void createIndex(RestClient restClient) throws Exception {
        String indexName = "my_index";
        String indexSettings = "{\n" +
                "  \"settings\": {\n" +
                "    \"number_of_shards\": 1,\n" +
                "    \"number_of_replicas\": 0\n" +
                "  }\n" +
                "}";

        Response response = restClient.performRequest(
                "PUT",
                "/" + indexName,
                Collections.emptyMap(),
                new StringEntity(indexSettings, ContentType.APPLICATION_JSON)
        );

        System.out.println("Create Index Response: " + response.getStatusLine());
    }
}
```

### 4. 索引文档

将文档索引到指定的索引中。

```java
public class ElasticsearchClientExample {
    public static void indexDocument(RestClient restClient) throws Exception {
        String indexName = "my_index";
        String document = "{\n" +
                "  \"title\": \"Elasticsearch Example\",\n" +
                "  \"content\": \"This is an example document.\"\n" +
                "}";

        Response response = restClient.performRequest(
                "POST",
                "/" + indexName + "/_doc",
                Collections.emptyMap(),
                new StringEntity(document, ContentType.APPLICATION_JSON)
        );

        System.out.println("Index Document Response: " + response.getStatusLine());
    }
}
```

### 5. 检索文档

通过文档的 ID 检索文档。

```java
public class ElasticsearchClientExample {
    public static void getDocument(RestClient restClient) throws Exception {
        String indexName = "my_index";
        String documentId = "1"; // 假设文档的 ID 是 1

        Response response = restClient.performRequest(
                "GET",
                "/" + indexName + "/_doc/" + documentId,
                Collections.emptyMap()
        );

        System.out.println("Get Document Response: " + response.getEntity().getContent());
    }
}
```

### 6. 删除文档

通过文档的 ID 删除文档。

```java
public class ElasticsearchClientExample {
    public static void deleteDocument(RestClient restClient) throws Exception {
        String indexName = "my_index";
        String documentId = "1"; // 假设文档的 ID 是 1

        Response response = restClient.performRequest(
                "DELETE",
                "/" + indexName + "/_doc/" + documentId,
                Collections.emptyMap()
        );

        System.out.println("Delete Document Response: " + response.getStatusLine());
    }
}
```

### 完整示例

将上述代码片段组合成一个完整的示例：

```java
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.Response;
import org.apache.http.HttpHost;
import org.apache.http.entity.ContentType;
import org.apache.http.entity.StringEntity;
import java.io.IOException;
import java.util.Collections;

public class ElasticsearchClientExample {
    public static void main(String[] args) {
        // 创建 RestClient 实例
        RestClient restClient = RestClient.builder(
                new HttpHost("localhost", 9200, "http")).build();

        // 在 finally 块中关闭客户端
        try {
            createIndex(restClient);
            indexDocument(restClient);
            getDocument(restClient);
            deleteDocument(restClient);
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try {
                restClient.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

    public static void createIndex(RestClient restClient) throws Exception {
        String indexName = "my_index";
        String indexSettings = "{\n" +
                "  \"settings\": {\n" +
                "    \"number_of_shards\": 1,\n" +
                "    \"number_of_replicas\": 0\n" +
                "  }\n" +
                "}";

        Response response = restClient.performRequest(
                "PUT",
                "/" + indexName,
                Collections.emptyMap(),
                new StringEntity(indexSettings, ContentType.APPLICATION_JSON)
        );

        System.out.println("Create Index Response: " + response.getStatusLine());
    }

    public static void indexDocument(RestClient restClient) throws Exception {
        String indexName = "my_index";
        String document = "{\n" +
                "  \"title\": \"Elasticsearch Example\",\n" +
                "  \"content\": \"This is an example document.\"\n" +
                "}";

        Response response = restClient.performRequest(
                "POST",
                "/" + indexName + "/_doc",
                Collections.emptyMap(),
                new StringEntity(document, ContentType.APPLICATION_JSON)
        );

        System.out.println("Index Document Response: " + response.getStatusLine());
    }

    public static void getDocument(RestClient restClient) throws Exception {
        String indexName = "my_index";
        String documentId = "1"; // 假设文档的 ID 是 1

        Response response = restClient.performRequest(
                "GET",
                "/" + indexName + "/_doc/" + documentId,
                Collections.emptyMap()
        );

        System.out.println("Get Document Response: " + response.getEntity().getContent());
    }

    public static void deleteDocument(RestClient restClient) throws Exception {
        String indexName = "my_index";
        String documentId = "1"; // 假设文档的 ID 是 1

        Response response = restClient.performRequest(
                "DELETE",
                "/" + indexName + "/_doc/" + documentId,
                Collections.emptyMap()
        );

        System.out.println("Delete Document Response: " + response.getStatusLine());
    }
}
```

### 总结

通过 `elasticsearch-rest-client`，你可以直接与 Elasticsearch 的 REST API 进行交互，执行各种操作。虽然它提供的抽象层次较低，但提供了极大的灵活性和控制能力。希望这些示例对你理解和使用 `elasticsearch-rest-client` 有所帮助！如果有任何其他问题，请随时提问。