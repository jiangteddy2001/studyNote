# Java函数式编程

## 1 概述

### 1.1 函数式编程

函数式编程思想类似于我们数学中的函数，它主要关注的是对数据进行了什么操作。

优点：

- 代码简洁，开发快速
- 接近自然语言，易于理解
- 易于“并发编程”

## 2 Lambda表达式

### 2.1 基本概念

Lambda是JDK8中一个语法糖，它可以对某些匿名内部类的写法进行简化。它是函数式编程思想的一个重要体现，让我们不用关注是什么对象，而是关注我们对数据进行了什么操作。

核心原则：

可推导可省略

基本格式：

```
(参数列表) -> {代码}
```

例子

在创建线程并启动时可以使用匿名内部类的写法

```
new Thread(new Runnable() {
    @Override
    public void run() {
        System.out.println("线程中run方法被执行");
    }
}).start();
```

![image-20230409182816631](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230409182816631.png)

Lambda表达式只关注有什么参数

我们剪切一下，只有红线部分才需要用到。

![image-20230409183053042](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230409183053042.png)

```
剪切下来的代码
             () {
                System.out.println("线程中run方法被执行");
             }
```

把蓝色框的代码都删掉

![image-20230409183243006](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230409183243006.png)

![image-20230409184056263](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230409184056263.png)

优化后的代码

```java
new Thread(() -> {
        System.out.println("线程中run方法被执行");
}).start();
```

我们可以缩写到一行代码

![image-20230409184218681](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230409184218681.png)

### 2.2练习1

先写一个匿名内部类

```java
 int i = calculateNum(new IntBinaryOperator() {
            @Override
            public int applyAsInt(int left, int right) {
                return left + right;
            }
        });

 System.out.println(i);
```

同理，我们只关注参数

截取代码

![image-20230409192848483](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230409192848483.png)

优化后代码：

```
       int i = calculateNum((int left, int right) ->{
            return left + right;
        });
```

测试结果

![image-20230409193224400](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230409193224400.png)

### 2.3Lambda表达式快捷键

我们可以使用快捷键来使用Lambda表达式

快捷键：

ALT+回车

![image-20230409193512387](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230409193512387.png)

转化后

![image-20230409193542392](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230409193542392.png)

如果看不懂表达式，当然也可以转换回来

快捷键：ALT+回车

![image-20230409194314245](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230409194314245.png)

### 2.4 练习2 

```
public static void printNum(IntPredicate predicate) {
    int[] arr = {1,2,3,4,5,6,7,8,9,10};
    for (int i : arr) {
        if (predicate.test(i)) {
            System.out.println(i);
        }
    }
}
```

先写成匿名内部类

```
       printNum(new IntPredicate(){

            @Override
            public boolean test(int value) {
                return value%2==0;
            }
        });
```

同样截取代码后

```
 printNum((int value) ->{
            return value%2==0;
  });
```

### 2.5 练习3

```
public static <R> R typeConver(Function<String,R> function) {
    String str = "1234";
    R result = function.apply(str);
    return result;
}
```

写成匿名内部类

```
       Integer result = typeConver(new Function<String, Integer>() {
            @Override
            public Integer apply(String s) {
                return Integer.valueOf(s);
            }
        });

        System.out.println(result);
```

只关注参数和方法体

![image-20230415161244429](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230415161244429.png)

```
     Integer result = typeConver((String s) -> {
            return Integer.valueOf(s);
        });

```

### 2.6 练习4

```
   public static void foreachArr(IntConsumer consumer) {
        int[] arr = {1,2,3,4,5,6,7,8,9,10};
        for(int i : arr){
            consumer.accept(i);
        }
    }
```

写成匿名内部类

```
    foreachArr(new IntConsumer(){
            @Override
            public void accept(int value) {
                System.out.println(value);
            }
        });
```

![image-20230415162715629](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230415162715629.png)

转化后

```
    foreachArr((int value) -> {
            System.out.println(value);
        });
```

### 2.7 Lambda**省略规则**

- 参数类型可以省略
- 方法体只有一句代码时大括号return和唯一一句代码的分号可以省略
- 方法只有一个参数时，小括号可以省略
- **以上规则都可以省略不记，使用idea的快捷键 alt+enter**

![image-20230415163531232](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230415163531232.png)

![image-20230415163637720](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230415163637720.png)

省略之后

```
方法名(参数->返回值)
```

## 3 Stream流

### 3.1 概述

