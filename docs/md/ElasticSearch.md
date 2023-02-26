# ElasticSearch

## 1 ElasticSearch 简介

### 1.1 ES 简介

ElasticSearch 是一个分布式、Restful 风格的搜索和数据分析引擎，能够解决不断涌现出的各种用力。作为 Elastic Stack 的核心，它集中存储您的数据，帮助您发现意料之中以及意料之外的情况。

The Elastic Stack，包括 ElasticSearch、Kibana、Beats 和 Logstash（也称为 ELK Stack）能够安全可靠地获取任何来源、任何格式的数据，然后实时地对数据进行搜索、分析和可视化。 ElasticSearch 简称为 ES，ES 是一个开源的高扩展的分布式全文搜索引擎，是整个 Elastic Stack 技术栈的核心。它可以近乎实时的存储、检索数据：本身扩展性很好，可以扩展到上百台服务器，处理 PB 级别的数据。

### 1.2 ES 安装

进入到官网：https://www.elastic.co/cn/，这里下载 7.8.0 版本的 ES

下载windows版本，并解压缩

![image-20230221155118696](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302211551851.png)

上图 可以看到 jdk 包，推荐用 自带的 JDK，如果用自己的 JDK 可能会报错。

bin 包下，双击  elasticsearch.bat，即可 启动 ES。

注意：9300 端口为 ElasticSearch 集群件组件的通信端口，9200 端口为浏览器访问的 http 协议 Restful 端口。

访问 http://localhost:9200/

![image-20230221155944162](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302211559201.png)

如果双击闪退

双击启动 elasticsearch.bat，启动窗口闪退，通过路径访问追踪 错误，如果是“空间不足”，请修改 /config/jvm.options 配置文件。

```
# 设置 JVM 初始内存为 1G。此值可以设置与 -Xmx 相同，以避免每次垃圾回收完成后 JVM 重新分配内存
# Xms represents the initial size of total heap space
# 设置 JVM 最大可用内存为 1G
# Xmx represents the maximum size of total heap space

-Xms1g
-Xmx1g
```

## 2 ElasticSearch 入门（HTTP）

HTTP 这里是指 用 Postman 调用请求的方式，操作 ES。

### 2.1 数据格式

ElasticSearch 是面向文档型数据库，一条数据在这里就是一个文档。

![image-20230221161152043](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302211611095.png)

ES 里的 Index 可以看做一个 数据库，而 Types 相当于表，Documents 则相当于表的行。这里 Type 的概念已经被逐渐弱化，ElasticSearch 6.X 中，一个 Index 下已经只能包含一个 Type；ElasticSearch 7.X 中，Types 的概念已经被删除了。

 倒排索引

| id   | content              |
| ---- | -------------------- |
| 1001 | my name is zhang san |
| 1002 | my name is li si     |

正排（正向）索引：在上表数据中 根据 id 查询对应的 content，（通过 key 找 vlue）。

倒排索引：根据 keyword 查找 对应的 id，（通过 value 找 key）。

### 2.2 索引操作

#### 2.2.1 创建索引

Postman中，向 ES 服务器（http://localhost:9200）发送 PUT 请求：

（PUT）http://localhost:9200/shopping，创建 shopping 索引。

![image-20230221163047352](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302211630408.png)

再次重复创建

![image-20230221163130710](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302211631771.png)

post操作也是不允许的

![image-20230221163349302](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302211633352.png)



#### 2.2.2 查询索引

查询一个索引

（GET）http://localhost:9200/shopping，查询 shopping 索引。

![image-20230221163445280](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302211634333.png)

查询全部索引

（GET）http://localhost:9200/_cat/indices?v，查询 全部索引。

![image-20230221163542545](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302211635586.png)



#### 2.2.3 删除索引

（DELETE）http://localhost:9200/shopping，删除 shopping 索引。

![image-20230221163630863](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302211636902.png)



### 2.3 文档操作（Documents）

文档 Documents 可以类比为 Mysql 中的 表数据。

#### 2.3.1 创建文档

Postman中，向 ES 服务器发送 POST 请求，

（PUT）http://localhost:9200/shopping，首先创建 shopping 索引，然后在 shopping 索引中 创建文档。

##### 随机 ID

（POST）http://localhost:9200/shopping/_doc，在 shopping 索引中 创建文档。

![image-20230221193712709](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302211937803.png)

![image-20230221193742491](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302211937529.png)

```
{
    "title":"小米手机",
    "category":"小米",
    "images":"https://cdn.cnbj0.fds.api.mi-img.com/b2c-shopapi-pms/pms_1650439833.58927055.png",
    "price":3999.00
}
```

 创建完成

![image-20230221193822078](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302211938132.png)

如果再点击一次SEND，再次创建。ID改变

![image-20230221194444652](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302211944713.png)



##### 自定义 ID

（POST）http://localhost:9200/shopping/_doc/1001，在 shopping 索引中 创建文档。

![image-20230221194600558](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302211946619.png)

PUT也可以

![image-20230221195408631](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302211954695.png)



##### _create 创建 自定义 ID

_create 也是可以创建的 自定义 ID的，但是没法创建 随机 ID。

（POST）http://localhost:9200/shopping/_create/1003，在 shopping 索引中 创建文档。

![image-20230221195446990](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302211954048.png)





#### 2.3.2 查询文档

##### 查询 ID 数据

（GET）http://localhost:9200/shopping/_doc/1001，在 shopping 索引中 查询 id 是 1001 的文档。

![image-20230221195550899](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302211955950.png)

![image-20230221195815667](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302211958704.png)

##### 查询全部文档

（GET）http://localhost:9200/shopping/_search，在 shopping 索引中 查询全部文档。

![image-20230221200057398](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302212000453.png)

##### 条件查询

（GET）http://localhost:9200/shopping/_search?q=category:小米，在 shopping 索引中 查询

![image-20230221200225625](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302212002678.png)

##### Get 请求体中 条件查询

（GET 请求体）http://localhost:9200/shopping/_search，在 shopping 索引中 查询

```
{
    "query": {
        "match": {
            "category": "小米"
        }
    }
}
```

![image-20230221200329222](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302212003283.png)

##### 分页查询

（GET 请求体）http://localhost:9200/shopping/_search，在 shopping 索引中 查询

```
{
    "query": {
        "match_all": {
   
        }
    },
    "from": 0,
    "size": 2
}
```

![image-20230222190355025](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302221904127.png)

查到2个数据

![image-20230222190437988](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302221904052.png)

设定参数，只查询标题

```json
{
    "query": {
        "match_all": {

        }
    },
    "from": 0,
    "size": 2,
    "_source": ["title"]

}
```

![image-20230222190554630](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302221905698.png)

##### 查询排序

（GET 请求体）http://localhost:9200/shopping/_search，在 shopping 索引中 查询

```json
{
    "query": {
        "match_all": {
   
        }
    },
    "from": 0,
    "size": 2,
    "_source": ["title"],
    "sort": {
        "price": {
            "order": "desc"
        }
    }
}
```

![image-20230222190705897](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302221907955.png)

##### 多条件查询

（GET 请求体）http://localhost:9200/shopping/_search，在 shopping 索引中 查询

bool 开启多条件查询，must 是 and，should 是 or

```json
{
    "query": {
        "bool": {
            "must": [
                {
                    "match": {
                        "category": "小米"
                    }
                },
                {
                    "match": {
                        "price": 3999
                    }
                }
            ]
        }
    }
}
```

![image-20230222190906390](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302221909453.png)

请求体should

```json
{
    "query": {
        "bool": {
            "should": [
                {
                    "match": {
                        "category": "小米"
                    }
                },
                {
                    "match": {
                        "category": "华为"
                    }
                }
            ]
        }
    }
}
```

![image-20230222191021836](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302221910896.png)

##### 范围查询

（GET 请求体）http://localhost:9200/shopping/_search，在 shopping 索引中 查询

gt：>，

```
{
    "query": {
        "bool": {
            "should": [
                {
                    "match": {
                        "category": "小米"
                    }
                },
                {
                    "match": {
                        "category": "华为"
                    }
                }
            ],
            "filter": {
                "range": {
                    "price": {
                        "gt": 3998
                    }
                }
            }
        }
    }
}
```

![image-20230222191115083](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302221911141.png)

##### 全文检索

（GET 请求体）http://localhost:9200/shopping/_search，在 shopping 索引中 查询

match：会拆分的模糊查询

match_phrase：不会拆分，只作为一个整体的模糊查询

```json
{
    "query": {
        "match": {
            "category": "小米"
        }
    }
}
```

![image-20230222191218474](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302221912540.png)

```
{
    "query": {
        "match": {
            "category": "米"
        }
    }
}
```

![image-20230222191255199](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302221912261.png)

改成“小华”

```
{
    "query": {
        "match": {
            "category": "小华" // match 拆分成 小 和 华，进行模糊查询
        }
    }
}
```

![image-20230222191534285](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302221915343.png)

##### 完全匹配

（GET 请求体）http://localhost:9200/shopping/_search，在 shopping 索引中 查询

match：会拆分的模糊查询

match_phrase：不会拆分，只作为一个整体的模糊查询

```
{
    "query": {
        "match_phrase": {
            "category": "小华" // match_phrase 不会拆分，作为整体模糊查询
        }
    }
}
```

![image-20230222191624440](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302221916498.png)

##### 高亮查询

（GET 请求体）http://localhost:9200/shopping/_search，在 shopping 索引中 查询

```
{
    "query": {
        "match_phrase": {
            "category": "小米"
        }
    },
    "highlight": {
        "fields": {
            "category": {}
        }
    }
}
```

![image-20230222191718570](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302221917627.png)

##### 聚合查询

（GET 请求体）http://localhost:9200/shopping/_search，在 shopping 索引中 查询

aggs：聚合操作，相当于 Mysql 中的 group by

terms：count，计数

avg：average，平均

###### terms

```
{
    "aggs": { // 聚合操作
        "price_group": { // 名称，随意起
            "terms": { // 分组
                "field": "price" // 分组字段
            }
        }
    }
}
```

![image-20230222191755981](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302221917042.png)

