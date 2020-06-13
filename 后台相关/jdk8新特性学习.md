# JDK新特性学习

## 一、lambda表达式

lambda表达式使用前提：

>方法的参数或局部变量类型必须为接口才能使用lambda
>
>接口中有且仅有一个抽象方法，@FunctionalInterface可以帮助检测接口中是否仅有一个方法

### 1.1 lambda表达式的基本语法

java8中引入了一个新的操作符 “ -> ” 该操作符称为箭头操作符或lambda操作符。lambda表达式体现的是函数式编程思想，只需要将要执行的代码放到函数中（函数就是类中的方法）。用于简化匿名内部类的书写。

>箭头操作符将lambda表达式拆分成两部分
>
>左侧：lambda表达式的参数列表
>
>右侧：lambda表达式中所需执行的功能，即lambda体

根据参数个数及有无返回值lambda表达式语法可有以下几种形式

1. 无参数，无返回值

   ```java
   new Thread(()->{
       System.out.println("hello world");
   }).start();
   ```

2. 有一个参数并且无返回值

   ```java
   //consumer消费者函数式接口
   Consumer<String> consumer = e->System.out.println("123456"+e);
   consumer.accept("789");
   ```

3. 有两个以上的参数，有返回值，并且 Lambda 体中有多条语句

   ```java
   Comparator<Integer> com = (x, y) -> {
       System.out.println("函数式接口");
       return Integer.compare(x, y);
   };
   int compare = com.compare(100, 244);
   System.out.println(compare);
   ```

4. 若 Lambda 体中只有一条语句， return 和 大括号都可以省略不写

   ```java
   Comparator com = (x, y) -> Integer.compare(x, y);
   ```

5. Lambda 表达式的参数列表的数据类型可以省略不写，因为JVM编译器通过上下文推断出，数据类型，即“类型推断”

   ```java
   (Integer x, Integer y) -> Integer.compare(x, y);
   ```


**可省略小节**

* 小括号内参数类型可以省略
* 如果小括号内仅有一个参数则小括号可以省略
* 如果大括号内仅有一个语句，可以同时省略大括号、return关键字及语句分号

### 1.2 lambda表达式实现原理

匿名内部类在编译后会生成一个$+数字的类，生成一个class文件。

lambda表达式在编译后会生成一个私有的静态方法，将lambda表达式中的代码放到新增的静态方法中。调用时会生成一个匿名内部类，这个匿名内部类会实现接口重写里面的方法。

lambda表达式在运行时产生一个类

1. 在类中新增一个方法，这个方法的方法体就是lambda表单式中的代码
2. 还会形成一个匿名内部类，实现接口重写抽象方法
3. 在接口的重写方法中调用新生成的方法

### 1.3 lambda与匿名内部类对比

lambda表达式和匿名内部类在使用上的区别

1. 所需的类型不一样

   匿名内部类所需的类型可以是类，抽象类，接口

   Lambda表达式所需的类型必须是接口

2. 抽象方法的数量不同

   匿名内部类所需的接口中抽象方法数量可以是任意个

   lambda表达式所需的接口只能有一个抽象方法

3. 实现原理不同

   匿名内部类是在编译后会生成class

   lambda表达式时在程序运行时动态生成class

### 1.4 接口增强特性

jdk8对接口进行了增强

jdk8以前的接口

```java
interface 接口名 {
    静态常类;
    抽象方法;
}
```

jdk8以后的接口

```java
interface 接口名 {
    静态常类;
    抽象方法;
    默认方法;
    静态方法;
}
```

很多时候一个接口中的某个方法可能在多个实现类中有相同的实现，jdk8之前在每个实现类中都需要完成该接口方法的实现，jdk8之后提供了默认方法，实现类可以不重写默认方法，也可以根据需要重写默认方法，使代码可重用性得到提升。

默认方法定义格式

```java
public default void test2(){
    System.out.println("接口A默认方法");
};
```

接口中的方法不可以重写，调用时使用接口类名直接调用相应静态方法

静态方法定义格式

```java
public static void test1(){
    System.out.println("接口AA静态方法");
}
```

### 1.5 常用内置函数式接口

lambda表达式使用时不关心接口名、抽象方法名，只关心抽象方法的参数列表和返回值。因此为了使lambda表达式更方便使用，JDK提供了大量的函数式接口。

JDK中有以下四个常用的函数式接口

