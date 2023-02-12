# PostgreSQL

## 1 概述

### 1.1 简介

PostgreSQL是一个开源的，特性非常齐全的对象-关系型数据库，同时还支持NoSql的文档型存储。

### 1.2 特征

- 多版本并发控制：PostgreSQL使用多版本并发控制（MVCC，Multiversion concurrency control）系统进行并发控制，该系统向每个用户提供了一个数据库的"快照"，用户在事务内所作的每个修改，对于其他的用户都不可见，直到该事务成功提交。

- 数据类型：包括文本、任意精度的数值数组、JSON 数据、枚举类型、XML 数据等。

- 全文检索：通过 Tsearch2 或 OpenFTS。

- NoSQL：JSON，JSONB，XML，HStore 原生支持，甚至 NoSQL 数据库的外部数据包装器。

- 数据仓库：能平滑迁移至同属 PostgreSQL 生态的 GreenPlum，DeepGreen等，使用 FDW（Foreign data wrappers） 进行 ETL（Extract-Transform-Load）。

- 函数：通过函数，可以在数据库服务器端执行指令程序。

- 索引：用户可以自定义索引方法，或使用内置的 B 树，哈希表与 GiST（Generalized Search Tree） 索引。

- 触发器：触发器是由SQL语句查询所触发的事件。如：一个INSERT语句可能触发一个检查数据完整性的触发器。触发器通常由INSERT或UPDATE语句触发。

- 规则：规则（RULE）允许一个查询能被重写，通常用来实现对视图（VIEW）的操作，如插入（INSERT）、更新（UPDATE）、删除（DELETE）。

- 继承：PostgreSQL实现了表继承，一个表可以从0个或者多个其他表继承，而对一个表的查询则可以引用一个表的所有行或者该表的所有行加上它所有的后代表。
  

### 1.3 约定

PostgreSQL使用一种客户端/服务器的模型

服务器进程（postgre）：管理数据库文件、接受来自客户端应用与数据库的联接并且代表客户端在数据库上执行操作

客户端应用：可以是一个面向文本的工具， 也可以是一个图形界面的应用，或者是一个通过访问数据库来显示网页的网页服务器，或者是一个特制的数据库管理工具


PostgreSQL支持标准的SQL类型`int`、`smallint`、`real`、`double precision`、`char(*N*)`、`varchar(*N*)`、`date`、`time`、`timestamp`和`interval`，还支持其他的通用功能的类型和丰富的几何类型。

### 1.4 优点

PostgreSQL的主要优点：

1.PostgreSQL是完全免费的，它是BSD协议。PostgreSQL数据库将不受其他公司的控制。oracle数据库是商业数据库，不是开放的。尽管MySQL数据库是开源的，但由于SUN被Oracle收购，因此它现在基本上由Oracle控制。实际上，在收购SUN之前，MySQL中最重要的InnoDB引擎也由Oracle控制。在MySQL中InnoDB引擎中的许多重要数据都放在InnoDB引擎中。因此，如果MySQL的市场范围与oracle数据库的市场范围冲突，oracle公司肯定会牺牲MySQL，这是毫无疑问的。

2.有很多与PostgreSQl合作的开源软件，还有很多分布式集群软件，例如pgpool，pgcluster，slony，plploxy等。它很容易实现解决方案，例如读写分离，负载平衡和数据级别拆分，这在MySQL下比较困难。

3.PostgreSQL源代码写得很清楚，可读性比MySQL好。因此，许多公司都使用基本PostgreSQL进行二次开发。

4.PostgreSQL在许多方面都比MySQL强，例如复杂的SQL执行，存储过程，触发器和索引。同时，PostgreSQL是多进程的，而MySQL是线程化的。尽管在并发性不高时MySQL的处理速度很快，但是在并发性高时，MySQL的整体处理性能不如在具有多核的单台计算机上的PostgreSQL更好。原因是MySQL线程无法充分利用CPU的功能。



## 2 安装