###### size：0

```
{
    "aggs": { // 聚合操作
        "price_group": { // 名称，随意起
            "terms": { // 分组
                "field": "price" // 分组字段
            }
        }
    },
    "size": 0 // hits 里面的数据没有了
}
```

![image-20230222191830626](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302221918672.png)

hits 里面的数据没有了

###### avg

```
{
    "aggs": { // 聚合操作
        "price_group": { // 名称，随意起
            "avg": { // 平均
                "field": "price" // 分组字段
            }
        }
    },
    "size": 0 // hits 里面的数据没有了
}
```

![image-20230222191953948](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302221919017.png)

#### 2.3.3 修改文档

##### 全量修改

全量修改：就是输入的 请求体 是全部。

（PUT）http://localhost:9200/shopping/_doc/1001，修改 1001 文档

```
{
    "title":"小米手机",
    "category":"小米",
    "images":"https://cdn.cnbj0.fds.api.mi-img.com/b2c-shopapi-pms/pms_1650439833.58927055.png",
    "price":4999.00
}
```

![image-20230222192145058](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302221921114.png)

（PUT）http://localhost:9200/shopping/_doc/1001，修改 1001 的价格 3999 -> 4999



##### 局部修改

只输入 修改的属性即可。

（POST）http://localhost:9200/shopping/_update/1001，修改 1001 文档

```
{
    "doc":{
        "title":"华为手机"
    }
}
```

![image-20230222192412682](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302221924751.png)

#### 2.3.4 删除文档

（DELETE）http://localhost:9200/shopping/_doc/1001，删除 1001 文档

![image-20230222194325559](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302221943609.png)

查询失败

![image-20230222194354911](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302221943974.png)



### 2.4 映射关系

type：

- text：支持分词模糊
- keyword：要完整的

index：

- true：允许查询
- false：不允许查询



#### 2.4.1 创建索引

（PUT 请求体）http://localhost:9200/user/，创建 user 索引。

![image-20230222194627332](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302221946386.png)



#### 2.4.2 创建映射

（PUT 请求体）http://localhost:9200/user/_mapping，在 user 索引 创建 映射关系。

多了 /_mapping

```
{
    "properties": {
        "name": {
            "type": "text",
            "index": true
        },
        "sex": {
            "type": "keyword",
            "index": true
        },
        "tel": {
            "type": "keyword",
            "index": false
        }
    }
}
```

![image-20230222194929421](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302221949479.png)

创建数据

（PUT）http://localhost:9200/user/_create/1001

```
{
    "name":"小张",
    "sex":"男",
    "tel":"19208461245"
}
```

![image-20230222200026730](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302222000771.png)

![image-20230222200048968](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302222000011.png)

查询

![image-20230222200259075](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302222002134.png)

keyword必须完全匹配

![image-20230222200350550](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302222003606.png)

![image-20230222200408275](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302222004343.png)



#### 2.4.3 查询索引映射

（GET）http://localhost:9200/user/_mapping，查询 user 索引映射。

![image-20230222195018886](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302221950957.png)



## 3.ElasticSearch 入门（JavaAPI）

JavaAPI 这里是指 用 Java 操作 ES。

### 3.1 项目搭建

```
    <dependencies>

        <!-- elasticsearch -->
        <dependency>
            <groupId>org.elasticsearch</groupId>
            <artifactId>elasticsearch</artifactId>
            <version>7.8.0</version>
        </dependency>

        <!-- elasticsearch 客户端 -->
        <dependency>
            <groupId>org.elasticsearch.client</groupId>
            <artifactId>elasticsearch-rest-high-level-client</artifactId>
            <version>7.8.0</version>
        </dependency>

        <!-- elasticsearch 依赖的 2.x 的log4j -->
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-api</artifactId>
            <version>2.8.2</version>
        </dependency>
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-core</artifactId>
            <version>2.8.2</version>
        </dependency>

        <!-- junit 单元测试 -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>

        <!-- json -->
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.9.9</version>
        </dependency>

    </dependencies>
```

代码测试

```
import org.apache.http.HttpHost;
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.RestHighLevelClient;

public class ESTestClient {

    public static void main(String[] args) throws Exception {

        // 创建 ES 客户端
        RestHighLevelClient client = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );

        // 关闭 ES 客户端
        client.close();
    }
}
```



### 3.2 索引操作

#### 3.2.1 创建索引

```
package com.jiang.es.index;

import org.apache.http.HttpHost;
import org.elasticsearch.action.admin.indices.create.CreateIndexRequest;
import org.elasticsearch.action.admin.indices.create.CreateIndexResponse;
import org.elasticsearch.client.RequestOptions;
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.RestHighLevelClient;

import java.io.IOException;

/**
 * [一句话描述该类的功能]
 *
 * @author : jiangbx
 * @version : [v1.0]
 * @createTime : [2023/2/23 14:23]
 */
public class CreateIndex {
    public static void main(String[] args) throws IOException {
        // 创建客户端对象
        RestHighLevelClient client = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http")));

        // 创建索引 - 请求对象
        CreateIndexRequest request = new CreateIndexRequest("menu");
        // 发送请求，获取响应
        CreateIndexResponse response = client.indices()
                .create(request, RequestOptions.DEFAULT);
        boolean acknowledged = response.isAcknowledged();
        // 响应状态
        System.out.println("索引创建 =  " + acknowledged);

        // 关闭客户端连接
        client.close();
    }
}

```

测试查询

```
http://localhost:9200/_cat/indices?v
```

![image-20230223153314354](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302231533528.png)



#### 3.2.2 查询索引

```
package com.jiang.es.index;

import org.apache.http.HttpHost;
import org.elasticsearch.client.RequestOptions;
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.RestHighLevelClient;
import org.elasticsearch.client.indices.GetIndexRequest;
import org.elasticsearch.client.indices.GetIndexResponse;

import java.io.IOException;

/**
 * [查询索引]
 *
 * @author : jiangbx
 * @version : [v1.0]
 * @createTime : [2023/2/23 15:34]
 */
public class SearchIndex {
    public static void main(String[] args) throws IOException {
        // 创建客户端对象
        RestHighLevelClient client = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http")));

        // 查询索引 - 请求对象
        GetIndexRequest request = new GetIndexRequest("menu");
        // 发送请求，获取响应
        GetIndexResponse response = client.indices().get(request, RequestOptions.DEFAULT);
        System.out.println("aliases:" + response.getAliases());
        System.out.println("mappings:" + response.getMappings());
        System.out.println("settings:" + response.getSettings());

        client.close();
    }
}

```

![image-20230223153547721](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302231535804.png)





#### 3.2.3 删除索引

```
package com.jiang.es.index;

import org.apache.http.HttpHost;
import org.elasticsearch.action.admin.indices.delete.DeleteIndexRequest;
import org.elasticsearch.action.support.master.AcknowledgedResponse;
import org.elasticsearch.client.RequestOptions;
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.RestHighLevelClient;

import java.io.IOException;
/**
 * [删除索引]
 *
 * @author : jiangbx
 * @version : [v1.0]
 * @createTime : [2023/2/23 15:37]
 */
public class DeleteIndex {
    public static void main(String[] args) throws IOException {
        RestHighLevelClient client = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http")));
        // 删除索引 - 请求对象
        DeleteIndexRequest request = new DeleteIndexRequest("menu");
        // 发送请求，获取响应
        AcknowledgedResponse response = client.indices().delete(request, RequestOptions.DEFAULT);
        // 操作结果
        System.out.println("操作结果：" + response.isAcknowledged());
        client.close();
    }
}

```

![image-20230223153826121](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302231538200.png)



### 3.3 文档操作

```
package com.jiang.es.model;

/**
 * [实体类]
 *
 * @author : jiangbx
 * @version : [v1.0]
 * @createTime : [2023/2/23 15:39]
 */
public class User {
    private String name;
    private String sex;
    private Integer age;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getSex() {
        return sex;
    }

    public void setSex(String sex) {
        this.sex = sex;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }
}

```



#### 3.3.1 创建文档

##### 单独创建

```
package com.jiang.es.doc;

import com.fasterxml.jackson.databind.ObjectMapper;
import com.jiang.es.model.User;
import com.jiang.es.utils.ConnectElasticsearch;
import org.apache.http.HttpHost;
import org.elasticsearch.action.index.IndexRequest;
import org.elasticsearch.action.index.IndexResponse;
import org.elasticsearch.client.RequestOptions;
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.RestHighLevelClient;
import org.elasticsearch.common.xcontent.XContentType;
/**
 * [单独创建文档]
 *
 * @author : jiangbx
 * @version : [v1.0]
 * @createTime : [2023/2/23 15:41]
 */
public class InsertDoc {
    public static void main(String[] args) throws Exception{
        // 创建 ES 客户端
        RestHighLevelClient client = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );

        // 插入数据
        IndexRequest request = new IndexRequest();
        request.index("users").id("1001");
        User user = new User("zhangsan", "男", 30);

        // 向 ES 插入数据，必须将数据转换为 JSON 格式
        ObjectMapper mapper = new ObjectMapper();
        String userJson = mapper.writeValueAsString(user);
        request.source(userJson, XContentType.JSON);
        IndexResponse response = client.index(request, RequestOptions.DEFAULT);

        System.err.println("response.getResult(): " + response.getResult());
        System.err.println("response = " + response); // TODO==delete

        // 关闭 ES 客户端
        client.close();
    }
}

```

![image-20230223160521086](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302231605177.png)

```
http://localhost:9200/users/_doc/1001
```

![image-20230223160644347](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302231606419.png)

##### 批量创建

