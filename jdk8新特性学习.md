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



## 二、集合stream流式操作



## 三、接口的增强



## 四、并行数组排序



## 五、Optional中避免Null检查



##  六、新的时间日期API



## 七、可重复注解