Java8的Stream使用的是函数式编程模式，如同它的名字一样，它可以被用来对集合或数组进行链状流式的操作，可以更方便的让我们对集合或数组操作。

### 3.2 案例数据准备

```xml
  <dependencies>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.16</version>
        </dependency>
    </dependencies>
```

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@EqualsAndHashCode  // 去重
public class Author {
    private Long id;
    private String name;
    private Integer age;
    private String intro;
    private List<Book> books;
}
```

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
@EqualsAndHashCode
public class Book {
    private Long id;
    private String name;
    private String category;
    private Integer score;
    private String intro;
}
```

```java
public class StreamDemo {

    public static void main(String[] args) {
        List<Author> authors=getAuthors();
        System.out.println(authors);

    }

    private static List<Author> getAuthors() {
        Author author = new Author(1L, "蒙多", 33, "一个祖安人", null);
        Author author2 = new Author(2L, "亚拉索", 15, "艾欧尼亚", null);
        Author author3 = new Author(3L, "易", 14, "黑色玫瑰", null);
        Author author4 = new Author(3L, "易", 14, "黑色玫瑰", null);

        List<Book> book1 = new ArrayList<>();
        List<Book> book2 = new ArrayList<>();
        List<Book> book3 = new ArrayList<>();

        book1.add(new Book(1L,"西游记","哲学,爱情", 80, "*"));
        book1.add(new Book(2L,"浪漫满屋","爱情,个人成长", 80, "**"));

        book2.add(new Book(3L,"七剑下天山","爱情,传记", 70, "****"));
        book2.add(new Book(3L,"七剑下天山","爱情,传记", 70, "****"));
        book2.add(new Book(4L,"名侦探柯南和他儿子","哲学", 70, "*****"));

        book3.add(new Book(5L,"三毛流浪记","个人成长", 60, "******"));
        book3.add(new Book(6L,"基督山伯爵","传记", 60, "********"));
        book3.add(new Book(6L,"基督山伯爵","传记", 60, "********"));

        author.setBooks(book1);
        author2.setBooks(book2);
        author3.setBooks(book3);
        author4.setBooks(book3);

        List<Author> authors = new ArrayList<>(Arrays.asList(author,author2,author3,author4));
        return authors;
    }
}

```

### 3.3 快速入门

#### 3.3.1 要求

获取到作家的集合，打印出年龄小于18的作家的名字并去重

#### 3.3.2 实现

![image-20230415172556207](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230415172556207.png)

```java
authors.stream()//把集合转换为流
               .distinct() //去重
               .filter(new Predicate<Author>(){

                   @Override
                   public boolean test(Author author) {
                       return false;
                   }
               });
```

根据年龄小于18修改方法

```java
 authors.stream()//把集合转换为流
               .distinct()
               .filter(new Predicate<Author>(){

                   @Override
                   public boolean test(Author author) {
                       return author.getAge() < 18;
                   }
               });
```

再增加一个打印名字的方法

```
authors.stream()//把集合转换为流
               .distinct()
               .filter(new Predicate<Author>(){

                   @Override
                   public boolean test(Author author) {
                       return author.getAge() < 18;
                   }
               })
               .forEach(new Consumer<Author>() {
                   @Override
                   public void accept(Author author) {
                       System.out.println(author.getName());
                   }
               });
```

优化后

```
 authors.stream()//把集合转换为流
               .distinct()
               .filter(author -> author.getAge() < 18)
               .forEach(author -> System.out.println(author.getName()));
```

我们使用debug模式依次查看

打断点

![image-20230415180419041](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230415180419041.png)

![image-20230415180521854](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230415180521854.png)

![image-20230415180551195](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230415180551195.png)

观察去重后

![image-20230415181438131](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230415181438131.png)

再下一步

![image-20230415181504191](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230415181504191.png)

测试结果

![image-20230415181800920](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230415181800920.png)

### 3.4 常用操作

#### 3.4.1 创建流

单列集合：`集合对象.stream()`

```
List<Integer> list = new ArrayList<>();
Stream<Integer> stream = list.stream();
```

![image-20230415183212347](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230415183212347.png)

数组：`Arrays.stream(数组)` 或 `Stream.of(数组)` 来创建

```
Integer[] arr = {1,2,3,4};
Stream<Integer> stream1 = Arrays.stream(arr);
Stream<Integer> stream2 = Stream.of(arr);
```

第一种方式

![image-20230415183905751](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230415183905751.png)

优化后