```
package com.jiang.es.doc;

import org.apache.http.HttpHost;
import org.elasticsearch.action.bulk.BulkRequest;
import org.elasticsearch.action.bulk.BulkResponse;
import org.elasticsearch.action.index.IndexRequest;
import org.elasticsearch.client.RequestOptions;
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.RestHighLevelClient;
import org.elasticsearch.common.xcontent.XContentType;
/**
 * [批量创建文档]
 *
 * @author : jiangbx
 * @version : [v1.0]
 * @createTime : [2023/2/23 16:08]
 */
public class BatchInsertDoc {
    public static void main(String[] args) throws Exception {

        // 创建 ES 客户端
        RestHighLevelClient client = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );

        // 批量插入数据
        BulkRequest request = new BulkRequest();

        request.add(new IndexRequest().index("users").id("1001").source(XContentType.JSON, "name", "zhangsan"));
        request.add(new IndexRequest().index("users").id("1002").source(XContentType.JSON, "name", "lisi"));
        request.add(new IndexRequest().index("users").id("1003").source(XContentType.JSON, "name", "wangwu"));

        BulkResponse response = client.bulk(request, RequestOptions.DEFAULT);

        System.err.println("response.getTook() = " + response.getTook());
        System.err.println("response.getItems() = " + response.getItems());
        System.err.println("response = " + response);

        // 关闭 ES 客户端
        client.close();
    }
}

```

![image-20230223160912075](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302231609160.png)

```
（GET）http://localhost:9200/users/_search
```

![image-20230223162011594](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302231620668.png)



#### 3.3.2 修改文档

```
package com.jiang.es.doc;


import com.jiang.es.utils.ConnectElasticsearch;
import org.elasticsearch.action.update.UpdateRequest;
import org.elasticsearch.action.update.UpdateResponse;
import org.elasticsearch.client.RequestOptions;
import org.elasticsearch.common.xcontent.XContentType;
/**
 * [修改文档]
 *
 * @author : jiangbx
 * @version : [v1.0]
 * @createTime : [2023/2/23 16:20]
 */
public class UpdateDoc {
    public static void main(String[] args) {
        ConnectElasticsearch.connect(client -> {
            // 修改文档 - 请求对象
            UpdateRequest request = new UpdateRequest();
            // 配置修改参数
            request.index("users").id("1001");
            // 设置请求体，对数据进行修改
            request.doc(XContentType.JSON, "sex", "女");
            // 客户端发送请求，获取响应对象
            UpdateResponse response = client.update(request, RequestOptions.DEFAULT);
            System.out.println("_index:" + response.getIndex());
            System.out.println("_id:" + response.getId());
            System.out.println("_result:" + response.getResult());
        });
    }
}

```

![image-20230223162318821](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302231623905.png)

#### 3.3.3 查询文档

##### 普通查询

```
package com.jiang.es.doc;

import com.jiang.es.utils.ConnectElasticsearch;
import org.elasticsearch.action.get.GetRequest;
import org.elasticsearch.action.get.GetResponse;
import org.elasticsearch.client.RequestOptions;
/**
 * [普通查询]
 *
 * @author : jiangbx
 * @version : [v1.0]
 * @createTime : [2023/2/23 16:39]
 */
public class GetDoc {
    public static void main(String[] args) {
        ConnectElasticsearch.connect(client -> {
            // 1.创建请求对象
            GetRequest request = new GetRequest().index("user").id("1001");
            // 2.客户端发送请求，获取响应对象
            GetResponse response = client.get(request, RequestOptions.DEFAULT);
            // 3.打印结果信息
            System.out.println("_index:" + response.getIndex());
            System.out.println("_type:" + response.getType());
            System.out.println("_id:" + response.getId());
            System.out.println("source:" + response.getSourceAsString());
        });
    }
}

```

![image-20230223164104877](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302231641971.png)

##### 高级查询

```
package com.jiang.es.doc;


import com.jiang.es.utils.ConnectElasticsearch;
import com.jiang.es.utils.ElasticsearchTask;
import org.elasticsearch.action.search.SearchRequest;
import org.elasticsearch.action.search.SearchResponse;
import org.elasticsearch.client.RequestOptions;
import org.elasticsearch.client.RestHighLevelClient;
import org.elasticsearch.common.unit.Fuzziness;
import org.elasticsearch.index.query.BoolQueryBuilder;
import org.elasticsearch.index.query.QueryBuilders;
import org.elasticsearch.index.query.RangeQueryBuilder;
import org.elasticsearch.index.query.TermsQueryBuilder;
import org.elasticsearch.search.SearchHit;
import org.elasticsearch.search.SearchHits;
import org.elasticsearch.search.aggregations.AggregationBuilders;
import org.elasticsearch.search.builder.SearchSourceBuilder;
import org.elasticsearch.search.fetch.subphase.highlight.HighlightBuilder;
import org.elasticsearch.search.fetch.subphase.highlight.HighlightField;
import org.elasticsearch.search.sort.SortOrder;

import java.io.IOException;
import java.util.Map;
/**
 * [高级查询]
 *
 * @author : jiangbx
 * @version : [v1.0]
 * @createTime : [2023/2/23 16:30]
 */
public class QueryDoc {
    @FunctionalInterface
    public interface QueryBuild {
        void doBuild(SearchRequest request, SearchSourceBuilder sourceBuilder) throws IOException;
    }

    public static void doQuery(RestHighLevelClient client, QueryBuild build) throws IOException {
        // 创建搜索请求对象
        SearchRequest request = new SearchRequest();
        request.indices("user");
        // 构建查询的请求体
        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();

        // 请求体的构建由调用者进行
        build.doBuild(request, sourceBuilder);
        request.source(sourceBuilder);
        SearchResponse response = client.search(request, RequestOptions.DEFAULT);

        // 结果输出
        SearchHits hits = response.getHits();
        System.out.println("took:" + response.getTook());
        System.out.println("timeout:" + response.isTimedOut());
        System.out.println("total:" + hits.getTotalHits());
        System.out.println("MaxScore:" + hits.getMaxScore());
        System.out.println("hits========>>");
        // 输出每条查询的结果信息
        for (SearchHit hit : hits){
            System.out.println(hit.getSourceAsString());
           
        }
        System.out.println("<<========");
    }

    /**
     * 全量查询
     */
    public static final ElasticsearchTask SEARCH_ALL = client -> {
        System.out.println("=======>全量查询<=======");
        doQuery(client, (request, sourceBuilder) -> {
            // 查询全部
            sourceBuilder.query(QueryBuilders.matchAllQuery());
        });
    };
    /**
     * 条件查询
     */
    public static final ElasticsearchTask SEARCH_BY_CONDITION = client -> {
        System.out.println("=======>条件查询<=======");
        doQuery(client, (request, sourceBuilder) -> {
            // 查询条件：age = 30
            sourceBuilder.query(QueryBuilders.termQuery("age", "30"));
        });
    };
    /**
     * 分页查询
     */
    public static final ElasticsearchTask SEARCH_BY_PAGING = client -> {
        System.out.println("=======>分页查询<=======");
        doQuery(client, (request, sourceBuilder) -> {
            // 当前页起始索引(第一条数据的顺序号) from
            sourceBuilder.from(0);
            // 每页显示多少条 size
            sourceBuilder.size(2);
        });
    };
    /**
     * 查询排序
     */
    public static final ElasticsearchTask SEARCH_WITH_ORDER = client -> {
        System.out.println("=======>查询排序<=======");
        doQuery(client, (request, sourceBuilder) -> {
            // 查询全部
            sourceBuilder.query(QueryBuilders.matchAllQuery());
            // 年龄升序
            sourceBuilder.sort("age", SortOrder.ASC);
        });
    };
    /**
     * 组合查询
     */
    public static final ElasticsearchTask SEARCH_BY_BOOL_CONDITION = client -> {
        System.out.println("=======>组合查询<=======");
        doQuery(client, (request, sourceBuilder) -> {
            BoolQueryBuilder boolQueryBuilder = QueryBuilders.boolQuery();
            // 必须包含: age = 30
            boolQueryBuilder.must(QueryBuilders.matchQuery("age", "30"));
            // 一定不含：name = zhangsan
            boolQueryBuilder.mustNot(QueryBuilders.matchQuery("name", "zhangsan"));
            // 可能包含: sex = 男
            boolQueryBuilder.should(QueryBuilders.matchQuery("sex", "男"));
            sourceBuilder.query(boolQueryBuilder);
        });
    };
    /**
     * 范围查询
     */
    public static final ElasticsearchTask SEARCH_BY_RANGE = client -> {
        System.out.println("=======>范围查询<=======");
        doQuery(client, (request, sourceBuilder) -> {
            RangeQueryBuilder rangeQuery = QueryBuilders.rangeQuery("age");
            // rangeQuery.gte("30"); // age 大于等于 30
            rangeQuery.lte("40"); // age 小于等于 40
            sourceBuilder.query(rangeQuery);
        });
    };
    /**
     * 模糊查询
     */
    public static final ElasticsearchTask SEARCH_BY_FUZZY_CONDITION = client -> {
        System.out.println("=======>模糊查询<=======");
        doQuery(client, (request, sourceBuilder) -> {
            // 模糊查询: name 包含 wanqwu 的数据
            sourceBuilder.query(QueryBuilders.fuzzyQuery("name", "wangwu").fuzziness(Fuzziness.ONE));
        });
    };
    /**
     * 高亮查询
     */
    public static final ElasticsearchTask SEARCH_WITH_HIGHLIGHT = client -> {
        System.out.println("=======>高亮查询<=======");
        SearchRequest request = new SearchRequest().indices("user");
        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
        // 构建查询方式：高亮查询
        TermsQueryBuilder termsQueryBuilder =
                QueryBuilders.termsQuery("name", "zhangsan");
        // 设置查询方式
        sourceBuilder.query(termsQueryBuilder);
        // 构建高亮字段
        HighlightBuilder highlightBuilder = new HighlightBuilder();
        highlightBuilder.preTags("<font color='red'>");//设置标签前缀
        highlightBuilder.postTags("</font>");//设置标签后缀
        highlightBuilder.field("name");//设置高亮字段
        // 设置高亮构建对象
        sourceBuilder.highlighter(highlightBuilder);
        // 设置请求体
        request.source(sourceBuilder);
        // 客户端发送请求，获取响应对象
        SearchResponse response = client.search(request, RequestOptions.DEFAULT);
        SearchHits hits = response.getHits();

        for (SearchHit hit : hits) {
            String sourceAsString = hit.getSourceAsString();
            System.out.println(sourceAsString);
            // 打印高亮结果
            Map<String, HighlightField> highlightFields = hit.getHighlightFields();
            System.out.println(highlightFields);
        }
        System.out.println("<<========");
    };
    /**
     * 最大值查询
     */
    public static final ElasticsearchTask SEARCH_WITH_MAX = client -> {
        System.out.println("=======>最大值查询<=======");
        SearchRequest request = new SearchRequest().indices("user");

        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
        sourceBuilder.aggregation(AggregationBuilders.max("maxAge").field("age"));
        request.source(sourceBuilder);

        // 客户端发送请求，获取响应对象
        SearchResponse response = client.search(request, RequestOptions.DEFAULT);
        System.out.println(response);
    };
    /**
     * 分组查询
     */
    public static final ElasticsearchTask SEARCH_WITH_GROUP = client -> {
        System.out.println("=======>分组查询<=======");
        SearchRequest request = new SearchRequest().indices("user");

        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
        sourceBuilder.aggregation(AggregationBuilders.terms("age_groupby").field("age"));
        request.source(sourceBuilder);

        // 客户端发送请求，获取响应对象
        SearchResponse response = client.search(request, RequestOptions.DEFAULT);
        System.out.println(response);
    };

    public static void main(String[] args) {
        ConnectElasticsearch.connect(SEARCH_ALL); // 全量查询
        ConnectElasticsearch.connect(SEARCH_BY_CONDITION); // 条件查询
        ConnectElasticsearch.connect(SEARCH_BY_PAGING); // 分页查询
        ConnectElasticsearch.connect(SEARCH_WITH_ORDER); // 查询排序
        ConnectElasticsearch.connect(SEARCH_BY_BOOL_CONDITION); // 组合查询
        ConnectElasticsearch.connect(SEARCH_BY_RANGE); // 范围查询
        ConnectElasticsearch.connect(SEARCH_BY_FUZZY_CONDITION); // 模糊查询
        ConnectElasticsearch.connect(SEARCH_WITH_HIGHLIGHT); // 高亮查询
        ConnectElasticsearch.connect(SEARCH_WITH_MAX); // 最大值查询
        ConnectElasticsearch.connect(SEARCH_WITH_GROUP); // 分组查询
    }
}

```