| Interface     | function method   | 说明                             |
| ------------- | ----------------- | -------------------------------- |
| Supplier<T>   | T get()           | 返回类型为泛型T                  |
| Function<T,R> | R apply(T t)      | 接收参数类型为T，返回参数类型为R |
| Consumer<T>   | void accept(T t)  | 接收一个泛型参数，无返回值       |
| Predicate<T>  | boolean test(T t) | 接收一个泛型参数                 |

1. Supplier<T>接口，方法T get()

   无参数有返回值的供给型接口

2. Consumer<T>接口，方法R apply(T t)

   有参数无返回值的消费型接口

3. Function<T,R>接口，方法R apply(T t)

   接受一个参数加工处理后返回一个结果的加工型接口

4. Predicate<T>接口，方法boolean test(T t)

   接收一个参数返回一个布尔值的判断型接口

### 1.6 方法引用

lambda表达式存在冗余场景，如果lambda表达式中仅调用一个静态的方法，可以使用方法引用来代替lambda表达式。（使用方法引用相当于让这个指定的方法去重写接口的抽象方法，到时候调用接口的抽象方法就是调用这个静态方法）

**方法引用的格式**

符号表示：::

符号说明：双冒号为方法引用运算符，而它所在的表达式被称为方法引用

应用场景：如果lambda表达式所要实现的方案，已经有其他方法存在相同的方法，那么则可以使用方法引用

**常见的方法引用的方式**

1. instanceName::methodName    对象::方法名

   ```java
   public static void test1(){
       Date now = new Date();
   //        Supplier<Long> supplier = ()->{
   //            return now.getTime();
   //        };
       //使用方法引用
       Supplier<Long> supplier = now::getTime;
       Long aLong = supplier.get();
       System.out.println("aLong= "+aLong);       
   }
   ```

   注意

   1. 被引用的方法，参数要和接口种的抽象方法的参数一样
   2. 当接口的抽象方法有返回值时，被引用的方法也必须有相同返回值

2. ClassName::staticMethodName    类名::静态方法

   ```java
   public static void test2(){
   //        Supplier<Long> supplier = () ->{
   //            return System.currentTimeMillis();
   //        };
       Supplier<Long> supplier = System::currentTimeMillis;
       Long time = supplier.get();
       System.out.println("time = "+time);
   }
   ```

3. ClassName::methodName    类名::实例方法

   类名::实例方法实际上会将第一个参数作为方法的调用者。

   ```java
   public static void test3(){
   //        Function<String,Integer> function = (str)->{
   //            return str.length();
   //        };
       Function<String,Integer> function = String::length;
       int account = function.apply("hello");
       System.out.println(account);
   
       //两个参数的提供者接口
       BiFunction<String,Integer,String> function1 = String::substring;
       String s = function1.apply("helloworld",3);
       System.out.println(s);
   }
   ```

4. ClassName::new    类名::new调用构造器

   ```java
   public static void test4(){
   //        Supplier<Map<String,Integer>> supplier = ()->{
   //            return new HashMap<>();
   //        };
       Supplier<Map<String,Integer>> supplier = HashMap::new;
       Map<String,Integer> map = supplier.get();
       System.out.println("map: "+map);
   }
   ```

5. TypeName[]::new    String[]::new调用数组的构造器

   数组也是object的子类，所以同样具有构造器，只是语法稍有不同

   ```java
   public static void test5(){
   //        Function<Integer,int[]> function = (length)->{
   //            return new int[length];
   //        };
       Function<Integer,int[]> function = int[]::new;
       int[] arr = function.apply(10);
       System.out.println(Arrays.toString(arr));
   }
   ```

小结：

方法引用是对lambda表达式符合特定情况下的一种缩写，它使得我们的lambda表达式更加精简，也可以理解为lambda表达式的缩写形式，不过要注意的是方法引用只能引用已经存在的方法。

## 二、集合stream流式操作

### 2.1 Stream流简介

Java 8 引入，位于java.util.stream包中，与 java.io 包里的输入输出流 InputStream 和 OutputStream 是不同的概念。**Stream API 借助 Lambda 表达式，可以提高编程效率和程序可读性**。

Stream流不是一种数据结构，不保存数据，而是对数据的加工处理，Stream流可以看作是流水线上的一个工序，在流水线上通过多个工序将一个原材料加工成目标产品。

