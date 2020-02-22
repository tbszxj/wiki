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



## 四、可重复注解



## 五、使用Optional处理null

optional是一个没有子类的工具类，Optional是一个可以为null的容器对象。它的主要作用就是为了解决避免Null检测。防止NullPointerException。

以前对null的处理方式

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

使用optional对null的处理方式

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