#### 3.3.4 删除文档

##### 单条删除

```
package com.jiang.es.doc;

import com.jiang.es.utils.ConnectElasticsearch;
import org.elasticsearch.action.delete.DeleteRequest;
import org.elasticsearch.action.delete.DeleteResponse;
import org.elasticsearch.client.RequestOptions;

/**
 * [单条删除]
 *
 * @author : jiangbx
 * @version : [v1.0]
 * @createTime : [2023/2/23 16:27]
 */
public class DeleteDoc {
    public static void main(String[] args) {
        ConnectElasticsearch.connect(client -> {
            // 1.创建请求对象
            DeleteRequest request = new DeleteRequest().index("user").id("1001");
            // 2.客户端发送请求，获取响应对象
            DeleteResponse response = client.delete(request, RequestOptions.DEFAULT);
            // 3.打印信息
            System.out.println(response.toString());
        });
    }
}

```



##### 批量删除

```
package com.jiang.es.doc;


import com.jiang.es.utils.ConnectElasticsearch;
import org.elasticsearch.action.bulk.BulkRequest;
import org.elasticsearch.action.bulk.BulkResponse;
import org.elasticsearch.action.delete.DeleteRequest;
import org.elasticsearch.client.RequestOptions;
/**
 * [批量删除]
 *
 * @author : jiangbx
 * @version : [v1.0]
 * @createTime : [2023/2/23 16:28]
 */
public class BatchDelDoc {
    public static void main(String[] args) {
        ConnectElasticsearch.connect(client -> {
            // 创建批量删除请求对象
            BulkRequest request = new BulkRequest();
            request.add(new DeleteRequest().index("user").id("1001"));
            request.add(new DeleteRequest().index("user").id("1002"));
            request.add(new DeleteRequest().index("user").id("1003"));
            // 客户端发送请求，获取响应对象
            BulkResponse responses = client.bulk(request, RequestOptions.DEFAULT);
            // 打印结果信息
            System.out.println("took:" + responses.getTook());
            System.out.println("items:" + responses.getItems());
        });
    }
}

```



## 4、ElasticSearch 部署

### 4.1 windows部署

#### 4.1.1  单节点部署

进入到官网：https://www.elastic.co/cn/，这里下载 7.8.0 版本的 ES

bin 包下，双击  elasticsearch.bat，即可 启动 ES。

注意：9300 端口为 ElasticSearch 集群件组件的通信端口，9200 端口为浏览器访问的 http 协议 Restful 端口。

![image-20230225152748882](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230225152748882.png)

#### 4.1.2 集群部署

##### 创建目录

创建 elasticsearch-cluster 文件夹，在 /elasticsearch-cluster 下创建 3个节点文件夹。

![image-20230225154324676](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230225154324676.png)

在 node-1001、node-1002、node-1003 文件夹 复制进去 elasticsearch 服务。

![image-20230225154350991](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230225154350991.png)

如果之前单机启动过，要删除data文件夹和logs里的文件

##### 修改配置文件

![image-20230225153418895](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230225153418895.png)

放开注释，修改配置里的配置项

![image-20230225153625219](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230225153625219.png)

![image-20230225153847519](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230225153847519.png)

![image-20230225154006451](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230225154006451.png)

尾部添加

![image-20230225154031986](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230225154031986.png)

```
# ---------------------------------- Cluster -----------------------------------
#
# Use a descriptive name for your cluster:
#
cluster.name: my-application
#
# ------------------------------------ Node ------------------------------------
#
# Use a descriptive name for the node:
#
node.name: node-1001
node.master: true
node.data: true
# ---------------------------------- Network -----------------------------------
#
# Set the bind address to a specific IP (IPv4 or IPv6):
#
network.host: localhost
#
# Set a custom port for HTTP:
#
http.port: 1001
# TCP 监听端口
transport.tcp.port: 9301

# 跨域配置
http.cors.enabled: true
http.cors.allow-origin: "*"
```

然后双击 /bin/elasticsearch.bat 启动

![image-20230225154958561](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230225154958561.png)

节点2配置

```
# ---------------------------------- Cluster -----------------------------------
#
# Use a descriptive name for your cluster:
#
cluster.name: my-application
#
# ------------------------------------ Node ------------------------------------
#
# Use a descriptive name for the node:
#
node.name: node-1002
node.master: true
node.data: true
# ---------------------------------- Network -----------------------------------
#
# Set the bind address to a specific IP (IPv4 or IPv6):
#
network.host: localhost
#
# Set a custom port for HTTP:
#
http.port: 1002
# TCP 监听端口
transport.tcp.port: 9302

discovery.seed_hosts: ["localhost:9301"]
discovery.zen.fd.ping_timeout: 1m
discovery.zen.fd.ping_retries: 5

# 跨域配置
http.cors.enabled: true
http.cors.allow-origin: "*"
```

![image-20230225155442391](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230225155442391.png)

然后双击 /bin/elasticsearch.bat 启动

观察发现已启动2个节点

![image-20230225155852617](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230225155852617.png)

节点3

```
# ---------------------------------- Cluster -----------------------------------
#
# Use a descriptive name for your cluster:
#
cluster.name: my-application
#
# ------------------------------------ Node ------------------------------------
#
# Use a descriptive name for the node:
#
node.name: node-1003
node.master: true
node.data: true
# ---------------------------------- Network -----------------------------------
#
# Set the bind address to a specific IP (IPv4 or IPv6):
#
network.host: localhost
#
# Set a custom port for HTTP:
#
http.port: 1003
# TCP 监听端口
transport.tcp.port: 9303

discovery.seed_hosts: ["localhost:9301","localhost:9302"]
discovery.zen.fd.ping_timeout: 1m
discovery.zen.fd.ping_retries: 5

# 跨域配置
http.cors.enabled: true
http.cors.allow-origin: "*"
```

然后双击 /bin/elasticsearch.bat 启动

![image-20230225160214812](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230225160214812.png)

观察发现已启动3个节点



##### 查询集群状态

查询集群的状态：http://localhost:1001/_cluster/health

![](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230225160214812.png)



### 4.2 Linux部署

#### 4.2.1 单机部署

##### 下载

 elasticsearch-7.8.0-linux-x86_64.tar.gz

上传到服务器

![image-20230225161630006](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230225161630006.png)

##### 解压缩

```
# 解压
tar -zxvf elasticsearch-7.8.0-linux-x86_64.tar.gz

# 改名
mv elasticsearch-7.8.0 es
# 移动
mv es /usr/local/es
```

##### 创建用户

因为安全问题，ES 不允许 root 用户直接运行，所以要创建新用户，在 root 用户中创建新用户。

```
# 新增 es 用户
useradd es

# 设置密码
passwd es

# 查看所有用户，/home 下 每一个文件夹就是一个用户
cd /home
ll

# 如果需要删除 es 用户
userdel -r es

# 给 es 用户赋权 文件夹所有者（下面目录写自己 es 所在目录）
chown -R es:es /usr/local/es
```

![image-20230225162323617](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230225162323617.png)

##### 修改配置

/es/config/elasticsearch.yml

```
cluster.name: elasticsearch
node.name: node-1
network.host: 0.0.0.0
http.port: 9200
cluster.initial_master_nodes: ["node-1"]
```

![image-20230225162713242](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230225162713242.png)

#####  /etc/security/limits.conf

```
cd /etc/security/
vi limits.conf

# 每个进程可以打开的文件数的限制
es soft nofile 65536
es hard nofile 65536
```

##### /etc/security/limits.d/20-nproc.conf

```
vi /etc/security/limits.d/20-nproc.conf

# 每个进程可以打开的文件数的限制
es soft nofile 65536
es hard nofile 65536
```

##### /etc/sysctl.conf

```
vi /etc/sysctl.conf

# 一个进程可以拥有的 VMA （虚拟内存区域）的数量，默认值为 65536
vm.max_map_count=655360
```

重新加载

```
sysctl -p
```

启动服务