```
 private static void test02() {
        Integer[] arr = {1,2,3,4};
        Stream<Integer> stream = Arrays.stream(arr);
        stream.distinct().forEach(integer -> System.out.println(integer));
    }
```

第二种方式

```
    private static void test02() {
        Integer[] arr = {1,2,3,4};
        Stream<Integer> stream = Stream.of(arr);
        stream.distinct().forEach(integer -> System.out.println(integer));
    }
```

双列集合：转换成单例集合后再创建

```
Map<String, Integer> map = new HashMap<>();
map.put("蜡笔小新",19;
map.put("三毛",12);
map.put("木下秀吉",13);

```

```
Set<Map.Entry<String, Integer>> entries = map.entrySet();
        Stream<Map.Entry<String, Integer>> stream = entries.stream();

        stream.filter(new Predicate<Map.Entry<String, Integer>>() {
            @Override
            public boolean test(Map.Entry<String, Integer> entry) {
                return entry.getValue()>16;
            }
        }).forEach(new Consumer<Map.Entry<String, Integer>>() {
            @Override
            public void accept(Map.Entry<String, Integer> enEntry) {
                System.out.println(enEntry.getKey()+"==="+enEntry.getValue());
            }
        });
```

![image-20230415200037446](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230415200037446.png)

优化后

```
Set<Map.Entry<String, Integer>> entries = map.entrySet();
Stream<Map.Entry<String, Integer>> stream = entries.stream();

stream.filter(entry -> entry.getValue()>16)
      .forEach(enEntry -> System.out.println(enEntry.getKey()+"==="+enEntry.getValue()));
```

#### 3.4.2 中间操作

##### 1 filter

对流中的元素进行条件过滤，符合过滤条件的才能继续留在流中。

案例

```
private static void test04() {
        List<Author> authors = getAuthors();
        // 打印所有姓名长度大于1的作家姓名
        authors.stream()
                .filter(author -> author.getName().length()>1)
                .forEach(author -> System.out.println(author.getName()));
    }
```

##### 2 map

可以把流中的元素进行计算或转换。

```
private static void test05() {
        //打印所有作家的姓名
        List<Author> authors = getAuthors();
        authors.stream()
                .map(new Function<Author, String>() {
                    @Override
                    public String apply(Author author) {
                        return author.getName();
                    }
                })
                .forEach(new Consumer<String>() {
                    @Override
                    public void accept(String s) {
                        System.out.println(s);
                    }
                });
    }
```

![image-20230415204621063](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230415204621063.png)

优化后

```
  private static void test05() {
        //打印所有作家的姓名
        List<Author> authors = getAuthors();
        authors.stream()
                .map(author -> author.getName())
                .forEach(s -> System.out.println(s));
    }
```

![image-20230415204847721](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230415204847721.png)

计算

```
         authors.stream()
                .map(author -> author.getAge())
                .map(age -> age+10)
                .forEach(age -> System.out.println(age));
```

![image-20230415205921460](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230415205921460.png)

测试结果：

![image-20230415205946907](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230415205946907.png)

##### 3 **distinct**

去除流中的重复元素。

**注意：distinct方法是依赖Object的equals方法来判断是否是相同对象，所以需要重写equals方法。**

前提条件，已添加注解

![image-20230415210212835](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230415210212835.png)

```
 private static void test06() {
        //打印所有作家的姓名，不能重复
        List<Author> authors = getAuthors();
        authors.stream()
                .distinct()
                .forEach(author -> System.out.println(author.getName()));
    }
```

##### 4 **sorted**

对流中的元素进行排序。

**方式一：调用sorted()空参方法**

在比较的实体类上要实现Comparable接口，不然会报类型不匹配的异常。

```
public class Author implements Comparable<Author>{
    private Long id;
    private String name;
    private Integer age;
    private String intro;
    private List<Book> books;

    @Override
    public int compareTo(Author o) {
        return this.getAge() -o.getAge();
    }
}
```

![image-20230415213525959](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230415213525959.png)

如果发现排序结果不对，就换一下顺序

![image-20230415213833656](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230415213833656.png)



**方式二：在sorted方法中实现Comparator接口**

```
   private static void test07() {
        List<Author> authors = getAuthors();
        // 年龄降序、去重
        authors.stream()
                .distinct()
                .sorted((o1, o2) -> o2.getAge()-o1.getAge())
                .forEach(author ->  System.out.println(author.getAge()));
    }
```

##### 5 limit