```java
public static void main(String[] args) {
    //需求:1.拿到所有姓张的  2.拿到名字长度等于三的  3.打印数据
    ArrayList<String> list = new ArrayList<>();
    Collections.addAll(list,"张三","李四","张三丰","张强","李四光");      
    //采用流式操作
    list.stream().filter((s)->{
        return s.startsWith("张");
    }).filter((s)->{
        return s.length()==3;
    }).forEach(System.out::println);
}
```

### 2.2 获取Stream流的两种方式

1. 根据Collection获取流

   Collection接口中有一个默认的方法，default Stream<E> stream()，实现了Collection接口的集合类都可以通过调用stream方法获取流，Map接口可以通过分别获取键值的集合来获取键值的流，或者获取实体集合的流

   ```java
   List<String> list = new ArrayList<>();
   Stream<String> stringStream = list.stream();
       
   Map<String,String> map = new HashMap<>();
   Stream<String> keyStream = map.keySet().stream();
   Stream<String> valueStream = map.values().stream();
   Stream<Map.Entry<String,String>> entryStream = map.entrySet().stream();
   ```

2. Stream类的静态of方法获取流

   基本类型数组会被看成是一个元素进行操作，而无法对其中的元素进行操作。

   ```java
   Stream<String> stream = Stream.of("a","b","c");
   
   String[] strs = {"a","b","c"};
   Stream<String> stream2 = Stream.of(strs);
   ```

### 2.3 Stream常用方法和注意事项

通常我们根据方法的返回值类型将stream类的方法分为两种

* 终结方法：返回值类型不再是Stream类型的方法，不再支持链式调用。
* 非终结方法：返回值类型仍然是Stream类型的方法，支持链式调用。

| 方法名   | 方法作用           | 返回值类型 | 方法种类 |
| -------- | ------------------ | ---------- | -------- |
| count    | 统计个数           | long       | 终结     |
| forEach  | 遍历处理           | void       | 终结     |
| filter   | 过滤               | Stream     | 函数拼接 |
| limit    | 取前几个           | Stream     | 函数拼接 |
| skip     | 跳过前几个         | Stream     | 函数拼接 |
| map      | 映射               | Stream     | 函数拼接 |
| concat   | 组合               | Stream     | 函数拼接 |
| distinct | 去重               | Stream     | 函数拼接 |
| sorted   | 排序               | Stream     | 函数拼接 |
| reduce   | 归纳总结得到一个值 | Stream     | 函数拼接 |
| concat   | 合并两个流为一个   | Stream     | 函数拼接 |

**注意事项**

1. Stream只能操作一次
2. Stream方法返回的是最新的流
3. Stream不调用终结方法，中间的操作不会执行

### 2.4 收集stream流中的结果

stream流中必须调用终结方法才会执行中间的操作，有时候我们希望把流中的处理结果保存起来，stream提供了collect方法完成对数据的保存。

1. 收集Stream流中的数据到集合中

   ```java
   public static void test1(){
       Stream<String> stream = Stream.of("a","b","c");
           //将流中的数据收集到集合中
   //        List<String> collect = stream.collect(Collectors.toList());
   //        System.out.println("collect = "+collect);
   
       //收集到指定的集合中
   	ArrayList<String> collect=stream.collect(Collectors.toCollection(ArrayList::new));
       System.out.println(collect);
   }
   ```

2. 收集Stream流中的数据到数组中

   ```java
   public static void test2(){
       Stream<String> stream = Stream.of("a","b","c");
           //收集到数组中(转成object对象不方便)
   //        Object[] objects = stream.toArray();
   //        for (Object o : objects){
   //            System.out.println("o = "+o);
   //        }
   
       String[] strings = stream.toArray(String[]::new);
       for (String s :strings){
           System.out.println("string = "+s +" 长度 = "+s.length());
       }
   }
   ```

### 2.5 Stream流中数据操作

流操作主程序

```java
public static void main(String[] args) {
        Stream<Student> studentStream = Stream.of(
                new Student("张三",18,172.0),
                new Student("张三丰",18,172.0),
                new Student("李四",18,168.0),
                new Student("李四光",23,159.0),
                new Student("老子",23,180.0)
        );
        test1(studentStream);
    }
```

