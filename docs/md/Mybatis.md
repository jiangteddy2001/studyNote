# Mybatis

## 1、MyBatis相关的设计模式

1. Builder模式，例如SqlSessionFactoryBuilder、XMLConfigBuilder、XMLMapperBuilder、XMLStatementBuilder、CacheBuilder；

   SqlSessionFactory 的构建过程:Mybatis的初始化工作非常复杂，不是只用一个构造函数就能搞定的。所以使用了建造者模式，使用了 大 量的Builder，进行分层构造。

   

   其中 XMLConfigBuilder 在构建 Configuration 对象时，也会调用 XMLMapperBuilder 用于读取 *Mapper 文件，而XMLMapperBuilder会使用XMLStatementBuilder来读取和build所有的SQL语句。

   

2. 工厂模式，例如SqlSessionFactory、ObjectFactory、MapperProxyFactory；

   

   SqlSessionFactory使用的是工厂模式，该工厂没有那么复杂的逻辑，是一个简单工厂 模式

   

3. 单例模式，例如ErrorContext和LogFactory；

   

   在Mybatis中有两个地方用到单例模式，ErrorContext和LogFactory，其中ErrorContext是用在每个线程范围内的单例，用于记录该线程的执行环境错误信息，而LogFactory则是提供给整个Mybatis使用的日志工厂，用于获得针对项目配置好的日志对象。

   

4. 代理模式，Mybatis实现的核心，比如MapperProxy、ConnectionLogger，用的jdk的动态代理；还有executor.loader包使用了cglib或者javassist达到延迟加载的效果；

   

   代理模式可以认为是Mybatis的核心使用的模式，正是由于这个模式，我们只需要编写Mapper.java接 口，不需要实现，由Mybati s后台帮我们完成具体SQL的执行

   

5. 组合模式，例如SqlNode和各个子类ChooseSqlNode等；

6. 模板方法模式，例如BaseExecutor和SimpleExecutor，还有BaseTypeHandler和所有的子类例如IntegerTypeHandler；

   在Mybatis中，sqlSession的SQL执行，都是委托给Executor实现的

   其中的BaseExecutor就采用了模板方法模式，它实现了大部分的SQL执行逻辑，然后把以下几个方法交给子类定制化完成：

   ![image-20221203121450835](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212031214823.png)

7. 适配器模式，例如Log的Mybatis接口和它对jdbc、log4j等各种日志框架的适配实现；

8. 装饰者模式，例如Cache包中的cache.decorators子包中等各个装饰者的实现；

9. 迭代器模式，例如迭代器模式PropertyTokenizer；