可以设置流的最大长度，超出的部分将被抛弃。

```
private static void test08() {
        //降序，去重，打印年龄最大的前2个作家名字
        List<Author> authors = getAuthors();
        authors.stream()
                .distinct()
                .sorted((o1, o2) -> o2.getAge()-o1.getAge())
                .limit(2)
                .forEach(author -> System.out.println(author.getAge()+"==="+author.getName()));
    }
```

##### 6 skip

跳过流中的前n个元素，返回剩下的元素。

```
 private static void test09() {
        //打印除了年纪最大的其他作家姓名，去重，降序
        List<Author> authors = getAuthors();
        authors.stream()
                .distinct()
                .sorted((o1, o2) -> o2.getAge()-o1.getAge())
                .skip(1)
                .forEach(author -> System.out.println(author.getName()));
    }
```



##### 7 flatMap

map只能把一个对象转换成另一个对象来作为流中的元素。而flatMap可以把一个对象转换成多个对象作为流中的元素。

```
 private static void test10() {
        //打印所有书籍的名字，对重复的元素去重
        List<Author> authors = getAuthors();
        authors.stream()
                .flatMap(new Function<Author, Stream<Book>>() {
                    @Override
                    public Stream<Book> apply(Author author) {
                        return author.getBooks().stream();
                    }
                })
                .distinct()
                .forEach(new Consumer<Book>() {
                    @Override
                    public void accept(Book book) {
                        System.out.println(book.getName());
                    }
                });
    }
```

优化后

```
private static void test10() {
        //打印所有书籍的名字，对重复的元素去重
        List<Author> authors = getAuthors();
        authors.stream()
                .flatMap((Function<Author, Stream<Book>>) author -> author.getBooks().stream())
                .distinct()
                .forEach(book -> System.out.println(book.getName()));
    }
```

案例2

```
打印现有数据的所有分类，要求对分类进行去重。不能出现这种格式：哲学,爱情，要将它们拆开输出。
```

![image-20230416143406097](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230416143406097.png)

在IDEA里选择stream快捷键

然后简化，去掉多余代码

![image-20230416143708314](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230416143708314.png)

```
 private static void test11() {
        // 打印现有数据的所有分类，要求对分类进行去重。不能出现这种格式：哲学,爱情，要将它们拆开输出。
        List<Author> authors = getAuthors();
        authors.stream()
                .flatMap(author -> author.getBooks().stream())
                .distinct()
                .flatMap(book -> Arrays.stream(book.getCategory().split(",")))
                .distinct()
                .forEach(category -> System.out.println(category) );
    }
```

![image-20230416145629035](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230416145629035.png)

![image-20230416145655041](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230416145655041.png)

#### 3.4.3 终结操作

##### 1 **forEach**

对流中的元素进行遍历操作，我们通过传入的参数去指定对遍历到的元素进行什么具体操作。

案例：输出所有作家的名字

```
 private static void test12() {
        //输出所有作家的名字
        List<Author> authors = getAuthors();
        authors.stream()
                .map(author -> author.getName())
                .distinct()
                .forEach(name -> System.out.println(name));
    }
```

##### 2 count

获取当前流中元素的个数。

*//打印这些作家的所出书籍的数量*

```
  private static void test13() {
        //打印这些作家的所出书籍的数量,去重
        List<Author> authors = getAuthors();
        long count = authors.stream()
                .flatMap(author -> author.getBooks().stream())
                .distinct()
                .count();
        System.out.println(count);
    }
```



##### 3 **max&min**

获取流中的最值

```java
//分别获取这些作家所出书籍的最高分和最低分
   private static void test14() {
        //分别获取这些作家所出书籍的最高分和最低分
        List<Author> authors = getAuthors();
        Optional<Integer> max = authors.stream()
                .flatMap(author -> author.getBooks().stream())
                .map(book -> book.getScore())
                .max(new Comparator<Integer>() {
                    @Override
                    public int compare(Integer o1, Integer o2) {
                        return o1 - o2;
                    }
                });
        System.out.println(max.get());
    }
```

优化后

```java
 private static void test14() {
        //分别获取这些作家所出书籍的最高分和最低分
        List<Author> authors = getAuthors();
        Optional<Integer> max = authors.stream()
                .flatMap(author -> author.getBooks().stream())
                .map(book -> book.getScore())
                .max((score1, score2) -> score1 - score2);
        System.out.println(max.get());
  }
```

最终代码