1. 对stream流中的结果进行聚合计算

   ```java
   public static void test1(Stream<Student> studentStream){
       //获取最大值
       //Optional<Student> max = studentStream.collect(Collectors.maxBy((s1, s2) -> s1.getAge() - s2.getAge()));
       //System.out.println("最大值 "+ max.get().toString());
       //获取最小值
       //Optional<Student> min = studentStream.collect(Collectors.minBy((s1, s2) -> s1.getAge() - s2.getAge()));
       //System.out.println("最小值 "+ min.get());
       //求和
       //Integer sum = studentStream.collect(Collectors.summingInt(s -> s.getAge()));
       //System.out.println("总和 "+ sum);
       //求平均值
       //Double avg = studentStream.collect(Collectors.averagingDouble(s -> s.getHigh()));
       //System.out.println("平均身高 "+ avg);
       //统计数量
       Long total = studentStream.collect(Collectors.counting());
       System.out.println("统计数量 "+ total);
   }
   ```

2. 对stream流中的数据进行分组

   ```java
   public static void test2(Stream<Student> studentStream){
       //年龄大于20的为一组小于20的为一组
       Map<String, List<Student>> map = studentStream.collect(Collectors.groupingBy(s -> {
           if (s.getAge() > 20) {
               return "大于20";
           } else {
               return "小于20";
           }
       }));
       map.forEach((k,v)->{
           System.out.println(k + "::" + v);
       }); 
   }
   ```

3. 对stream流中的数据进行多级分组

   ```java
   public static void test3(Stream<Student> studentStream){
           //年龄大于20的为一组小于20的为一组，然后按
           Map<Integer, Map<String, List<Student>>> map = studentStream.collect(Collectors.groupingBy(Student::getAge, Collectors.groupingBy(s -> {
               if (s.getHigh() > 170) {
                   return "超过一米七";
               } else {
                   return "不超过一米七";
               }
           })));
           map.forEach((k,v)->{
               System.out.println(k);
               v.forEach((k2,v2)->{
                   System.out.println(k2 + "::" +v2);
               });
           });
       }
   ```

4. 对流中的数据进行分区

   ```java
   public static void test4(Stream<Student> studentStream){
           //年龄大于20的为一组小于20的为一组，然后按
           Map<Boolean, List<Student>> map = studentStream.collect(Collectors.partitioningBy(s -> {
               return s.getHigh() > 170;
           }));
           map.forEach((k,v)->{
               System.out.println(k + "::"+ v);
           });
       }
   ```

5. 对流中的数据进行拼接

   ```java
   public static void test5(Stream<Student> studentStream){
           //年龄大于20的为一组小于20的为一组，然后按
           String names = studentStream.map(Student::getName).collect(Collectors.joining("__"));
           System.out.println(names);
       }
   ```

### 2.6 并行的stream流

为了提高流中数据的处理效率，流处理提供了并行计算的方法，需要注意的是并不是所有的场景都可以进行并行计算，要考虑线程安全问题。解决线程安全问题可以使用synchronize锁，或者使用线程安全的集合。

串行计算的流实例

```java
public static void test1(){
        Stream.of(1,2,3,4,5,6).filter(s->{
            System.out.println(Thread.currentThread()+"---"+s);
            return s>3;
        }).count();
    }
```

**获取并行流的两种方式**

1. 直接获取并行流

   ```java
   public static void test2(){
       List<String> list = new ArrayList<>();
       Stream<String> stream = list.parallelStream();
   }
   ```

2. 将串行流转换为并行流

   ```java
   public static void test3(){
       List<String> list = new ArrayList<>();
       Stream<String> stream = list.stream().parallel();
   
   }
   ```

**并行流实现原理fork/join框架**

**Fork/Join原理-分治法**

ForkJoinPool主要用来使用分治法来解决问题。典型的应用比如快速排序算法。ForkJoinPool需要使用相对少的线程来处理大量的任务。比如要对1000万个数据进行排序，那么就会将这个任务分成两个500万的排序任务和一个针对这两个数组500万数据的合并任务。以此类推，对500万的数据进行相同的分割，到最后会设置一个阈值来规定数据规模到多少时停止这样的分割。比如，当元素数量小于10时会停止分割，转而使用插入排序对他们进行排序。那么到最后，所有的任务加起来大概2000000+个，问题的关键在于，对一个任务而言，只有当它所有的子任务完成后它才能够被执行。

**Fork/Join原理-工作窃取算法**

