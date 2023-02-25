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
node.master: false
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
node.master: false
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
chown -R es:es /01Java/es
```

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
network.host: 192.168.27.251
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
discovery.seed_hosts: ["192.168.27.251:9300","192.168.27.252:9300","192.168.27.253:9300"]
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
network.host: 192.168.27.252
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
discovery.seed_hosts: ["192.168.27.251:9300","192.168.27.252:9300","192.168.27.253:9300"]
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
network.host: 192.168.27.253
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
discovery.seed_hosts: ["192.168.27.251:9300","192.168.27.252:9300","192.168.27.253:9300"]
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

http://192.168.27.251:9200/_cat/nodes

Bug：遇到节点无法加入到集群情况

如果还是采用上述 配置，无法加入到集群，就自己形成了一个集群。把 node.master 改成 false，不让其自己形成一个集群。报错信息如下图。

```
# 是不是有资格 主节点
node.master: false
```



## 5、Elasticsearch进阶









## 6、ElasticsearchHead 插件安装