```java
 private static void test14() {
        //分别获取这些作家所出书籍的最高分和最低分
        List<Author> authors = getAuthors();
        Optional<Integer> max = authors.stream()
                .flatMap(author -> author.getBooks().stream())
                .map(book -> book.getScore())
                .max((score1, score2) -> score1 - score2);
        Optional<Integer> min = authors.stream()
                .flatMap(author -> author.getBooks().stream())
                .map(book -> book.getScore())
                .min((score1, score2) -> score1 - score2);
        System.out.println(max.get());
        System.out.println(min.get());

    }
```

##### 4 **collect**

把当前流转换成一个集合。

list集合

使用collect里的工具类Collectors

![image-20230416163658467](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230416163658467.png)

```java
   private static void test15() {
        //获取一个存放所有作者名字的list集合
        List<Author> authors = getAuthors();
        List<String> namelist = authors.stream()
                .map(author -> author.getName())
                .collect(Collectors.toList());
        System.out.println(namelist);
    }
```

测试结果

![image-20230416165119298](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230416165119298.png)

set

```java
private static void test16() {
        //返回所有书的set
        List<Author> authors = getAuthors();
        Set<Book> bookSet = authors.stream()
                .flatMap(author -> author.getBooks().stream())
                .collect(Collectors.toSet());
        System.out.println(bookSet);
    }
```

map

```java
        //获取一个Map集合，map的key为作者名，value为List<Book>
        List<Author> authors = getAuthors();
        Map<String, List<Book>> map = authors.stream()
                .distinct()
                .collect(Collectors.toMap(new Function<Author, String>() {

                    @Override
                    public String apply(Author author) {
                        return author.getName();
                    }
                }, new Function<Author, List<Book>>() {
                    @Override
                    public List<Book> apply(Author author) {
                        return author.getBooks();
                    }
                }));
```

简化后

```java
  private static void test17() {
        //获取一个Map集合，map的key为作者名，value为List<Book>
        List<Author> authors = getAuthors();
        Map<String, List<Book>> map = authors.stream()
                 .distinct()
                .collect(Collectors.toMap(author -> author.getName(), author -> author.getBooks()));
    }
```



##### 5 查找和匹配

###### **（1）anyMatch**

判断是否有任意符合匹配条件的元素，结果为boolean类型。

```java
  private static void test18() {
        //判断是否有作家年龄在29以上
        List<Author> authors = getAuthors();
        boolean flag = authors.stream()
                .anyMatch(new Predicate<Author>() {
                    @Override
                    public boolean test(Author author) {
                        return author.getAge() > 29;
                    }
                });
        System.out.println(flag);
    }
```

优化后

```java
   private static void test18() {
        //判断是否有作家年龄在29以上
        List<Author> authors = getAuthors();
        boolean flag = authors.stream()
                .anyMatch(author -> author.getAge() > 29);
        System.out.println(flag);
    }
```

###### （2）allMatch

**判断是否都符合条件，如果都符合返回true，否则返回false**

```java
   private static void test19() {
        //判断是否所有作家年龄在18以上
        List<Author> authors = getAuthors();
        boolean flag = authors.stream()
                .allMatch(author -> author.getAge() > 18);
        System.out.println(flag);
    }
```

###### **（3）noneMatch**

判断流中的元素是否都不符合匹配条件，如果都不符合结果为true，否则结果为false。

```java
   private static void test20() {
        // 判断所有作家是否都没有超过100岁
        List<Author> authors = getAuthors();
        boolean flag = authors.stream()
                .noneMatch(author -> author.getAge() >= 100);
        System.out.println(flag);
    }
```

###### **（4）findAny**

**获取流中的任意一个元素。该方法没有办法保证获取到的一定是流中的第一个元素。**

```java
  private static void test21() {
        //获取任意一个年龄大于18的作家，如果存在就输出他的名字
        List<Author> authors = getAuthors();
        Optional<Author> any = authors.stream()
                .filter(author -> author.getAge() > 18)
                .findAny();
        //如果这个Optional中有元素，则执行方法，没有就不执行
        any.ifPresent(author -> System.out.println(author.getName()));
    }
```

###### **（5）findFirst**

**获取流中的第一个元素。**

```java
 private static void test22() {
        //获取一个年龄最小的作家，并输出他的姓名
        List<Author> authors = getAuthors();
        Optional<Author> first = authors.stream()
                .sorted((o1, o2) -> o1.getAge() - o2.getAge())
                .findFirst();
        first.ifPresent(author -> System.out.println(author.getName()));
    }
```