Fork/Join最核心的地方就是利用现代硬件设备多核，再操作时会有空闲的cpu，那么如何利用好这个空闲的cpu就成了提供性能的关键，而这里我们要提到的工作窃取算法就是整个fork/join框架的核心理念Fork/join工作窃取算法是指某个线程从其他队列里窃取任务来执行。

##  三、新的时间日期API

### 3.1 新旧时间API介绍

**旧版本日期时间API存在的问题**

1. 设计较差：在java.util和java.sql的包中都有日期类，java.util.Date同时包含日期和时间，而java.sql.Date仅包含日期。此外用于格式化和解析的类再java.text包中定义
2. 非线程安全：java.util.Date是非线程安全的，所有的日期都是可变的，这是java日期类最大的问题之一。
3. 时区处理麻烦：日期类不提供国际化，没有时区支持，因此java引入了java引入了java.util.Calendar和java.util.TimeZone类，但它们同样存在上述所有问题。

**新日期时间API介绍**

jdk8中新增了一个全新的日期时间API，这套API设计合理，是线程安全的。新的日期及时间API位于java.time包中，一些关键类如下

| 类名              | 描述                                | 格式                    |
| ----------------- | ----------------------------------- | ----------------------- |
| LocalDate         | 表示日期，包含年月日                | 2020-02-23              |
| LocalTime         | 表示时间，包含时分秒                | 16:38:54.158549300      |
| LocalDateTime     | 表示日期时间，包含年月日时分秒      | 2020-02-23T15:33:56.750 |
| DateTimeFormatter | 日期时间格式化类                    |                         |
| instance          | 时间戳，表示一个特定的时间瞬间      |                         |
| Duration          | 用于计算两个时间（LocalTime）的距离 |                         |
| Preiod            | 用于计算两个日期（LocalDate）的距离 |                         |
| ZoneDateTime      | 包含时区的时间                      |                         |



java中使用的历法是ISO 8601日历系统，它是世界民用历法，也就是我们所说的公历，平年每年365天，闰年每年366天，此外java 8 还提供了四套其他历法，分别为：

1. ThaiBuddhisDate：泰国佛教历
2. MinguoDate：中华民国历
3. JapaneseDate：日本历
4. HijtahDate：伊斯兰历

### 3.2 日期时间类

1. LocalDate类,可以获取单独的年月日

   ```java
   public static void testLocalDate(){
           LocalDate now = LocalDate.now();
           System.out.println("now = "+now);
           //创建指定的日期
           LocalDate date = LocalDate.of(2020,2,29);
           System.out.println("date = "+date);
           //获取单独的年月日
           System.out.println(now.getYear());
           System.out.println(now.getMonth());
           System.out.println(now.getDayOfMonth());
       }
   ```

2. LocalTime类，可以获取单独的时分秒纳秒

   ```java
   public static void testLocalTime(){
           LocalTime time = LocalTime.of(13,26,39);
           System.out.println("time = " +time);
   
           LocalTime now = LocalTime.now();
           System.out.println("now = "+now);
   
           System.out.println(now.getHour());
           System.out.println(now.getMinute());
           System.out.println(now.getSecond());
           System.out.println(now.getNano());
       }
   ```

3. LocalDateTime类，可以获取单独的年月日时分秒

   ```java
    public static void testLocalDateTime(){
           LocalDateTime dateTime = LocalDateTime.of(2020,2,20,20,20,20);
           System.out.println("dateTime = "+dateTime);
   
           LocalDateTime now = LocalDateTime.now();
           System.out.println("now = "+now);
           
           System.out.println(now.getYear());
           System.out.println(now.getMonth());
           System.out.println(now.getDayOfMonth());
           System.out.println(now.getHour());
           System.out.println(now.getMinute());
           System.out.println(now.getSecond());
       }
   ```

### 3.3 日期时间格式化与解析

jdk8新的日期时间api提供了新的格式化类，DateTimeFormatter.可以使用JDK自带的时间格式或者自定义的时间格式来格式化与解析日期时间。

```java
public static void test1(){
        //创建一个日期
        LocalDateTime now = LocalDateTime.now();
        // 格式化
        // 指定时间格式
        // JDK自带的时间格式
        DateTimeFormatter formatter = DateTimeFormatter.ISO_DATE_TIME;
        //自定义日期格式
        DateTimeFormatter formatter1 = DateTimeFormatter.ofPattern("yyyy年MM月dd HH时mm分ss秒");

        String format = now.format(formatter);
        String format1 = now.format(formatter1);
        System.out.println(format);
        System.out.println(format1);

        //解析
        LocalDateTime parse = LocalDateTime.parse("2020年02月23 21时16分47秒", formatter1);
        System.out.println(parse);
    }
```

