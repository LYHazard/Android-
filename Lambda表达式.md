# JDK8新特性

## Lambda表达式

### 1.需求分析

创建一个新的线程，指定线程要执行的任务

```java
public static void main(String[] args) {
    //开启一个新线程
    new Thread(new Runnable(){
        @Override
        public void run() {
            System.out.println("新线程中的代码"+Thread.currentThread().getName());
        }
    }).start();
    //主线程
    System.out.println("主线程中的代码"+Thread.currentThread().getName());
}
```

代码分析：

1. Thread类需要一个Runnable接口作为参数，其中的抽象方法run方法是用来指定线程任务内容的核心
2. 为了指定run方法体，不得不需要Runnable的实现类
3. 为了省去定义一个Runnable的实现类，不得不使用匿名内部类
4. 必须覆盖重写抽象的run方法，所有的方法名称，方法参数，方法返回值不得不重写一遍，而且不能出错
5. 但是实际上，我们只在乎方法体中的代码

### 2. Lambda表达式简单案例

简化 1 中的代码

```java
new Thread() ->{system.out.println("新线程中的代码"+Thread.currentThread().getName());}.start
```

Lambda表达式的优点：简化了匿名内部类的使用，语法更加简单

匿名内部类语法冗余：体验了Lambda表达式后，发现Lambda表达式是简化匿名内部类的一种方式。

### 3. Lambda的语法规则

Lambda省去了面向对象的条条框框，Lambda的标准格式由3个部分组成：

```java
（参数类型：参数名称）->{
代码体；
}
```

格式说明：

- （参数类型：参数名称）：参数列表
- {代码体}：方法体
- ->:分割方法体和参数列表和方法体

#### 3.1 无参无返回值的lambda

创建无参无返回值的接口

``` java
public interface Service {
    public void show();
}
```

对比实现

``` java 
public class Demo1 {

    public static void main(String[] args) {

        goShow(new Service() {
            @Override
            public void show() {
                System.out.println("show 方法执行了");
            }
        });
        goShow(()->
        {
            System.out.println("lambda show 方法执行了");
        });
    }
    public static void goShow(Service service){
        service.show();
    }
}
```

#### 3.2 有参有返回值的lambda

先创建一个数据类

``` java
public class Person {
    private String name;
    private Integer age;
    private Integer height;
}
```

利用Collection类的sort方法对Person集合中的元素进行排序

``` java
public class Demo2 {
    public static void main(String[] args) {
        List<Person> people = new ArrayList<>();
        people.add(new Person("刘德华", 35, 175));
        people.add(new Person("张学友", 36, 173));
        people.add(new Person("郭富城", 37, 178));
        people.add(new Person("黎明", 39, 180));
        /*Collections.sort(people, new Comparator<Person>() {
            @Override
            public int compare(Person o1, Person o2) {
                Integer n = o1.getHeight()-o2.getHeight();
                return n;
            }
        });*/
        Collections.sort(people,((Person o1, Person o2) ->  return o1.getHeight()-o2.getHeight()));
        for (Person person:people){
            System.out.println(person);
        }
    }
}
```

实现结果

``` java 
Person{name='张学友', age=36, height=173}
Person{name='刘德华', age=35, height=175}
Person{name='郭富城', age=37, height=178}
Person{name='黎明', age=39, height=180}
```

### 4 FunctionalInterface注解

```java
/**
 * FunctionalInterface 规定接口中只能有一个抽象方法
 */


@FunctionalInterface
public interface Service {
    public void show();
}

```

### 5 lambda表达式的原理

通过JDK自带的一个工具：Javap对字节码进行反汇编操作

```shell
java -c -p 文件名.class
```