```
# 进入到 es 目录下
cd /01Java/es/

# 启动 es （root 用户会启动失败）
bin/elasticsearch

# 切换 es 用户 启动 es 服务
su es
bin/elasticsearch
```

##### 测试

注意：防火墙；端口开放

关闭防火墙：systemctl stop firewalld

异常

```
[2023-02-25T16:35:13,219][INFO ][o.e.t.TransportService   ] [node-1] publish_address {192.168.204.105:9300}, bound_addresses {[::]:9300}
[2023-02-25T16:35:13,477][INFO ][o.e.b.BootstrapChecks    ] [node-1] bound or publishing to a non-loopback address, enforcing bootstrap checks
ERROR: [1] bootstrap checks failed
[1]: max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
ERROR: Elasticsearch did not exit normally - check the logs at /usr/local/es/logs/elasticsearch.log
[2023-02-25T16:35:13,504][INFO ][o.e.n.Node               ] [node-1] stopping ...
[2023-02-25T16:35:13,526][INFO ][o.e.n.Node               ] [node-1] stopped
[2023-02-25T16:35:13,527][INFO ][o.e.n.Node               ] [node-1] closing .
```

解决办法：

重新加载/etc/sysctl.conf配置

```
sysctl -p
```

浏览器查询验证

![image-20230225163934857](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230225163934857.png)



#### 4.2.2 集群部署

在 Linux 单节点部署 中，Node_1 已部署 es。

在 Node_2、Node_3 服务器中 部署 es。

```
# 解压
tar -zxvf elasticsearch-7.8.0-linux-x86_64.tar.gz

# 改名
mv elasticsearch-7.8.0 es
# 移动
mv es /usr/local/es
```

在 Node_2、Node_3 中创建 es 用户。

```
# 新增 es 用户
useradd es

# 设置密码（密码设为 es）
passwd es

# 查看所有用户，/home 下 每一个文件夹就是一个用户
cd /home
ll

# 删除 es 用户
userdel -r es

# 给 es 用户赋权 文件夹所有者（下面目录写自己 es 所在目录）
chown -R es:es /usr/local/es
```

![image-20230226142426088](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230226142426088.png)



参照单节点，修改另外2台服务器的配置

```
cd /etc/security/
vi limits.conf

# 每个进程可以打开的文件数的限制
es soft nofile 65536
es hard nofile 65536
```

```
vi /etc/security/limits.d/20-nproc.conf

# 每个进程可以打开的文件数的限制
es soft nofile 65536
es hard nofile 65536

# * 代表 linux 所有用户名称
* hard nproc 4096
```

```
vi /etc/sysctl.conf

# 一个进程可以拥有的 VMA （虚拟内存区域）的数量，默认值为 65536
vm.max_map_count=655360

# 重新加载， 使配置生效。
sysctl -p
```

修改3个节点的配置文件

```
# 集群名称
cluster.name: cluster-es
# 节点名称，每个节点的名称不能重复
node.name: node-1
# Ip地址
network.host: 192.168.204.105
http.port: 9200
# 是不是有资格 主节点
node.master: true
node.data: true

# head 插件需要打开着两个配置
http.cors.allow-origin: "*"
http.cors.enabled: true

http.max_content_length: 200mb

# es 7.x 之后新增的配置，初始化一个新的集群时需要此配置来选举 master
cluster.initial_master_nodes: ["node-1"]
# es 7.x 之后新增的配置，节点发现
discovery.seed_hosts: ["192.168.204.105:9300","192.168.204.106:9300","192.168.204.104:9300"]
gateway.recover_after_nodes: 2
network.tcp.keep_alive: true
network.tcp.no_delay: true
transport.tcp.compress: true

# 集群内同时启动的数据任务个数，默认是2个
cluster.routing.allocation.cluster_concurrent_rebalance: 16
# 添加或删除节点 及 负载均衡时并发恢复的线程个数，默认4个
cluster.routing.allocation.node_concurrent_recoveries: 16
# 初始化数据恢复时，并发恢复线程的个数，默认4个
cluster.routing.allocation.node_initial_primaries_recoveries: 16
```

Node2

```
# 集群名称
cluster.name: cluster-es
# 节点名称，每个节点的名称不能重复
node.name: node-2
# Ip地址
network.host: 192.168.204.106
http.port: 9200
# 是不是有资格 主节点
node.master: true
node.data: true

# head 插件需要打开着两个配置
http.cors.allow-origin: "*"
http.cors.enabled: true

http.max_content_length: 200mb

# es 7.x 之后新增的配置，初始化一个新的集群时需要此配置来选举 master
cluster.initial_master_nodes: ["node-1"]
# es 7.x 之后新增的配置，节点发现
discovery.seed_hosts: ["192.168.204.105:9300","192.168.204.106:9300","192.168.204.104:9300"]
gateway.recover_after_nodes: 2
network.tcp.keep_alive: true
network.tcp.no_delay: true
transport.tcp.compress: true

# 集群内同时启动的数据任务个数，默认是2个
cluster.routing.allocation.cluster_concurrent_rebalance: 16
# 添加或删除节点 及 负载均衡时并发恢复的线程个数，默认4个
cluster.routing.allocation.node_concurrent_recoveries: 16
# 初始化数据恢复时，并发恢复线程的个数，默认4个
cluster.routing.allocation.node_initial_primaries_recoveries: 16
```

Node3

```
# 集群名称
cluster.name: cluster-es
# 节点名称，每个节点的名称不能重复
node.name: node-3
# Ip地址
network.host: 192.168.204.104
http.port: 9200
# 是不是有资格 主节点
node.master: true
node.data: true

# head 插件需要打开着两个配置
http.cors.allow-origin: "*"
http.cors.enabled: true

http.max_content_length: 200mb

# es 7.x 之后新增的配置，初始化一个新的集群时需要此配置来选举 master
cluster.initial_master_nodes: ["node-1"]
# es 7.x 之后新增的配置，节点发现
discovery.seed_hosts: ["192.168.204.105:9300","192.168.204.106:9300","192.168.204.104:9300"]
gateway.recover_after_nodes: 2
network.tcp.keep_alive: true
network.tcp.no_delay: true
transport.tcp.compress: true

# 集群内同时启动的数据任务个数，默认是2个
cluster.routing.allocation.cluster_concurrent_rebalance: 16
# 添加或删除节点 及 负载均衡时并发恢复的线程个数，默认4个
cluster.routing.allocation.node_concurrent_recoveries: 16
# 初始化数据恢复时，并发恢复线程的个数，默认4个
cluster.routing.allocation.node_initial_primaries_recoveries: 16
```

启动服务

```
# 进入到 es 目录下
cd /usr/local/es/

# 切换 es 用户 启动 es 服务
su es
bin/elasticsearch
# 后台启动
bin/elasticsearch -d
```

测试集群

http://192.168.204.105:9200/_cat/nodes

Bug：遇到节点无法加入到集群情况

如果还是采用上述 配置，无法加入到集群，就自己形成了一个集群。把 node.master 改成 false，不让其自己形成一个集群。报错信息如下图。

```
# 是不是有资格 主节点
node.master: false
```

![image-20230226154042606](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230226154042606.png)

## 5、Elasticsearch进阶

### 5.1 核心概念

#### 5.1.1 索引（Index）

一个索引就是一个拥有几分相似特征的文档的集合。比如说，你可以有一个客户数据的索引，另一个产品目录的索引，还有一个订单数据的索引。一个索引由一个名字来标识（必须全部是小写字母），并且当我们要对这个索引中的文档进行索引、搜索、更新和删除的时候，都要使用到这个名字。在一个集群中，可以定义任意多的索引。

能搜索的数据必须索引，这样的好处是可以提高查询速度，比如：新华字典前面的目录就是索引的意思，目录可以提高查询速度。典型的空间换时间。

**ElasticSearch 索引的精髓：一切设计都是为了 提高搜索的性能**。



#### 5.1.2 类型（Type）

在一个索引中，你可以定义一种或多种类型。

一个类型是你的索引的一个逻辑上的分类 / 分区，其语义完全由你来定。通常，会为具有一组共同字段的文档定义一个类型。不同的版本，类型发生了不同的变化。

![image-20230226155550543](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230226155550543.png)

#### 5.1.3 文档（Document）

一个文档是一个可被索引的基础信息单元，也就是一条数据。

比如：你可以拥有某一个客户的文档，某一个产品的一个文档；当然，也可以拥有某个订单的一个文档。文档以 JSON（Javascript Object Notation）格式来表示，而 JSON 是一个到处存在的互联网数据交互格式。

在一个 index / type 里面，你可以存储任意多的文档。

#### 5.1.4 字段（Field）

相当于是数据表的字段，对文档数据根据不同属性进行的分类标识。

#### 5.1.5 映射（Mapping）

Mapping 是处理数据的方式和规则方面做一些限制，如：某个字段的数据类型、默认值、分析器、是否被索引等等。这些都是映射里面可以设置的，其他就是处理 ES 里面数据的一些使用规则设置也叫做映射，按着最优规则处理数据对性能提高很大，因此才需要建立映射，并且需要思考如何建立映射才能对性能更好。

#### 5.1.6  分片（Shards）

一个索引可以存储超出单个节点硬件限制的大量数据。比如，一个具有 10亿 文档数据的索引占 1TB 的磁盘空间，而任一节点都可能没有这样大的磁盘空间。或者单个节点处理搜索请求，响应太慢。为了解决这个问题， ES 提供了将索引划分成多份的能力，每一份就称之为分片。当你创建一个索引的时候，你可以指定你想要的分片的数量。每个分片本身也是一个功能完善并且独立的“索引”，这个“索引”可以被放置到集群中的任何节点上。

分片很重要，主要有两方面的原因：

1. 允许你水平分割 / 扩展你的内容容量。
2. 允许你在分片之上进行分布式、并行的操作，进而提高性能 / 吞吐量。

至于一个分片怎样分布，它的文档怎样聚合和搜索请求，是完全由 ES 管理的，对于作为用户的你来说，这些都是透明的，无需过分关心。