### 3.4 Instant时间戳

主要是给程序统计时间使用，可以操作纳秒

```java
public static void test(){
        Instant now = Instant.now();
        System.out.println("当前时间戳 = " + now);
        //获取从1970年1月1日00：00：00的秒
        System.out.println(now.getNano());
        System.out.println(now.getEpochSecond());
        System.out.println(now.toEpochMilli());
        System.out.println(System.currentTimeMillis());

        Instant instant = Instant.ofEpochSecond(5);
        System.out.println(instant);
        //Instant内部保存了秒和纳秒，一般用于统计程序耗时
        //对instant操作只能加减秒、毫秒，纳秒
        System.out.println(now.plusSeconds(10));
        System.out.println(now.plusMillis(20));
        System.out.println(now.plusNanos(100));
    }
```

### 3.5 计算时间差的类

1. Duration：用于计算两个世界（LocalTime）的距离

   ```java
   public static void test1(){
           LocalTime now = LocalTime.now();
           LocalTime localTime = LocalTime.of(10,15,25);
   
           Duration duration = Duration.between(localTime,now);
           System.out.println("相差天：" + duration.toDays());
           System.out.println("相差小时：" + duration.toHours());
           System.out.println("相差分钟：" + duration.toMinutes());
           System.out.println("相差秒：" + duration.getSeconds());
           System.out.println("相差毫秒：" + duration.toMillis());
       }
   ```

2. Period：用于计算两个日期（LocalDate）的距离

   ```java
   public static void test2(){
           LocalDate now = LocalDate.now();
           LocalDate date = LocalDate.of(1985,9,23);
   
           Period period = Period.between(date,now);
           System.out.println("相差年："+period.getYears());
           System.out.println("相差月："+period.getMonths());
           System.out.println("相差日："+period.getDays());
   
       }
   ```

### 3.6 时间校准

有时候我们可能需要获取例如：将日期调整到“下个月的第一天”等操作，可以通过时间校正器来完成

* TemporalAdjuster：时间校正器 (自定义调整时间)
* TemporalAdjusters：通过静态方法提供了大量常用的TemporalAdjuster的实现

```java
//TemporalAdjuster：自定义调整时间
public static void test1(){
        //得到下个月的第一天
        LocalDateTime now = LocalDateTime.now();
        TemporalAdjuster firsDayOfNextMonth = (temporal) ->{
            LocalDateTime dateTime = (LocalDateTime)temporal;
            return dateTime.plusMonths(1).withDayOfMonth(1);
        };
        System.out.println(now.with(firsDayOfNextMonth));
    }
```

```java
public static void test2(){
        LocalDateTime now = LocalDateTime.now();
        //jdk自带的调整器
        //1.调整到下个月的第一天
        System.out.println(now.with(TemporalAdjusters.firstDayOfNextMonth()));
        //2.调整到下一年的第一天
        System.out.println(now.with(TemporalAdjusters.firstDayOfNextYear()));
    }
```

### 3.7 设置时区

java8中加入了对时区的支持，LocalDate、LocalTime、LocalDateTime都是不带时区的，带时区的时间类分别为ZonedDate、ZonedTime、ZonedDateTime。

其中每个时区都对应着ID、ID格式为“区域/城市”。例如：Asia/Shanghai

ZonedId：该类中包含了所有的时区信息

```java
public static void test1(){
        //获取所有时区的ID
        ZoneId.getAvailableZoneIds().forEach(System.out::println);

        //不带时区的时间，获取计算机当前的时间
        LocalDateTime now = LocalDateTime.now();
        System.out.println("中国当前时间："+now);

        //创建带时区的类
        //创建时间标准时间
        ZonedDateTime zonedDateTime = ZonedDateTime.now(Clock.systemUTC());
        System.out.println("世界标准时间当前时间："+zonedDateTime);

        //使用计算机默认时区
        ZonedDateTime now1 = ZonedDateTime.now();
        System.out.println("计算机默认时区时间："+now1);

        //使用指定的时区创建时间
        ZonedDateTime now2 = ZonedDateTime.now(ZoneId.of("Asia/Tokyo"));
        System.out.println("指定时区创建时间："+now2);
        //withZoneSameInstant 修改时区,同时修改时间
        ZonedDateTime withZoneSameInstant = now2.withZoneSameInstant(ZoneId.of("Asia/Shanghai"));
        System.out.println("withZoneSameInstant" +withZoneSameInstant);
        //withZoneSameInstant 修改时区,不修改时间
        ZonedDateTime withZoneSameLocal = now2.withZoneSameLocal(ZoneId.of("Asia/Shanghai"));
        System.out.println("withZoneSameLocal" +withZoneSameLocal);
    }
```