###### **（6）reduce归并**

对流中的数据按照你指定的计算方式计算出一个结果。（缩减操作）

reduce的作用是把stream中的元素给组合起来。我们可以传入一个初始值，它会按照我们的计算方式依次拿流中的元素和初始化值进行计算，计算结果再和后面的元素计算。

它内部的计算方式如下：

```
T result = identity;
for (T element : this stream)
	result = accumulator.apply(result, element)
return result;
```

案例1：

```
使用reduce求所有作者年龄的和
```

我们可以看到，reduce有3个参数，其中方法参数是个T

![image-20230417195850539](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230417195850539.png)

我们要计算年龄相加，所以我们要先保证这个类型是Int类型

![image-20230417200052833](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230417200052833.png)

```java
//先用匿名内部类实现
 private static void test23() {
        //使用reduce求所有作者年龄的和
        List<Author> authors = getAuthors();
        Integer sum = authors.stream()
                .map(author -> author.getAge())
                .reduce(0, new BinaryOperator<Integer>() {
                    @Override
                    public Integer apply(Integer result, Integer element) {
                        return result + element;
                    }
                });
        System.out.println(sum);
    }
```

简化后

```java
    private static void test23() {
        //使用reduce求所有作者年龄的和
        List<Author> authors = getAuthors();
        Integer sum = authors.stream()
                .distinct()
                .map(author -> author.getAge())
                .reduce(0, (result, element) -> result + element);
        System.out.println(sum);
    }
```

![image-20230417200921888](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230417200921888.png)

![image-20230417200935623](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230417200935623.png)

案例2 **使用reduce求所有作者中年龄的最大值**

```java
   private static void test24() {
        //使用reduce求所有作者中年龄的最大值
        List<Author> authors = getAuthors();
        Integer max = authors.stream()
                .map(author -> author.getAge())
                .reduce(Integer.MIN_VALUE, new BinaryOperator<Integer>() {
                    @Override
                    public Integer apply(Integer result, Integer element) {
                        return result < element ? element : result;
                    }
                });
        System.out.println(max);

    }
```

优化后

```java
  private static void test24() {
        //使用reduce求所有作者中年龄的最大值
        List<Author> authors = getAuthors();
        Integer max = authors.stream()
                .map(author -> author.getAge())
                .reduce(Integer.MIN_VALUE, (result, element) -> result < element ? element : result);
        System.out.println(max);
    }
```

案例3 **使用reduce求所有作者中年龄的最小值**

```java
  private static void test25() {
        //使用reduce求所有作者中年龄的最小值
        List<Author> authors = getAuthors();
        Integer min = authors.stream()
                .map(author -> author.getAge())
                .reduce(Integer.MAX_VALUE, (result, element) -> result > element ? element : result);
        System.out.println(min);
    }
```

如何去查看他的源码注释，点击进源码查看

![image-20230417203616887](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230417203616887.png)

改用匿名内部类，进入方法里面看

```java
private static void test26() {
        //使用reduce求所有作者中年龄的最小值
        List<Author> authors = getAuthors();
        Integer min = authors.stream()
                .map(author -> author.getAge())
                .reduce(Integer.MAX_VALUE, new BinaryOperator<Integer>() {
                    @Override
                    public Integer apply(Integer integer, Integer integer2) {
                        return null;
                    }
                });
        
    }
```

![image-20230417204306195](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230417204306195.png)

```java
boolean foundAny = false;
T result = null;
for (T element : this stream) {
	if(!foundAny) {
		foundAny = true;
		result = element;
	} else {
		result = accumulator.apply(result, element);
	}
}
return foundAny ? Optional.of(result) : Optional.empty();

```

利用这个重载形式，求作者年龄的最大值，不用传递初始值了。

![image-20230417204822459](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230417204822459.png)

```java
   private static void test26() {
        //使用reduce求所有作者中年龄的最小值
        List<Author> authors = getAuthors();
        Optional<Integer> max = authors.stream()
                .map(author -> author.getAge())
                .reduce( new BinaryOperator<Integer>() {
                    @Override
                    public Integer apply(Integer integer, Integer integer2) {
                        return null;
                    }
                });
        max.ifPresent(age -> System.out.println(age));
    }
```



### 3.5 stream注意事项

**惰性求值**：如果没有终结操作，中间操作是不会得到执行的。

**流是一次性的**：一旦一个流对象经过一个终结操作后，这个流就不能再被使用了，只能重新创建流对象再使用。