被混淆的概念是，一个 Lucene 索引 我们在 ES 称作分片。一个 ES 索引是分片的集合。当 ES 在索引中搜索的时候，它发送查询到每一个属于索引的分片（Lucene 索引），然后合并每个分片的结果到一个全局的结果集。



#### 5.1.7 副本（Replicas）

在一个网络 / 云 的环境里，失败随时都可能发生，在某个分片 / 节点 不知怎么的就处于离线状态，或者由于任何原因消失了，这种情况下，有一个故障转移机制是非常有用并且强烈推荐的。为此目的，ES 允许你创建分片的一份或多份拷贝，这些拷贝叫做复制分片（副本）。

复制分片之所以重要，有两个主要原因：

1. 在分片 / 节点失败的情况下，提供了高可用性。因为这个原因，注意到复制分片从不与 原 / 主要（original / primary）分片置于同一个节点上是非常重要的。
2. 扩展你的搜素量 / 吞吐量，因为搜索可以在所有的副本上并行运行。

总之，每个索引可以被分成多个分片。一个索引也可以被复制 0 次（意思是没有复制）或多次。一旦复制了，每个索引就有了主分片（作为复制原来的分片）和复制分片（主分片的拷贝）之别。分片和复制的数量可以在索引创建的时候指定。在索引创建之后，你可以在任何时候动态地改变复制的数量，但是你事后不能改变分片的数量。默认情况下，ES 中的每个索引被分片成 1 个主分片 和 1 个复制分片，这意味着，如果你的集群中至少有两个节点，你的索引将会从 1 个主分别和另外 1 个复制分片（1 个完全拷贝），这样的话每个索引总共就有 2 个分片，我们需要根据索引需要确定分片个数。

#### 5.1.8 分配（Allocation）

将分片分配给某个节点的过程，包括分配主分片或者副本。如果是副本，还包含从主分片复制数据的过程。这个过程是由 master 节点完成的。

### 5.2 系统架构

![image-20230226155011555](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230226155011555.png)

### 5.3 分布式集群

#### 5.3.1 单节点集群

我们在包含一个空节点的集群内创建名为 uesrs 的索引，为了演示目的，我们将分配 3个主分片 和 1份副本（每个主分片拥有一副本分片）

（PUT 请求体）http://localhost:1001/users 创建 users索引。

shards：分片；此例 指定 3 个分片。

replicas：副本；此例 自定 1 个副本。

```
{
    "settings": {
        "number_of_shards": 3,
        "number_of_replicas": 1
    }
}
```

![image-20230226163959670](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230226163959670.png)



（GET）http://localhost:1001/users，查看 uses 索引。

![image-20230226164116785](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230226164116785.png)

上述例子是 一个单节点集群 创建的索引，3 个主分片都被分配在 上面的 单节点 ES 上。

![image-20230226185149332](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230226185149332.png)

副本分片没有全部处在正常状态。黄色

![image-20230226193724598](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230226193724598.png)

3个副本分片都是 Unassigned —— 它们都没有被分配到任何节点。在同一个节点上即保存原始数据又保存副本是没有意义的，因为一旦失去了那个节点，我们也将丢失该节点上的所有副本数据。

![image-20230226193811359](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230226193811359.png)



#### 5.3.2 故障转移

当集群中只有一个节点在运行时，意味着会有一个单点故障问题——没有冗余。幸运的是，我们只需再启动一个节点即可防止数据丢失。当你在同一台机器上启动了第二个节点时，只要它和第一个节点有同样的 cluster.name 配置，它就会自动发现集群并加入到其中。但是在不同机器上启动节点的时候，为了加入到同一个集群，你需要配置一个可连接到的单播主机列表。之所有配置为使用单播发现，以防止节点无意中加入集群。只有在同一台机器上运行的节点才会自动组成集群。

如果启动了第二个节点，我们的集群将会拥有两个节点的集群：所有主分片和副本分片都已被分配。

再启动一个节点，观察

![](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230226194946557.png)

![image-20230226195028163](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230226195028163.png)

![image-20230226195143713](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230226195143713.png)



#### 5.3.3 水平扩容

怎么样为我们的正在增长中的应用程序按需扩容呢？当启动了第三个节点，我们的集群将会拥有三个节点的集群：为了分散负载而对分片进行重新分配。

![image-20230226195543928](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230226195543928.png)

新增一个 节点加入 集群后，分片会自动重新分配。

这表示每个节点的硬件资源（CPU，RAM，I / O）将被更少的分片所共享，每个分片的性能将会得到提升。

分片是一个功能完整的搜索引擎，它拥有使用一个节点上的所有资源的能力。我们这个拥有 6 个分片（3个主分片和3个副本分片）的索引可以最大扩容到6个节点，每个节点上存在一个分片，并且每个分片拥有所在节点的全部资源。



但是如果我们想要扩容超过 6 个节点怎么办呢？

主分片的数目在索引创建时就已经确定下来。实际上，这个数目定义了这个索引能够存储的最大数据量。（实际大小取决于你的数据、硬件和使用场景。）但是，读操作——搜索和返回数据——可以同时被主分片或副本分片所处理，所以当你拥有越多的副本分片时，也将拥有越高的吞吐量。

在运行的集群上是可以动态调整副本分片数目的，我们可以按需伸缩集群。让我们把副本数从默认的 1 增加到 2

（PUT 请求体）http://localhost:1001/users/_settings，修改 user 索引 的副本数。

```
{
   "number_of_replicas": 2
}
```

![image-20230226195841509](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230226195841509.png)

![image-20230226195943434](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230226195943434.png)



#### 5.3.4 应对故障

stop node-1001 主节点，会重新选举一个 新的主节点

![image-20230226200627458](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230226200627458.png)

修改 node-1001 的 配置文件 elasticsearch.yaml，加入 下面代码（如果不修改配置，重新启动 node-1001 不会加入集群里）

```
discovery.seed_hosts: ["localhost:9303", "localhost:9302"]
```

![image-20230226200832069](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230226200832069.png)

再次启动节点1

再次查看，可以发现 node-1001 回到集群里来了。但主节点依然是节点3

![image-20230226201338517](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230226201338517.png)

### 5.4 路由计算 & 分片控制

![image-20230226202822464](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230226202822464.png)

![image-20230226202839260](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230226202839260.png)



### 5.5 数据流程

#### 5.5.1 数据写流程

![image-20230226155158652](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230226155158652.png)

1. 客户端向 Node1 发送新建、索引或者删除请求。
2. 节点使用文档的 _id 确定文档属于分片0。请求会被转发到 Node3，因为分片0的主分片目前被分配在 Node3 上。
3. Node3 在主分片上面执行请求。如果成功了，它将请求并行转发到 Node1 和 Node2 的副本分片上。一旦所有的副本分片都报告成功，Node3 将向协调节点报告成功，协调节点向 客户端报告成功。

| 参数        | 含义                                                         |
| ----------- | ------------------------------------------------------------ |
| consistency | consistency，即一致性。在默认设置下，即是仅仅是在试图执行一个 写 操作之前，主分片都会要求必须要有规定数量（quorum）（或者换种说法，也即必须要有大多数）的分片副本处于活跃可用状态，才会去执行 写 操作（其中分片副本可以是主分片或者副本分片）。这是为了避免在发生网络分区故障（network partition）的时候进行 写 操作，进而导致数据不一致。规定数量，即 int（（primary + number_of_replicas）/ 2）+ 1consistency参数的值可以设为one（只要主分片状态 ok 就允许执行 写 操作）all（必须要主分片和所有副本分片的状态没问题才允许执行 写 操作）quorum（默认），即大多数的分片副本状态没问题就允许执行 写 操作注意，规定数量的计算公式中 number_of_replicas指的是在索引设置中的设定副本分片数，而不是指当前处理活动状态的副本分片数。如果你的索引设置中指定了当前索引拥有三个副本分片，那规定数量的计算结果即：int（（primary + 3 replicas）/ 2） + 1 = 3如果此时你只启动两个节点，那么处于活跃状态的分片副本数量就达不到规定数量，也因此您将无法索引和删除任何文档。 |
| timeout     | 如果没有足够的副本分片会发生什么？ES 会等待，希望更多的分片出现。默认情况下，它最多等待 1 分钟。如果你需要，你可以使用 timeout 参数使它更早终止：100 是 100 毫秒，30s 是 30 秒。 |

新索引默认有 1 个 副本分片，这意味着为满足 规定数量应该需要两个活动的分片副本。但是，这些默认的设置会阻止我们在单一节点上做任何事情。为了避免这个问题，要求只有当 number_of_replicas 大于 1 的时候，规定数量才会执行。

#### 5.5.2 数据读流程

我们可以从主分片或者从其他任意副本分片检索文档。

![image.png](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/1664164271527-c4d11596-0abb-4931-a98d-cf1fe0b2364a.png)

#### 5.5.3 更新流程

部分更新一个文档的步骤如下：

![image-20230226203710201](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230226203710201.png)

1. 客户端向 Node1 发送更新请求
2. ES 将请求转发到主分片所在的 Node3
3. Node3 从主分片检索文档，修改 _source 字段中的 JSON，并且尝试重新索引主分片的文档。如果文档已经被另一个进程修改，它会重试步骤3，超过 retry_on_conflict 次后放弃。
4. 如果 Node3 成功地更新文档，它将新版本的文档并行转发到 Node1 和 Node2 上的副本分片，重新建立索引。一旦所有副本分片都返回成功，Node3 向协调节点也返回成功，协调节点向客户端返回成功。

当主分片把更改转发到副本分片时，它不会转发更新请求。相反，它转发完整文档的新版本。请记住，这些更改将会异步转发到副本分片，并且不能保证它们以发送它们相同的顺序到达。如果 ES 仅转发更改请求，则可能以错误的顺序应用更改，导致得到损坏的文档。



#### 5.5.4 批量操作流程

![image-20230226204002667](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230226204002667.png)

mget 和 buikAPI 的模式类似于单文档模式。区别在于协调节点知道每个文档存在于哪个分片中。它将整个多文档请求分解成每个分片的多文档请求，并且将这些请求并行转发到每个参与节点。

协调节点一旦收到来自每个节点的应答，就将每个节点的响应收集整理成单个响应，返回给客户端