## 四、可重复注解与类型注解

### 4.1 可重复注解

自从java5中引入注解以来，注解开始变得非常流行，并在各个框架和项目中被广泛使用。不过注解有一个很大的限制是：在同一个地方不能多次使用同一个注解。jdk8引入了重复注解的概念，允许在同一个地方多次使用同一个注解。在jdk8中使用@Repeatable注解定义重复注解。

重复注解使用的步骤：

1. 定义重复的注解容器注解

   ```java
   @Retention(RetentionPolicy.RUNTIME)
   @interface MyTests {
       //可重复注解容器
       MyTest[] value();
   }
   ```

2. 定义一个可以重复的注解

   ```java
   @Retention(RetentionPolicy.RUNTIME)
   @Repeatable(MyTests.class)
   @interface MyTest {
       String value();
   }
   ```

3. 配置多个重复的注解

   ```java
   @MyTest("ma")
       @MyTest("mb")
       public void test(){
   
       }
   ```

4. 解析可重复注解

   ```java
   public static void main(String[] args) throws NoSuchMethodException {
           //4.解析可重复注解
           //获取类上的可重复注解
           //getAnnotationsByType是新增的注解，用于获取可重复的注解
           MyTest[] annotationsByType = RepeatableAnnotationDemo.class.getAnnotationsByType(MyTest.class);
           for(MyTest myTest : annotationsByType){
               System.out.println(myTest);
           }
           //获取方法上的重复注解
           MyTest[] tests = RepeatableAnnotationDemo.class.getMethod("test").getAnnotationsByType(MyTest.class);
           for(MyTest myTest :tests){
               System.out.println(myTest);
           }
       }
   ```

### 4.2 类型注解

JDK8为@Tarfet元注解新增了两种类型：TYPE_PARAMETER，TYPE_USE

TYPE_PARAMETER：表示该注解能写在类型参数的声明语句中。类型参数声明如：<T>、<T extends Xxx>

TYPE_USE ：表示注解可以在任何用到类型的地方使用。

TYPE_PARAMETER的使用

```java
@Target(ElementType.TYPE_PARAMETER)
@interface TypeParam{

}
```

```java
public <@TypeParam E extends Integer> void test(){

}
```

TYPE_USE的使用

```java
@Target(ElementType.TYPE_USE)
@interface NotNull {

}
```

```java
private @NotNull int a= 10;
    
public void test1(@NotNull String str,@NotNull int a){
        
}
```

## 五、使用Optional处理null

optional是一个没有子类的工具类，Optional是一个可以为null的容器对象。它的主要作用就是为了解决避免Null检测。防止NullPointerException。

**以前对null的处理方式**

```java
public static void test1(){
        String userName = "张三";
        //String userName = null;
        if(userName != null){
            System.out.println("用户名为: " + userName);
        }else{
            System.out.println("用户名不存在");
        }
    }
```

**使用optional对null的处理方式**

```java
public static void test2(){
        //1. 创建optiona对象(of方法只能传入一个具体值，不能传入null)
        //如果要创建一个空的容器，可以使用ofNullable方法，既可以穿非空又可以传空值。
        //empty: 存入的是null
        Optional<String> optional = Optional.of("张三");
        Optional<String> optional1 = Optional.ofNullable(null);
        Optional<String> optional2 = Optional.empty();

        //2.isPresent:判断optional中是否有值,有返回true否则false
        boolean present = optional.isPresent();
        System.out.println("present = "+present);

        //3. get:获取Optional中的值,如果有值就返回具体值，否则就报错
        System.out.println(optional.get());

        //通常get方法不单独使用
        if(optional2.isPresent()){
            System.out.println(optional.get());
        }else {
            System.out.println("没有值");
        }

    }
```