**不会影响原数据**：我们在流中可以对数据做很多处理，但正常情况下是不会影响原来集合中的元素的。

测试：

![image-20230417205335040](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230417205335040.png)

![image-20230417205413155](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230417205413155.png)



## 4 Optional

### 4.1 概述

我们在编写代码的时候出现最多的就是空指针异常，所以在很多情况下我们需要做各种非空的判断。尤其是对象中的属性还是一个对象的情况下，这种判断会更多。

```
Author author = getAuthor();
if(author != null) {
	System.out.println(author.getName());
}
```

过多的判断语句会让我们的代码显得臃肿，所以在JDK8中引入了Optional，养成使用Optional的习惯后，你可以写出更优雅的代码来避免空指针异常。

### 4.2   创建对象

Optional就好像是包装类，可以把我们的具体数据封装到Optional对象内部，然后我们去使用Optional中封装好的方法 操作封装进去的数据，就可以非常优雅的避免空指针异常。

原始方法

```java
public class OptionalDemo {

    public static void main(String[] args) {
        Author author = getAuthor();
        Optional<Author> optionalAuthor = Optional.ofNullable(author);
        optionalAuthor.ifPresent(author1 -> System.out.println(author1.getName()));
    }

    private static Author getAuthor() {
        Author author = new Author(1L, "蒙多", 17, "一个祖安人", null);
        return author;
    }
}
```

我们一般使用Optional的静态方法ofNullable来把数据封装成一个Optional对象，无论传入的参数是否为null都不会出现问题。

```
Author author = new Author();
Optional<Author> optionalAuthor1 = Optional.ofNullable(author);
```

上述方法改为

```java
   public static void main(String[] args) {
        Optional<Author> optionalAuthor = getOptionalAuthors();
        optionalAuthor.ifPresent(author1 -> System.out.println(author1.getName()));
    }

    private static Optional<Author> getOptionalAuthors() {
        Author author = new Author(1L, "蒙多", 17, "一个祖安人", null);
        return Optional.ofNullable(author);
    }
```

如果你可以确定一个对象不为空，则可以使用**Optional的静态方法of**来把数据封装成Optional对象。

```
Author author = new Author();
Optional<Author> optionalAuthor = Optional.of(author);
```

如果传入of()方法的对象为空，也会报空指针异常。

源码

![image-20230417213229998](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230417213229998.png)

```
private static Optional<Author> getOptionalAuthor2() {
    Author author = new Author(1L, "蒙多", 17, "一个祖安人", null);
    return author == null ? Optional.empty() : Optional.of(author);
}

```

### 4.3 安全消费值

我们获取到一个Optional对象后，肯定需要对其中的数据进行使用，这时候我们可以使用其**ifPresent**方法来消费其中的值。

这个方法会判断其内部封装的数据是否为空，不为空时才会执行具体的消费代码，这样使用起来就更加安全了。

```
 Optional<Author> optionalAuthor = getOptionalAuthors();
 optionalAuthor.ifPresent(author1 -> System.out.println(author1.getName())
```

### 4.4 安全获取值

如果我们期望安全获取值，不推荐使用get()方法获取，如果传入一个Null会报错

![image-20230417213927683](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230417213927683.png)

可以看一下get()方法的注释

![image-20230417214033999](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230417214033999.png)

而是用如下方法：

- orElseGet
  获取数据并且设置数据为空时的默认值。如果数据不为空则获取到该数据，如果为空则返回传入的参数来创建对象。

```java
 Optional<Author> optionalAuthor = getOptionalAuthors();
        optionalAuthor.orElseGet(new Supplier<Author>() {
            @Override
            public Author get() {
                return new Author();
            }
        });
```

![image-20230417214721646](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230417214721646.png)

优化后

```java
Optional<Author> optionalAuthor = getOptionalAuthors();
Author author = optionalAuthor.orElseGet(() -> new Author());
```

- orElseThrow

获取数据，如果数据不为空则获取数据，如果为空则根据传入的参数来创建异常抛出。

```java
Optional<Author> optionalAuthor = getOptionalAuthors();
        try {
            Author author = optionalAuthor.orElseThrow(new Supplier<Throwable>() {
                @Override
                public Throwable get() {
                    return new RuntimeException("运行时异常");
                }
            });
        } catch (Throwable throwable) {
            throwable.printStackTrace();
        }
```

优化