#### 5.5.5 分片原理

分片是 ES 最小的工作单元。传统的数据库每个字段存储单个值，但这对全文检索并不够。文本字段中的每个单词需要被搜索，对数据库意味着需要单个字段有索引多值的能力。最好的支持是一个字段多个值需求的数据接口是 倒排索引。

#### 5.5.6 倒排索引

ES 使用一种称为倒排索引的结构，它适用于快速的全文搜索。

见其名，知其意，有倒排索引，肯定会对应有正向索引。正向索引（forward index），反向索引（inverted index）更熟悉的名字是 倒排索引。



#### 5.5.7 文档搜索

##### 不可改变的倒排索引

早期的全文检索会为整个文档集合建立一个很大的倒排索引并将其写入到磁盘。 一旦新的索引就绪，旧的就会被其替换，这样最近的变化便可以被检索到。

倒排索引被写入磁盘后是不可改变的：它永远不会修改。

不需要锁。如果你从来不更新索引，你就不需要担心多进程同时修改数据的问题。

一旦索引被读入内核的文件系统缓存，便会留在哪里，由于其不变性。只要文件系统缓存中还有足够的空间，那么大部分读请求会直接请求内存，而不会命中磁盘。这提供了很大的性能提升。

其它缓存(像filter缓存)，在索引的生命周期内始终有效。它们不需要在每次数据改变时被重建，因为数据不会变化。

写入单个大的倒排索引允许数据被压缩，减少磁盘IO和需要被缓存到内存的索引的使用量。

当然，一个不变的索引也有不好的地方。主要事实是它是不可变的! 你不能修改它。如果你需要让一个新的文档可被搜索，你需要重建整个索引。这要么对一个索引所能包含的数据量造成了很大的限制，要么对索引可被更新的频率造成了很大的限制



##### 动态更新索引

如何在保留不变性的前提下实现倒排索引的更新？

答案是：用更多的索引。通过增加新的补充索引来反映新近的修改，而不是直接重写整个倒排索引。每一个倒排索引都会被轮流查询到,从最早的开始查询完后再对结果进行合并。

Elasticsearch基于Lucene，这个java库引入了按段搜索的概念。每一段本身都是一个倒排索引，但索引在 Lucene 中除表示所有段的集合外，还增加了提交点的概念—一个列出了所有已知段的文件。



按段搜索会以如下流程执行：

一、新文档被收集到内存索引缓存。

二、不时地, 缓存被提交。

一个新的段，一个追加的倒排索引，被写入磁盘。
一个新的包含新段名字的提交点被写入磁盘。
磁盘进行同步，所有在文件系统缓存中等待的写入都刷新到磁盘，以确保它们被写入物理文件
三、新的段被开启，让它包含的文档可见以被搜索。

四、内存缓存被清空，等待接收新的文档。


当一个查询被触发，所有已知的段按顺序被查询。词项统计会对所有段的结果进行聚合，以保证每个词和每个文档的关联都被准确计算。这种方式可以用相对较低的成本将新文档添加到索引。

段是不可改变的，所以既不能从把文档从旧的段中移除，也不能修改旧的段来进行反映文档的更新。取而代之的是，每个提交点会包含一个.del 文件，文件中会列出这些被删除文档的段信息。

当一个**文档被“删除”**时，它实际上只是在 .del 文件中被标记删除。一个被标记删除的文档仍然可以被查询匹配到，但它会在最终结果被返回前从结果集中移除。

文档更新也是类似的操作方式:当一个文档被更新时，旧版本文档被标记删除，文档的新版本被索引到一个新的段中。可能两个版本的文档都会被一个查询匹配到，但被删除的那个旧版本文档在结果集返回前就已经被移除。

#### 5.5.8 文档刷新 & 文档刷写 & 文档合并

![image-20230226205309894](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230226205309894.png)

![image-20230226205330078](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230226205330078.png)

##### 近实时搜索

随着按段（per-segment）搜索的发展，一个新的文档从索引到可被搜索的延迟显著降低了。新文档在几分钟之内即可被检索，但这样还是不够快。磁盘在这里成为了瓶颈。提交（Commiting）一个新的段到磁盘需要一个fsync来确保段被物理性地写入磁盘，这样在断电的时候就不会丢失数据。但是fsync操作代价很大；如果每次索引一个文档都去执行一次的话会造成很大的性能问题。

我们需要的是一个更轻量的方式来使一个文档可被搜索，这意味着fsync要从整个过程中被移除。在Elasticsearch和磁盘之间是文件系统缓存。像之前描述的一样，在内存索引缓冲区中的文档会被写入到一个新的段中。但是这里新段会被先写入到文件系统缓存—这一步代价会比较低，稍后再被刷新到磁盘—这一步代价比较高。不过只要文件已经在缓存中，就可以像其它文件一样被打开和读取了

![img](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/a679d4f5f4bfa6913a53316251beef2a.png)

Lucene允许新段被写入和打开，使其包含的文档在未进行一次完整提交时便对搜索可见。这种方式比进行一次提交代价要小得多，并且在不影响性能的前提下可以被频繁地执行。

![img](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/673d3a77e254fa3a5a6f5293ffb125ab.png)

在 Elasticsearch 中，写入和打开一个新段的轻量的过程叫做refresh。默认情况下每个分片会每秒自动刷新一次。这就是为什么我们说 Elasticsearch是近实时搜索：文档的变化并不是立即对搜索可见，但会在一秒之内变为可见。

这些行为可能会对新用户造成困惑：他们索引了一个文档然后尝试搜索它，但却没有搜到。这个问题的解决办法是用refresh API执行一次手动刷新：/usersl_refresh

尽管刷新是比提交轻量很多的操作，它还是会有性能开销。当写测试的时候，手动刷新很有用，但是不要在生产环境下每次索引一个文档都去手动刷新。相反，你的应用需要意识到Elasticsearch 的近实时的性质，并接受它的不足。

并不是所有的情况都需要每秒刷新。可能你正在使用Elasticsearch索引大量的日志文件，你可能想优化索引速度而不是近实时搜索，可以通过设置refresh_interval ，降低每个索引的刷新频率

```
{
    "settings": {
    	"refresh_interval": "30s"
    }
}

```

refresh_interval可以在既存索引上进行动态更新。在生产环境中，当你正在建立一个大的新索引时，可以先关闭自动刷新，待开始使用该索引时，再把它们调回来。

```
# 关闭自动刷新
PUT /users/_settings
{ "refresh_interval": -1 }

# 每一秒刷新
PUT /users/_settings
{ "refresh_interval": "1s" }

```

##### 持久化变更

如果没有用fsync把数据从文件系统缓存刷（flush）到硬盘，我们不能保证数据在断电甚至是程序正常退出之后依然存在。为了保证Elasticsearch 的可靠性，需要确保数据变化被持久化到磁盘。在动态更新索引，我们说一次完整的提交会将段刷到磁盘，并写入一个包含所有段列表的提交点。Elasticsearch 在启动或重新打开一个索引的过程中使用这个提交点来判断哪些段隶属于当前分片。

即使通过每秒刷新(refresh）实现了近实时搜索，我们仍然需要经常进行完整提交来确保能从失败中恢复。但在两次提交之间发生变化的文档怎么办?我们也不希望丢失掉这些数据。Elasticsearch 增加了一个translog ，或者叫事务日志，在每一次对Elasticsearch进行操作时均进行了日志记录。

整个流程如下:
一、一个文档被索引之后，就会被添加到内存缓冲区，并且追加到了 translog

![img](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/baeab48c8d6b87660ac4fb954e9c9731.png)

二、刷新（refresh）使分片每秒被刷新（refresh）一次：

- 这些在内存缓冲区的文档被写入到一个新的段中，且没有进行fsync操作。
- 这个段被打开，使其可被搜索。
- 内存缓冲区被清空。

![img](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/17be4247e6b23f31b1e589c70d61e817.png)

三、这个进程继续工作，更多的文档被添加到内存缓冲区和追加到事务日志。

![img](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/4b5c4a3a3ffb4c84625bb283f6a67018.png)

四、每隔一段时间—例如translog变得越来越大，索引被刷新（flush）；一个新的translog被创建，并且一个全量提交被执行。

- 所有在内存缓冲区的文档都被写入一个新的段。

- 缓冲区被清空。

- 一个提交点被写入硬盘。

- 文件系统缓存通过fsync被刷新（flush） 。

- 老的translog被删除。
  

translog 提供所有还没有被刷到磁盘的操作的一个持久化纪录。当Elasticsearch启动的时候，它会从磁盘中使用最后一个提交点去恢复己知的段，并且会重放translog 中所有在最后一次提交后发生的变更操作。

translog 也被用来提供实时CRUD。当你试着通过ID查询、更新、删除一个文档，它会在尝试从相应的段中检索之前，首先检查 translog任何最近的变更。这意味着它总是能够实时地获取到文档的最新版本。


![img](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/11c7d2cc05244e669eb8402dd8049de9.png)

执行一个提交并且截断translog 的行为在 Elasticsearch被称作一次flush。分片每30分钟被自动刷新（flush)，或者在 translog 太大的时候也会刷新。

你很少需要自己手动执行flush操作，通常情况下，自动刷新就足够了。这就是说，在重启节点或关闭索引之前执行 flush有益于你的索引。当Elasticsearch尝试恢复或重新打开一个索引，它需要重放translog中所有的操作，所以如果日志越短，恢复越快。

translog 的目的是保证操作不会丢失，在文件被fsync到磁盘前，被写入的文件在重启之后就会丢失。默认translog是每5秒被fsync刷新到硬盘，或者在每次写请求完成之后执行（e.g. index, delete, update, bulk）。这个过程在主分片和复制分片都会发生。最终，基本上，这意味着在整个请求被fsync到主分片和复制分片的translog之前，你的客户端不会得到一个200 OK响应。

在每次请求后都执行一个fsync会带来一些性能损失，尽管实践表明这种损失相对较小（特别是 bulk 导入，它在一次请求中平摊了大量文档的开销）。

但是对于一些大容量的偶尔丢失几秒数据问题也并不严重的集群，使用异步的 fsync还是比较有益的。比如，写入的数据被缓存到内存中，再每5秒执行一次 fsync 。如果你决定使用异步translog 的话，你需要保证在发生 crash 时，丢失掉 sync_interval时间段的数据也无所谓。请在决定前知晓这个特性。如果你不确定这个行为的后果，最好是使用默认的参数{“index.translog.durability”: “request”}来避免数据丢失


##### 段合并

由于自动刷新流程每秒会创建一个新的段，这样会导致短时间内的段数量暴增。而段数目太多会带来较大的麻烦。每一个段都会消耗文件句柄、内存和 cpu运行周期。更重要的是，每个搜索请求都必须轮流检查每个段；所以段越多，搜索也就越慢。

Elasticsearch通过在后台进行段合并来解决这个问题。小的段被合并到大的段，然后这些大的段再被合并到更大的段。

段合并的时候会将那些旧的已删除文档从文件系统中清除。被删除的文档（或被更新文档的旧版本）不会被拷贝到新的大段中。

启动段合并不需要你做任何事。进行索引和搜索时会自动进行。

一、当索引的时候，刷新（refresh）操作会创建新的段并将段打开以供搜索使用。

二、合并进程选择一小部分大小相似的段，并且在后台将它们合并到更大的段中。这并不会中断索引和搜索。

![img](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/c907ca35bd7c0393d46aec2c7038af19.png)

三、一旦合并结束，老的段被删除

新的段被刷新(flush)到了磁盘。
写入一个包含新段且排除旧的和较小的段的新提交点。
新的段被打开用来搜索。老的段被删除。

![img](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/a00cc1c19652c47fcfb663aaf337a41b.png)


合并大的段需要消耗大量的 I/O 和 CPU 资源，如果任其发展会影响搜索性能。 Elasticsearch在默认情况下会对合并流程进行资源限制，所以搜索仍然有足够的资源很好地执行。


#### 5.5.9 文档分析

分析包含下面的过程：

将一块文本分成适合于倒排索引的独立的词条。
将这些词条统一化为标准格式以提高它们的“可搜索性”，或者recall。
分析器执行上面的工作。分析器实际上是将三个功能封装到了一个包里：

字符过滤器：首先，字符串按顺序通过每个 字符过滤器 。他们的任务是在分词前整理字符串。一个字符过滤器可以用来去掉 HTML，或者将 & 转化成 and。
分词器：其次，字符串被分词器分为单个的词条。一个简单的分词器遇到空格和标点的时候，可能会将文本拆分成词条。
Token 过滤器：最后，词条按顺序通过每个 token 过滤器 。这个过程可能会改变词条（例如，小写化Quick ），删除词条（例如， 像 a， and， the 等无用词），或者增加词条（例如，像jump和leap这种同义词）

##### 内置分析器

Elasticsearch还附带了可以直接使用的预包装的分析器。接下来我们会列出最重要的分析器。为了证明它们的差异，我们看看每个分析器会从下面的字符串得到哪些词条：

```
"Set the shape to semi-transparent by calling set_trans(5)"
```





##### 分析器使用场景

当我们索引一个文档，它的全文域被分析成词条以用来创建倒排索引。但是，当我们在全文域搜索的时候，我们需要将查询字符串通过相同的分析过程，以保证我们搜索的词条格式与索引中的词条格式一致。

全文查询，理解每个域是如何定义的，因此它们可以做正确的事：

当你查询一个全文域时，会对查询字符串应用相同的分析器，以产生正确的搜索词条列表。

当你查询一个精确值域时，不会分析查询字符串，而是搜索你指定的精确值。


##### 测试分析器

有些时候很难理解分词的过程和实际被存储到索引中的词条，特别是你刚接触Elasticsearch。为了理解发生了什么，你可以使用analyze API来看文本是如何被分析的。在消息体里，指定分析器和要分析的文本。

```
#GET http://localhost:9200/_analyze
{
    "analyzer": "standard",
    "text": "Text to analyze"
}

```

结果中每个元素代表一个单独的词条：

```
{
    "tokens": [
        {
            "token": "text", 
            "start_offset": 0, 
            "end_offset": 4, 
            "type": "<ALPHANUM>", 
            "position": 1
        }, 
        {
            "token": "to", 
            "start_offset": 5, 
            "end_offset": 7, 
            "type": "<ALPHANUM>", 
            "position": 2
        }, 
        {
            "token": "analyze", 
            "start_offset": 8, 
            "end_offset": 15, 
            "type": "<ALPHANUM>", 
            "position": 3
        }
    ]
}

```

- token是实际存储到索引中的词条。
- start_ offset 和end_ offset指明字符在原始字符串中的位置。
- position指明词条在原始文本中出现的位置。

##### 指定分析器

当Elasticsearch在你的文档中检测到一个新的字符串域，它会自动设置其为一个全文字符串域，使用 标准 分析器对它进行分析。你不希望总是这样。可能你想使用一个不同的分析器，适用于你的数据使用的语言。有时候你想要一个字符串域就是一个字符串域，不使用分析，直接索引你传入的精确值，例如用户 ID 或者一个内部的状态域或标签。要做到这一点，我们必须手动指定这些域的映射。


##### 自定义分析器

虽然Elasticsearch带有一些现成的分析器，然而在分析器上Elasticsearch真正的强大之处在于，你可以通过在一个适合你的特定数据的设置之中组合字符过滤器、分词器、词汇单元过滤器来创建自定义的分析器。在分析与分析器我们说过，一个分析器就是在一个包里面组合了三种函数的一个包装器，三种函数按照顺序被执行：

字符过滤器
字符过滤器用来整理一个尚未被分词的字符串。例如，如果我们的文本是HTML格式的，它会包含像<p>或者<div>这样的HTML标签，这些标签是我们不想索引的。我们可以使用html清除字符过滤器来移除掉所有的HTML标签，并且像把&Aacute;转换为相对应的Unicode字符Á 这样，转换HTML实体。一个分析器可能有0个或者多个字符过滤器。

分词器
一个分析器必须有一个唯一的分词器。分词器把字符串分解成单个词条或者词汇单元。标准分析器里使用的标准分词器把一个字符串根据单词边界分解成单个词条，并且移除掉大部分的标点符号，然而还有其他不同行为的分词器存在。

例如，关键词分词器完整地输出接收到的同样的字符串，并不做任何分词。空格分词器只根据空格分割文本。正则分词器根据匹配正则表达式来分割文本。

词单元过滤器
经过分词，作为结果的词单元流会按照指定的顺序通过指定的词单元过滤器。词单元过滤器可以修改、添加或者移除词单元。我们已经提到过lowercase和stop词过滤器，但是在Elasticsearch 里面还有很多可供选择的词单元过滤器。词干过滤器把单词遏制为词干。ascii_folding过滤器移除变音符，把一个像"très”这样的词转换为“tres”。

ngram和 edge_ngram词单元过滤器可以产生适合用于部分匹配或者自动补全的词单元。




#### 5.5.11 文档展示-Kibana

Kibana是一个免费且开放的用户界面，能够让你对Elasticsearch 数据进行可视化，并让你在Elastic Stack 中进行导航。你可以进行各种操作，从跟踪查询负载，到理解请求如何流经你的整个应用，都能轻松完成。

一、解压缩下载的 zip 文件。

二、修改 config/kibana.yml 文件。

```
# 默认端口
server.port: 5601
# ES 服务器的地址
elasticsearch.hosts: ["http://localhost:9200"]
# 索引名
kibana.index: ".kibana"
# 支持中文
i18n.locale: "zh-CN"

```

三、Windows 环境下执行 bin/kibana.bat 文件。（首次启动有点耗时）

四、通过浏览器访问：http://localhost:5601。





## 6、ElasticsearchHead 插件安装

### 6.1 安装 Node

需要有 Node 环境

### 6.2 安装 grunt

```
npm install -g grunt-cli
```

![image-20230226161521344](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230226161521344.png)

```
# 切换淘宝镜像源
npm install -g cnpm --registry=https://registry.npm.taobao.org

# 安装grunt-cli
cnpm install -g grunt-cli

grunt -version
```





### 6.3 修改配置

修改 es 目录下 /config/elasticsearch.yml

```
cluster.name: my-application

node.name: my-node

network.host: 0.0.0.0
http.port: 9200

# 开启跨域
http.cors.enabled: true
http.cors.allow-origin: "*"
node.master: true
node.data: true
```

### 6.4 安装 Head

Git 下载地址：https://github.com/mobz/elasticsearch-head

选择 下载 zip

![image-20230226161658458](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230226161658458.png)

下载好后，解压，进入到 一层目录，找到 Gruntfile.js，加入一行代码：

```
hostname: '*',
```

![image-20230226162231126](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230226162231126.png)

![image-20230226162356766](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230226162356766.png)

去所在目录 执行 npm install

```
# 安装 （npm命令安装失败，使用cnpm安装）
cnpm install
# 启动
cnpm run start
```



### 6.5 启动 Head

进入到 Head 所在目录，grunt servcer



### 6.6 测试 Head

http://localhost:9100/



也可以插件安装

![image-20230226182942434](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230226182942434.png)

![image-20230226182919007](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230226182919007.png)



## 7、SpringData集成

Spring Data是一个用于简化数据库、非关系型数据库、索引库访问，并支持云服务的开源框架。其主要目标是使得对数据的访问变得方便快捷，并支持 map-reduce框架和云计算数据服务。Spring Data可以极大的简化JPA(Elasticsearch…)的写法，可以在几乎不用写实现的情况下，实现对数据的访问和操作。除了CRUD 外，还包括如分页、排序等一些常用的功能。

Spring Data Elasticsearch 介绍
Spring Data Elasticsearch基于Spring Data API简化 Elasticsearch 操作，将原始操作Elasticsearch 的客户端API进行封装。Spring Data为Elasticsearch 项目提供集成搜索引擎。Spring Data Elasticsearch POJO的关键功能区域为中心的模型与Elastichsearch交互文档和轻松地编写一个存储索引库数据访问层。



## 8、ElasticSearch8.x