```
        try {
            Author author = optionalAuthor.orElseThrow(() -> new RuntimeException("运行时异常"));
        } catch (Throwable throwable) {
            throwable.printStackTrace();
        }
```

### 4.5 过滤

我们可以使用**isPresent**方法进行判断是否存在数据的判断。如果为空，返回值为false，如果不为空，返回值为true。
但这种方式不能体现Optional的好处，更推荐使用ifPresent方法。

```java
 private static void testFilter() {
        Optional<Author> optionalAuthors = getOptionalAuthors();
        Optional<Author> author = optionalAuthors.filter(new Predicate<Author>() {
            @Override
            public boolean test(Author author) {
                return author.getAge() > 18;
            }
        });
    }
```

简化后

```
  Optional<Author> optionalAuthors = getOptionalAuthors();
        optionalAuthors.filter(author1 -> author1.getAge() > 18).ifPresent(author -> System.out.println(author.getName()));
```

### 4.6 判断

我们可以使用**isPresent**方法进行判断是否存在数据的判断。如果为空，返回值为false，如果不为空，返回值为true。
但这种方式不能体现Optional的好处，更推荐使用ifPresent方法。

```java
  private static void testIsPresent() {
        Optional<Author> optionalAuthors = getOptionalAuthors();
        if(optionalAuthors.isPresent()){
            System.out.println(optionalAuthors.get().getName());
        }
    }
```

### 4.7 数据转换

Optional还提供了map可以让我们的数据进行转换，并且转换得到的数据也还是Optional包装好的，保证了我们的使用安全。

```java
 private static void testMap() {
        Optional<Author> authorOptional = getOptionalAuthors();
        Optional<List<Book>> bookList = authorOptional.map(author -> author.getBooks());
        bookList.ifPresent(books -> System.out.println(books));
    }
```

## 5 函数式接口

### 5.1 概述

只有一个抽象方法的接口我们称为函数接口。

JDK的函数式接口都加上了@FunctionalInterface注解进行标识。但是无论是否加上该注解，只要接口中只有一个抽象方法，就是函数式接口。

我们创建一个接口，如果没有抽象方法，只有注解会报错

![image-20230422170654586](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230422170654586.png)

如果加了一个抽象方法，不报错

![image-20230422170712967](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230422170712967.png)

如果加了2个抽象方法，会报错

![image-20230422170729479](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230422170729479.png)





### 5.2 常见的函数式接口

- Consumer 消费接口
  根据其中抽象方法的参数列表和返回值类型可以知道，我们可以在方法中对传入的参数进行消费。

![image-20230422165348320](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230422165348320.png)

选中这个类，按一个光标

![image-20230422165721045](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230422165721045.png)

可以看到这个类所在的包，结构如下：

![image-20230422170219145](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230422170219145.png)



- Function 计算转换接口
  根据其中抽象方法的参数列表和返回值类型知道，我们可以在方法中对传入的参数计算或转换，把结果返回。

![image-20230422204354016](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230422204354016.png)



- Predicate 判断接口
  根据其中抽象方法的参数列表和返回值类型知道，我们可以在方法中对传入的参数条件判断，返回判断结果。

![image-20230422204523991](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230422204523991.png)

- Supplier 生产型接口
  根据其中抽象方法的参数列表和返回值类型知道，我们可以在方法中创建对象，把创建好的对象返回。

![image-20230422204543079](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230422204543079.png)

注意规律，带Bi开头的有2个参数，BiFunction

![image-20230422204732750](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230422204732750.png)

### 5.3 常用的默认方法

**and**
我们在使用Predicate接口的时候可能需要进行判断条件的拼接，而and方法相当于是使用&&来拼接两个判断条件。

案例1 *//打印作家中年龄大于17并且姓名的长度大于1的作家*

```

```





**or**
我们在使用Predicate接口的时候可能需要进行判断条件的拼接。而or方法相当于是使用 || 来拼接两个判断条件。





**negate**
Predicate接口中的方法。negate方法相当于是在判断添加前面加了个 !，表示取反





## 6 方法引用

我们在使用lambda时，如果方法体中只有一个方法的调用的话，我们可以用方法引用进一步简化代码。

推荐用法：我们在使用lambda时不需要考虑什么时候用方法引用，方法引用的格式是什么。我们只需要在写完lambda方法发现方法体只有一行代码，并且在方法的调用时使用快捷键尝试是否能转换成方法引用即可。

基本格式：类名或对象名::方法名















## 7 高级用法