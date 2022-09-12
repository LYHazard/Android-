# Java学习笔记



## 一、Java基本程序设计结构

1、\u2122 表示注册符号 ( ), \u03C0 表示希腊字母Π。

2、![image-20210712172306501](C:\Users\22859\AppData\Roaming\Typora\typora-user-images\image-20210712172306501.png)

### 1.1、码点与代码单元

​	char数据类型是一个采用UTF-16编码表示Unicode码点的代码单元。大多数的常用Unicode字符使用一个代码单元就可以表示，而**辅助字符**需要一对代码单元表示。

​	length方法将返回采用UTF-16编码表示的给定字符串所需要的代码单元数量。例如：

```java
String greeting ="Hello";

int n =greeting.length();//is 5.
```

想要得到实际的长度，即码点数量，可以调用：

```java
int cpCount =greeting.codePointCount(0,greeting.length());
```

调用s.charAt(n)将返回位置n的代码单元，n介于0~ s.length()-1之间。例如：

```java
char first = greeting.charAt(0);//first is 'H'
```

要想得到第i个码点，应该使用下列语句

```java
int index = greeting.offsetByCodePoints(0,i);

int cp =greeting.codePointAt(index);
```

**String API**

* **char charAt(int index)**

返回给定位置的代码单元。除非对底层的代码单元感兴趣， 否则不需要调用这个方法。 

* **int codePointAt(int index)**  5.0

返回从给定位置开始的码点。

* **int offsetByCodePoints(int startIndex, int cpCount)**  5.0

返回从startIndex代码点开始，位移cpCount后的码点索引。

* **int compareTo(String other)**

按照字典顺序，如果字符串位于other之前，返回一个负数；如果字符串位于other之后，返回一个正数；如果两个字符串相等，返回0.

* **IntSteam codePoints() ** 8

将这个字符串的码点作为一个流返回。调用toArray将它们放在一个数组中。

* **new String(int[] codePoints, int offset,int count)**  5.0

用数组中 offset开始的count个码点 构造一个字符串。

* **boolean equals(object other)**

如果字符串与other相等，返回true。

* **boolean equalsIgnoreCase(String other)**

如果字符串与other相等（忽略大小写），返回true。

* **boolean startWith(String prefix)**
* **boolean endsWith(String suffix)**

如果字符串以suffix开头或者结尾，则返回true。

* **int index0f(String str)**
* **int index0f(String str,int fromIndex)**
* **int index0f(int cp)**

* **int index0f(int cp, int fromIndex)**

返回与字符串str或代码点cp匹配的第一个子串的开始位置。这个位置从索引0或fromIndex开始计算。如果再原始串中不存在str，返回-1。

* **int lastIndex0f(String str)**
* **int lastIndex0f(String str,int fromIndex)**
* **int lastIndex0f(int cp)**
* **int lastIndex0f(int cp, int fromIndex)**

​    返回与字符串str或代码点cp匹配的最后一个子串的开始位置。这个位置从原始串尾端或fromIndex开始计算。

* **int length()**

返回字符串的长度。

* **int codePointCount(int startIndex , int endIndex)**5.0

返回startIndex和endIndex-1之间的代码点数量。没有配成对的代用字符将计入代码点。

* **String replace(CharSequence oldString,CharSequence newString)**

返回一个新字符串。这个字符串用newString代替原始字符串中所有的oldString。可以用String或StingBuilder对象作为CharSequence参数。

* **String substring(int beginIndex)**
* **String substring(int beginIndex,int endIndex)**

返回一个新字符串。这个字符串包含原始字符串中从beginIndex到串尾或endIndex-1的所有代码单元。

* **String toLowerCase()**
* **String toUpperCase()**

返回一个新字符串。这个字符串将原始字符串中的大写字母改为小写，或者将原始字符串中的所有小写字母改成大写字母。

* **String trim()**

返回一个新字符串。这个字符串将删除了原始字符串头部和尾部的空格。

* **String join(CharSequence delimiter,CharSequence...element)**8

返回一个新字符串，用给定的定界符连接所有元素。

***

### 1.2、阅读联机API文档

​	标准库中的几千个类，要记住所有的类和方法是不可能的，所以要使用在线APi文档，API文档是JDK的一部分，它是HTML格式的。让浏览器指向安装jdk的docs/api/index.html子目录，就可以看到。

***

### 1.3、构建字符串

​	如果需要用许多小段的字符串构建一个字符串，那么应该按照下列步骤进行。首先，构建一个空的字符串构建器：

```java
 StringBuilder builder = new StringBuilder();

//每次需要添加一部分内容时，就调用append方法。

builder.append(ch);//appends a single character

builder.append(str);//appends a string


```

​	在需要构建字符串时就调用toString方法，将可以得到一个String对象，其中包含了构建起中的字符序列。

​	

```java
String comletedString=builder.toString();
```

**注释**：在JDK5.0中引入StringBuilder类。这个类的前身是StringBuffer，其效率稍有些低，但允许采用多线程的方式执行添加或删除字符操作。如果所有字符串在一个单线程中编辑，则应该用StringBuilder替代它。这两个类的API是相同的。

​	StringBuilder类中的重要方法。

* **StringBuilder（）**

构造一个空的字符串构建器

* **int length()**

返回构建器或缓冲器中的代码单元数量

* **StringBuilder append(String str)**

追加一个字符串并返回this

* **StringBuilder append(char c)**

追加一个代码单元并返回this

* **StringBuilder appendCodePoint(int cp)**

追加一个代码点，并将其转换成一个或两个代码单元并返回this

* **void setCharAt(int i,char c)**

将第i个代码单元设置为c。

* **StringBuilder insert(int offset,String str)**

在offset位置插入一个字符串并返回this

* **StringBuilder insert(int offset,Char c)**

在offset位置插入一个代码单元并返回this

* **StringBuilder delete(int startIndex,int endIndex)**

删除偏移量从startIndex到endIndex-1的代码单元并返回this。

* **String toString()**

返回一个与构建器或缓冲器内容相同的字符串

***

###  1.4、输入输出

#### 1.4.1 读取输入

​	打印输出到“标准输出流”（即控制台窗口）是一件非常容易的事情，只要调用System.out.println即可。然而，读取“标准输入流”System.in就没有那么容易。通过控制台进行输入，首先需要构造一个Scanner对象，并与“标准输入流”System.in关联。

​	**Scanner in = new Scanner(System.in);**

现在，就可以使用Scanner类的各种方法实现输入操作了。例如，nextLine方法将输入一行。

​	

```java
System.out.print("What is your name?");

String name =in.nextLine();


```

在这里，使用nextLine方法是因为在输入行中有可能包含空格。要想读取一个单词（以空白符作为分隔符），就调用

​	**String firstName= in.next();**

要想读取一个整数，就调用nextInt方法。

​	**System.out.print("How old are you?");**

​	**int age = in.nextInt();**

与此类似，要想读取下一个浮点数，就调用nextDouble方法。

因为输入是可见的，所以Scanner类不适用于从控制台读取密码，所以引入了**Console类**实现这个目的。要想读取一个密码，实行以下操作：

```java
Console cons = System.console();
String username = cons.readLine("User name:");
char[] passwd = cons.readPassword("Password:")
```

​	为了安全起见，返回的密码存放在一维数组中，而不是字符串中。在对密码进行处理之后，应该马上用一个填充值覆盖数组元素

​	采用Console对象处理输入不如采用Scanner方便，每次只能读取一行输入，而没有能够读取一个单词或者数值的方法。

**API java.util.Scanner 5.2**:

* **Scanner(InputStream in)**

用给定的输入流创建一个Scanner对象

* **String nextLine()**

读取输入的下一行内容

* **String next（）**

读取下一个单词（以空格为分隔符）

* **int nextInt()**
* **double nextDouble()**

读取并转换下一个表示整数或浮点数的字符序列

* **boolean hasNext()**

检测输入中是否还有其他单词。

* **boolean hasNextInt()**
* **boolean hasNextDouble()**

检测是否还有表示整数或浮点数的下一个字符序列。

**API java.lang.System 1.0**

* **static Console console()**

如果有可能进行交互操作，就通过控制台窗口为交互的用户返回一个Console对象，否则返回null。对于任何一个通过控制台窗口启动的程序，都可使用Console对象。否则，其可用性将与锁使用的系统有关。

**API java.io.Console **6

* **static char[] readPassword(String prompt,Object...args)**
* **static String readLine(String prompt,Object...args)**

显示字符串prompt并且读取用户输入，直到输入行结束。args参数可以用来提供输入格式。

#### 1.4.2 格式化输出

![image-20210810153205564](C:\Users\22859\AppData\Roaming\Typora\typora-user-images\image-20210810153205564.png)

​                                                                                                             **转换符**

![image-20210810153559176](C:\Users\22859\AppData\Roaming\Typora\typora-user-images\image-20210810153559176.png)



#### 1.4.3文件输入与输出

​	对文件进行读取，需要一个用File对象构造一个Scanner对象，如下所示：

​	**Scanner in = new Scanner(Paths.get("myfile.text"),“UTF-8”）;**

​	如果文件不存在，创建该文件。可以像输出到System.out一样使用print\println\printf命令。

**API java.util.Scanner 5.0**:

* **Scanner(File f)**

  构造一个从给定文件读取数据的Scanner

* **Scanner(String data)**

  构造一个从给定字符串读取数据的Scanner

**API java.io.PrintWriter 1.1**:

* **PrintWriter(String fileName)**

  构造一个将数据写入文件的PrintWriter。文件名由参数指定。

**API java.nio.file.Paths 7**:

* **static Path get(String pathname)**

  根据给定的路径名构造一个Path

***

### 1.5、控制流程

#### 1.5.1块作用域

​	块（即复合语句）是指一对大括号括起来的若干条简单的Java语句。块确定了变量的作用域。一个块可以嵌套在另一个块中。

​	在Java中不能在嵌套的两个块中声明同名的变量。在C++中是可以这样做的，在内层定义的变量会覆盖在外层定义的变量，但是这样容易出错，因此在Java中不允许这样做。

#### 1.5.2条件语句

​	while循环语句首先检测循环条件。因此，循环体中的代码可能不被执行。如果希望循环体至少执行一次，则应该将检测条件放在最后。使用**do/while循环语句**可以实现这种操作方式。它的语法格式为：

​	**do** *statement* **while**(*condition*)

​	这种循环语句先执行语句，再检测循环条件；然后重复语句，再检测循环条件，以此类推；

​	Java还提供了一种带标签的break语句，用于跳出多重嵌套的循环语句。有时候，在嵌套很深的循环语句中会发生一些不可预料的事情。此时可能更加希望跳到嵌套的所有语句之外。通过添加一些额外的条件判断实现各层循环的检测很不方便。例子如：

```java
read_data:

.......

if(condition)

break read_data
```

### 1.6、大数值

​	如果基本的整数和浮点数精度不能够满足需求，那么可以使用java.math包中的两个很有用的类：**BigInteger 和 BigDecimal**。这两个类可以处理包含任意长度数字序列的数值。BigInteger类实现了任意精度的整数运算，BigDecimal实现了任意精度的浮点数运算。

​	使用静态的valueOf方法可以将普通的数值转换成大数值：

​	**BigInteger a =BigInteger.valueof(100);**

​	遗憾的是，不能使用人们熟悉的算术运算符（如：+和*）处理大数值。而需要使用大数值类中的add和multiply方法。

​	**BigInteger c= a.add(b);//c=a+b**

​	**BigInteger d = c.multiply(b.add(BigInteger.valueof(2)));//d=c*(b+2)**

**API java.math.BigInteger 1.1**:

* **BigInteger add(BigInteger other)**
* **BigInteger subtract(BigInteger other)**
* **BigInteger multiply(BigInteger other)**
* **BigInteger multiply(BigInteger other)**
* **BigInteger divide(BigInteger other)**
* **BigInteger mod(BigInteger other)**

返回这个大整数和另外一个大整数other的和、差、积、商以及余数。

* **int compareTo(BigInteger other**)

如果这个大整数与另一个大整数other相等，返回0；如果这个大整数小于另一个大整数other，返回负数；否则，返回正数。

* **static BigInteger valueOf(long x)**

返回等于x的大整数。

**API java.math.BigInteger 1.1**:

* **BigDecimal add(BigDecimal other)**
* **BigDecimal subtract(BigDecimal other)**
* **BigDecimal multiply(BigDecimal other)**
* **BigDecimal divide(BigDecimal other RoundingMode mode)5.0**

返回这个大实数与另一个大实数other的和、c差、积、商。想要计算商，必须给出舍入方式（rounding mode）。RoundingMode.HALF_UP是四舍五入，有关其他的舍入方式参看API文档。

* **int compareTo(Bigdecimal other)**

如果这个大实数与另一个大实数相等，返回0；如果这个大实数小于另一个大实数，返回负数；否则，返回正数。

* **static BigDecimal valueOf(long x)**

* **static BigDecimal valueOf(long x,int scale)**

  返回值为x或者x/10^scale的一个大实数。

### 1.7、 数组

#### 1.7.1for each 循环

​	Java有一种功能很强的循环结构，可以用来依次处理数组中的每个元素（其他类型的元素集合亦可）而不必为指定下标值而分心。

​	这种增强的for循环的语句格式为：

​	**for**(*variable:collection*)*statement*

定义一个变量用于暂存集合中的每一个元素，并执行相应的语句（当然，也可以是语句块）。collection 这一集合表达式必须是一个数组或者是一个实现了Iterable 接口的类对象（例如ArrayList)。例子：

```java
for(int element : a)

​	System.out.println(element);
```

打印数组a的每一个元素，一个元素占一行。

​	这个循环应该读作“循环a中的每一个元素”Java语言的设计者认为应该使用诸如foreach、in这样的关键字，但这种循环语句并不是最初就包含在Java语言中的，而是后来添加进去的，并且没有人打算废除已经包含同名方法或变量的旧代码。

​	当然，使用传统的for循环也可以获得同样的效果但是，for each循环语句会更简洁、更不易出错。

：有个更加简单的方式打印数组中的所有值， 即利用 Arrays 类的 toString 方法。 调用 Arrays.toString(a), 返回一个包含数组元素的字符串，这些元素被放置在括号内， 并用逗号分隔， 例如，“ [2,3,5,7,11,13]” 、 要想打印数组，可以调用**System.out.println(Arrays.toString(a));**

***

#### 1.7.2数组的初始化以及匿名数组

​	在Java中，允许数组长度为0.在编写一个结果为数组的方法时，如果碰巧结果为空，则这种语法形式就显得非常有用。此时可以创建一个长度为0的数组；

**new elementType[0]**

注意，数组长度为0与null不同。

***

#### 1.7.3数组拷贝

​	在Java中，允许将一个数组变量拷贝给另一个数组变量。这时，两个变量将引用同一个数组：

```java
	int[] luckyNumbers=smallPrimes;

luckyNumber[5]=12;//now smallPrimes[5] is also 12
```

​	如果希望将一个数组的所有值拷贝到一个新的数组当中，就需要**Arrays**类的copyOf方法：

​	**int[] copiedLuckyNumbers = Arrays.copyOf(luckyNumbers,luckyNumbers.length);**

第二个参数是新数组的长度。这个方法通常用来增加数组的大小：

**luckyNumbers =Arrays.copyOf(luckyNumbers,2*luckyNumbers.length);**

如果数组元素是数值型，那么多余的元素将被赋值为0；如果数组元素是布尔型，则将赋值为false。相反，如果长度小于原始数组的长度，则只拷贝最前面的数据元素。

***

#### 1.7.4命令行参数

​	每一个Java应用程序都有一个带String arg[]参数的main方法。这个参数表明main方法将接收一个字符串数组，也就是**命令行参数**；

***

#### 1.7.5数组排序

​	对数值型数组进行排序，可以使用Arrays类中的sort方法：

**int[] a = new int[10000];**

**...**

**Arrays.sort(a)**

​	这个方法使用了优化的快速排序算法。快速排序算法对于大多数数据集合来说都是效率比较高的。Arrays类还提供了几个使用很便捷的办法，API注释中会介绍到。

**API java.util.Arrays 1.2**

* static String toString(type[] a) 5.0

  返回包含a中数据元素的字符串，这些数据被放在括号内，并用逗号分隔。

  参数：a 是各种类型的数组

* **static type copyOf(type[] a,int length)6**

* **static type copyOfRange(type[] a,int start,int end)6**

  参数：a 类型为 int、 long、 short、 char、 byte、boolean、 float 或 double 的数组。

  start  起始下标（包含这个值）

  end   终止下标（不包含这个值）。这个值可能大于 a.length。 在这种情况

  下，结果为 0 或 false。

  length  拷贝的数据元素长度。 如果 length 值大于 a.length， 结果为 0 或 false ;

  否则， 数组中只有前面 length 个数据元素的拷贝值。 

* **static void sort(type[] a)**

  采用优化的快速排序算法对数组进行排序。

* **static int binarySearch(tyoe[] a ,type v)**

* **static int binarySearch(tyoe[] a ,int start,int end,type v) **6

  采用二分搜索算法查找值v。如果查找成功，则返回相应的下标值；否则，返回一个负数值r。-r-1是为保持a有序v应插入的位置。

* **static void fill(type[] a,type v)**

  将数组的所有数据元素值设置为v。

* **static boolean equals(type[] a,type[] b)**

  如果两个数组大小相同，并且下标相同的元素都对应相等，返回true。

  ***

  #### 7.6多维数组

  ​	**for** **each** 循环语句不能自动处理二维数组的每一个元素。它是按照行， 也就是一维数组处理的要想访问二维教组 **a** 的所有元素， 需要使用两个嵌套的循环， 如下所示：

  for (double[] row : a)

  for (double value : row)

  *do* *something* *with* value

  如果想要快速的打印一个二维数组的数据元素列表，可以调用：

  **System.out.println(Arrays.deepToString(a));**

***

#### 1.7.7不规则数组

​	Java实际上没有多维数组，只有一维数组。多维数组被解释为“数组的数组”

![image-20210813161405655](C:\Users\22859\AppData\Roaming\Typora\typora-user-images\image-20210813161405655.png)

## 二、对象与类

### 2.1、LocalDate 类的使用

​	**API：java.time.LocalDate 8**

* **static LocalTIme now()**

​      构造一个表示当前日期的对象

* **static LocalTime of(int year, int month, int day)**

  构造一个表示给定日期的对象

* **int getYear()**

* **int getMonthValue()**

* **int getDayOfMonth()**

  得到当前日期的年、月和日。

* **DayOfWek getDayOfWeek**

  得到当前日期是星期几，作为DayOfWeek类的一个实例返回。调用getValue来返回~7之间的一个数，表示这是星期几，1表示星期一，7表示星期日；

* **LocalDate plusDays(int n)**

* **LocalDate minusDays(int n)**

  生成当前日期之后或之前n天的日期

### 2.2、构造器的使用

* 构造器与类同名
* 每个类可以有一个以上的构造器
* 构造器可以有0个、1个或多个参数
* 构造器没有返回值
* 构造器总是伴随着new操作一起调用

### 2.3、 方法参数

java程序设计语言对对象采用的不是引用调用，时间上，对象引用是按值传递的。

​	总结一下Java钟方法参数的使用情况：

* 一个方法不能修改一个基本数
* 据类型的参数（即数值型或布尔型）
* 一个方法可以改变一个对象参数的状态
* 一个方法不能让对象参数引用一个新的对象。   

### 2.4 包

​	使用包的主要原因是确保类名的唯一性。假如两个程序员不约而同地建立了Employee类。只要将这些类放置在不同的包中，就不会产生冲突。

#### 2.4.1 类的导入

​	一个类可以使用所属包中的所有类，以及其他包中的公有类（public class）。有两种方式访问另一个包中的公有类。第一种方式是在每个类名之前添加完整的包名。

例如

​	

```java
java.time.LocalDate.today=java.time.LocalDate.now();
```

​	这种方式很繁琐，一般都是使用import语句。import语句是一种引用包含在包中的类的简明描述，可以使用import语句导入一个特定的类或者整个包。import语句应该位于源文件的顶部（但位于package语句的后面）。例如，可以使用下面这条语句导入java.util包中所有的类。

​	

```java
import java.util.*
```

然后，就可以使用

​	

```java
LocalDate today=LocalDate.now();
```

#### 2.4.2  静态导入

​	import语句不仅可以导入类，还增加了导入静态方法和静态域的功能。

​	例如，如果在源文件的顶部，添加一条指令：

```java
	import static java.lang.System.*;
```

就可以使用System类的静态方法和静态域，而不必加类名前缀：

```java
	out.println("Goodbye,world!");//i.e,System.out

exit(0);
```

另外，还可以导入特定的方法或域：

```java
	import static java.lang.System.out;
```



#### 2.4.3 将类放入包中

​	要想将一个类放入包中，就必须将包的名字放在源文件的开头，包中定义类的代码之前。例如 package com.horstman.corejava;

#### 2.4.4 包作用域

​	如果没有指定public或者private，这个部分（类、方法或变量）可以被同一个包中的所有方法访问。

***

### 2.5 类路径

​	类存储在文件系统的子目录中。类的路径必须与包名匹配。

为了使类能够被多个程序共享，需要做到下面几点： 

1 ) 把类放到一个目录中， 例如/home/user/classdir。需要注意， 这个目录是包树状结构的基目录。如果希望将 com.horstmann.corejava.Employee 类添加到其中，这个 Employee.class类文件就必须位于子目录 /home/user/classdir/com/horstmann/corejava 中。 

2 ) 将 JAR 文件放在一个目录中，例如：/home/user/archives。 

3 ) 设置类路径（classpath)。类路径是所有包含类文件的路径的集合。 

在 UNIX 环境中， 类路径中的不同项目之间采用冒号 （；） 分隔： 

/home/user/classdir:.:/home/use r/archives/archive.jar

***

### 2.6 文档注释

​	JDK包含一个很有用的工具，叫做javadoc，它可以由源文件生成一个HTML文档。事实上，联机API文档就是通过对标准java类库的源代码运行javadoc生成的。

​	如果在源代码中添加以专用的定界符/**开始的注释，那么可以很容易地生成一个看上去很专业的文档。这是一个很好的方式，这样做可以把代码和注释保存在一个地方。如果将文档存入一个独立的文件中，就有可能随着时间的推移，出现代码和注释不一致的问题。然而，由于文档注释和源代码在同一个文件中，在修改源代码的同时，重新运行javadoc就可以轻而易举地保持两者的一致。

#### 2.6.1 注释的插入

​	javadoc使用程序（utility）从下面几个特性中抽取信息：

* 包
* 公有类与接口
* 公有和受保护的构造器及方法
* 公有的和受保护的域

​    应该为上面几部分编写注释。注释应该放置在所描述特性的前面。注释以/ * * 开始，并以/ *结束  

每个/ * *.....  * /文档注释在标记之后紧跟着自由格式文本（free-form text）。标记由@开始，如@author或@param。

​	自由格式文本的第一句应该是一个概要性的句子。javadoc实用程序自动地将这些句子抽取出来形成概要页。

​	在自由格式文本中，可以使用html修饰符，不过，一定不要使用< h 1 >或< h r >，因为它们会域文档的格式产生冲突。

#### 2.6.2 类注释

​	类注释必须放在import语句之后，类定义之前。



#### 2.6.3 方法注释

​	每个方法注释必须放在所描述的方法之前。除了通用标记之外，还可以使用下面的标记：

* **@param变量描述**

  这个标记将对当前方法的“param”（参数）部分添加一个条目。这个描述可以占据多行，并可以使用HTML标记。一个方法的所有@param标记必须放在一起。

* @return描述

  做个标记对当前方法添加“return”（返回）部分。这个描述可以跨越多行，并且可以使用HTML标记。

* @throws类描述

  这个标记将添加一个注释，用于表示这个方法有可能抛出异常。

#### 2.6.4 域注释

​	只需要对公有域（通常指的是静态常量）建立文档。例如：

/ * *

*The “Hearts” card suit

*/

public static final int HEARTS = 1;

#### 2.6.5 通用注释

​	下面的标记可以用在类文档的注释中。

* @auther 姓名

  这个标记将产生一个“author”（作者）条目。可以使用多个@author标记，每个@author标记对应一个作者。

* @version文本

  这个标记将产生一个“version”(版本)条目。这里的文本可以是对当前版本的任何描述。

  下面的标记可以用于所有的文档注释中。

* @since文本

  这个标记将产生一个“since”条目。这里的text可以是对引入特性的版本描述。例如，@since version 1.7.1

* @deprecated 文本

  这个标记对类、方法或变量添加一个不再使用的注释。文本中给出了取代的建议。

  例如：

  @deprecated Use < code > setVisible(true)< /code> instead

  通过@see 和@link 标记，可以使用超级链接，链接到javadoc文档的相关部分或外本文档

* @see引用

  这个标记将在“see also”部分增加一个超级链接。它可以用于类中，也可以用于方法中。这里的引用可以选择下列情形之一：

  package,class#feature label

  < a href="..." >label< /a>

  "text"

  第一种情况最常见。只要提供类、方法或变量的名字，javadoc就在文档中插入一个超链接。例如，

  @see com.horstmann.corejava.Employee#raiseSalary(double)

  建立一个链接到 com.horstmann.corejava.Employee 类的 raiseSalary(double) 方法的超

  链接。 可以省略包名， 甚至把包名和类名都省去，此时，链接将定位于当前包或当前类。

## 三、继承

### 3.1、子类构造器

​	关键字 this 有两个用途： 一是引用隐式参数，二是调用该类其他的构造器 ， 同样，super 关键字也有两个用途：一是调用超类的方法，二是调用超类的构造器。在调用构造器的时候，这两个关键字的使用方式很相似。调用构造器的语句只能作为另一个构造器的第一条语句出现。构造参数既可以传递给本类（ this) 的其他构造器，也可以传递给超类（super ) 的构造器。 

#### 3.1.1 四个访问修饰符

 * 仅对本类可见--private
 * 对所有类可见--public
 * 对本包和所有子类可见--protected
 * 对本包可见--不需要修饰符

***

### 3.2 Object：所有类的超类

​	Object类是java所有类的始祖，在Java钟每个类都是由它拓展而来的。**如果没有明确地指出超类，Object就被认为是这个类的超类。**

​	可以使用Object类型的变量引用任何类型的对象：

​	

```java
Object obj = new Employee（“Haryy”，35000）
```

​	当然，Object类型的变量只能用于作为各种值的通用持有者。要想对其中的内容进行具体的操作，还需要清楚对象的原始类型，并进行相应的类型转换：

​	

```java
Employee e =（Employee）obj；
```

​	在Java中，只有基本类型（primitive types）不是对象，例如，数值、字符和布尔类型。

​	所有的数值类型，不管是对象数组还是基本类型的数组都拓展了Object类。

```java
	Employee[] staff= new Employee[10];

​	obj=staff;//OK

​	obj = new int[10];//OK
```

***

#### 3.2.1 equals方法

​	在Object类中的equals方法用于检测一个对象是否等于另外一个对象。在Object类中，这个方法将判断两个对象是否具有相同的引用。如果引用相同，那么久一定是相等的。

​	在子类中定义equals方法时，首先调用超类的equals。如果检测失败，对象就不可能相等。如果超类中的域都相等，就需要比较子类中的实例域。

#### 3.2.2 相等测试与继承

​	Java语言规范要求equals方法具有下面的特性：

* 自反性：对于任何非空引用x，x.equals(x)应该返回true。
* 对称性：对于任何引用x和y，当且仅当y.equals(x)返回true，x.euqals(y)也应该返回true。
* 传递性： 对于任何引用 **x**、 **y** 和 **z**, 如果 **x**.**equals**(**y**) 返 true， **y**.**equals**(**z**) 返回 **true**, **x**.**equals**(**z**) 也应该返回 **true**。
* 一致性：如果x和y应用的对象没有发生变化，反复调用x.equals(y)应该返回同样的结果。
* 对于任意非空引用x，x.equals(null)应该返回false。

  然而，就对称性来说，当参数不属于同一个类的时候需要仔细地思考一下。如下调用：

e.equals(m)

这里的e是一个Employee对象。m是一个Manager对象，并且两个对象具有相同的姓名、薪水和雇佣日期。如果在Employee.equals中用instanceof进行检测则返回true。这样就意味着

m.equals(e)

也需要返回true。对称性不允许返回false，或者抛出异常。

​	这也就使得Manager类收到了束缚；

**这样也就可以得出：**

* 如果子类能够拥有自己的相等概念，则对称性需求将强制采用getClass进行检测。

* 如果由超类决定相等的概念，那么就可以使用instanceof进行检测，这样可以在不同子类的对象之间进行相等的比较。

  

​     在雇员和经理的例子当中，只要对应的域相等，就认为两个对象相等。如果两个Manager对象所对应的姓名、薪水和雇佣日期均相等，二奖金不相等，就认为它们是不相同的，因此，可以使用getClass检测。

​	但是，假设使用雇员的ID作为相等的检测标准，并且这个相等的概念使用域所有的子类，就可以使用instanceof进行检测，并且将Employee.equals声明伟final。

**下面给出编写一个完美的 equals 方法的建议：** 

1 ) 显式参数命名为 otherObject, 稍后需要将它转换成另一个叫做 other 的变量。 

2 ) 检测 this 与 otherObject 是否引用同一个对象：

```java
if (this = otherObject) return true;
```

这条语句只是一个优化。实际上，这是一种经常采用的形式。因为计算这个等式要比一个一个地比较类中的域所付出的代价小得多。 

3 ) 检测 otherObject 是否为 null, 如 果 为 null, 返 回 false。这项检测是很必要的。

```java
if (otherObject = null) return false; 
```

4 ) 比较 this 与 otherObject 是否属于同一个类。如果 equals 的语义在每个子类中有所改变，就使用 getClass 检测：

```java
if (getClass() != otherObject.getClass()) return false;
```

如果所有的子类都拥有统一的语义，就使用 instanceof 检测：

```java
if (!(otherObject instanceof ClassName)) return false; 
```

5 ) 将 otherObject 转换为相应的类类型变量：

```java
ClassName other = (ClassName) otherObject
```

6 ) 现在开始对所有需要比较的域进行比较了。使用 ==比较基本类型域，使用 equals 比较对象域。如果所有的域都匹配， 就返回 true; 否 则 返 回 false。

```java
return field1 == other.field1

​	&& Objects.equals(field2,other.field2)

​	&&...;
```

如果在子类中重新定义 equals, 就要在其中包含调用 super.equals(other)。

为了避免发生类型错误，可以使用@Override对覆盖超类的方法进行标记

**API java.util.Arrarys 1.2**

* static Boolean equals(typr[] a ,type[] b)

  如果两个数组长度相同，并且在对应我i之上的数据元素也均相同，将返回true。数组的元素类型可以是Object、 int、 long、 short、 char、 byte、 boolean、 float 或 double。

* java.util.Objects 7

  如果a和b都为null，返回true；如果只有其中之一为null，则返回false；否则返回a.equals(b)。

 #### 3.2.3 hashCode方法

​	散列码（hash code）是由对象导出的一个整型值。散列码是没有规律的。如果x和y是两个不同的对象x.hashCode()与y.hashCode()基本上不会相同。

​	String类使用下列算法计算散列码：

​	

```java
int hash = 0;

​	for(int i = 0;i<length(); i++)

​		hash = 31*hash + charAt(i);
```

​	由于hashCode方法定义在Object类中，因此每个对象都有一个默认的散列码，其值为对象的存储地址。

​	如果重新定义equals方法，就必须重新定义hashCode方法，以便用户可以将对象插入到散列表中。

​	hashCode方法应该返回一个整型数值（可以是负数），并合理地组合实例域的散列码，以便能够让各个不同的对象产生的散列码更加均匀。

​	例如，下面是Employee类的hashCode方法。

```java
	public class Employee

{
        public int hashCode()
        {
            return 7*name.hashCode()+11*newDouble(salary).hashCode()+13*hireDay.hashCode();
        }
        ...
    }
```

​	不过，还可以做得更好。首先，最好使用null安全的方法Object.hashCode。如果其参数为null,这个方法会返回0，否则返回对参数调用hashCode的结果。

​	另外，使用静态方法Double.hashCode避免创建Double对象：

​	

```java
public int hashCode()
{
	return 7*Objects.hashCode(name)+11*Double.hashCode(salary)+13*Objects.hashCode(hireDay);
}
```

​		还有更好的做法，需要组合多个散列值时，可以调用Object.hash并提供多个参数。这个方法会对各个参数调用Objects.hashCode,并组合这些散列值。这样Employee.hashCode方法可以简单地写为：

​	

```java
public int hashCode()
{
	return Objects.hash(name,salaty,hireDay);
}
```

​	Equals与hashCode的定义必须一致：如果x.equals(y)返回true，那么x.hashCode（）就必须与y.hashCode()具有相同的值。例如,如果用定义的Employee.equals比较雇员的ID，那么hashCode方法就需要散列ID，而不是雇员的姓名或存储地址。

**API java.util.Object 1.0**

* int hashCode()

  返回对象的散列码。散列码可以是任意的整数，包括正数或负数。两个相等的对象要求返回相等的散列码。

**API java.util.Object 7**

* static int hash(Object...objects)

  返回一个散列码，由提供的所有对象的散列码组合而得到。

* static int hashCode(Object a)

  如果a为null返回0，否则返回a.hashCode().

**API java.lang.(lnteger|Long|Short|Byte|Double| Float|Character|Boolean) 1.0**

* static int hashCode((int|long|short|byte|double|f1oat|char|boolean) value) 8

  返回给定值的散列码。

**API java.util.Arrays 1.2**

* static int hashCode(type[] a) 5.0

  计算数组a的散列码。组成这个数组的元素类型可以是 object，int，long, short, char,

  byte, boolean, float 或 double。

#### 3.2.4 toString 方法

​	在Object中还有一个重要的方法，就是toString方法，它用于返回表示对象值的字符串。下面是一个典型的例子。Point类的toString方法将返回下面这样的字符串：

​		

```java
java.awt.Point[x=10,y=20]
```

​	绝大多数的toSting方法都遵循这样的格式：类的名字，随后是一对方括号扣起来的阈值。

最好通过调用getCLass().getName()获得类名的字符串，而不要将类名硬加到toString中。

​	

```java
public String toString()
{
    return getClass().getName()
        +"[name="+name+",salary="+salary+",hireDay="+hireDay+"]";
}
```

​	toString方法亦可以供子类调用。

当然，设计子类的程序员也应该定义自己的 toString 方法，并将子类域的描述添加进去。

如果超类使用了 getClass( ).getName( ), 那么子类只要调用 super.toString( )就可以了。例如，下面是 Manager 类中的 toString 方法：

```java
public calss Manger extends Employee
{
...
	public String toString()
{
    return super.toString()
        +"[bonus="+bonus+"]";
}
}
```

​	随处可见toString方法的主要原因是：只要对象与一个字符串通过操作符“+”链接起来，Java编译就会自动地调用toString方法，以便获得这个对象的字符串描述。例如： 

​	

```java
Point p = new Point(10,20);
String message = "THe current position is"+ p;
//automatically invokes p.toString()
```

如果x是任意一个对象，并调用

```java
System.out.println(x);
```

println方法就会直接地调用x.toSting(),并打印输出得到的字符串。

​	Object类定义了toString方法，用来打印输出对象所属的类名和散列码。例如，调用

​	System.out.println(System.out)

将输出下面内容：

​	java.io.printStream@2f6684

之所以得到这样的结果是因为PrintStream类的设计者没有覆盖toString方法。

**API:java.lang.Object 1.0**

* Class getClass()

  返回包含对象信息的类对象。稍后会看到Java提供了类运行时的描述，它的内容被封装在class类中。

* boolean equals(Object otherObject)

  比较两个对象是否相等，如果两个对象指向同一块存储区域，方法返回true；否则方法返回false。在自定义的类中，应该覆盖这个方法。

* String toString()

  返回描述该对象值的字符串。在自定义的类中，应该覆盖这个方法。

**API： java.lang.Class 1.0**

* String getName()

  返回这个类的名字

* Class getSuperclass()

  以Class对象的形式返回这个类的超类信息

****

### 3.3 泛型数组列表

​	在许多程序设计语言中， 特别是在 **C**++ 语言中， 必须在编译时就确定整个数组的大小。程序员对此十分反感， 因为这样做将迫使程序员做出一些不情愿的折中。例如，在一个部门中有多少雇员？ 肯定不会超过100 人。一旦出现一个拥有 150 名雇员的大型部门呢？ 愿意为那些仅有 10 名雇员的部门浪费 90 名雇员占据的存储空间吗？ 

在 **Java** 中，情况就好多了。它允许在运行时确定数组的大小。

int actualSize = . . .;

Employee[] staff = new Employee[actualSize];

当然，这段代码并没有完全解决运行时动态更改数组的问题。一旦确定了数组的大小， 改变它就不太容易了。在 **Java** 中， 解决这个问题最简单的方法是使用 **Java** 中另外一个被称为**ArrayList** 的类。它使用起来有点像数组，但在添加或删除元素时， 具有自动调节数组容量的功能，而不需要为此编写任何代码。

​	**ArrayList** 是一个采用类型参数（ **type** **parameter** ) 的泛型类（ **generic** **class**)。为了指定数组列表保存的元素对象类型，需要用一对尖括号将类名括起来加在后面， 例如，**ArrayList**

<**Employee**>。

下面声明和构造一个保存 **Employee** 对象的数组列表：

ArrayList< Employee > staff = new ArrayList< Employee >();

两边都使用类型参数 **Employee**， 这有些繁琐。**Java** **SE** 7中， 可以省去右边的类型参数：

ArrayList< Employee> staff = new ArrayList<>()；

这被称为“ 菱形” 语法，因为空尖括号 < >就像是一个菱形。可以结合 **new** 操作符使用菱形语法。编译器会检查新值是什么。如果赋值给一个变量，或传递到某个方法，或者从某个方法返回，编译器会检査这个变量、 参数或方法的泛型类型，然后将这个类型放在<>中。在这个例子中，**new** ArrayList<>()将赋至一个类型为 **ArrayList**<**Employee**> 的变量， 所以泛型类型为 **Employee**。 

​	使用 **add** 方法可以将元素添加到数组列表中。例如，下面展示了如何将雇员对象添加到数组列表中的方法：

​	staff.add(new Empolyee("Harry Hacker",...));

​	数组列表管理着对象引用的一个内部数组。最终， 数组的全部空间有可能被用尽。这就显现出数组列表的操作魅力： 如果调用 add 且内部数组已经满了，数组列表就将自动地创建一个更大的数组，并将所有的对象从较小的数组中拷贝到较大的数组中。

​	如果已经清楚或能够估计出数组可能存储的元素数量， 就可以在填充数组之前调用ensureCapacity方法：

​	staff.ensureCapacity(100);

这个方法调用将分配一个包含 100 个对象的内部数组。然后调用 100 次 add, 而不用重新分配空间。

另外，还可以把初始容量传递给 ArrayList 构造器：

ArrayList< Employee> staff = new ArrayListo(100);

​	size方法将返回数组列表中包含的实际元素数目。例如：

​	staff.size()

将返回staff数组列表的当前元素数量，它等价于数组啊的a.length。

​	一旦能够确认数组列表的大小不再发生变化，就可以调用trimToSize方法。这个方法将存储区域的大小调整为当前元素数量所属要的存储空间数目。垃圾回收器将回收多余的存储空间。

​	一旦整理了数组列表的大小，添加新元素就需要花时间再次移动存储块，所以应该再确认不会添加任何元素时，再调用trimToSize。

**API java.util.ArrayList< E>**1.2

* ArrayList< E>()

  构造一个空数组列表。

* ArrayList< E>(int initialCapacity)

  用指定容量构造一个空数组列表。

  参数：initalCapacity     数组列表的最初容量

* boolean ad(E obj)

  在数组列表的尾端添加一个元素。永远返回true。

  参数：obj        添加的元素

* int size()

  返回存储在数组列表中的当前元素数量（这个值将小于或等于数组列表的容量。）

* void ensureCapacity(int capacity)

  确保数组列表在不重新分配存储空间的情况下能够保存给定数量的元素。

  参数：capacity      需要的存储容量

* void trimToSize()

  将数组列表的存储容量削减到当前尺寸。

#### 3.3.1 访问数组列表元素

​	数组列表自动扩展容量的s便利增加了访问元素语法的复杂程度。起原因时ArrayList类并不是Java程序设计语言的一部分；他只是一个由某些人编写=且放在标准库中的一个实用类。

​	使用get和set方法实现访问或改变数组元素的操作，而不使用人们喜爱的[ ]语法格式。

​	例如，要设置第i个元素，可以使用：

​	staff.set(i,harry);

它等价于对数组a的元素赋值（数组的下标从0开始）：

​	a[i]=harry;

​	使用add方法为数组添加新元素，而不要使用set方法，它只能替换数组中已经存在的元素内容。

​	使用下列格式获得数组列表的元素：

​	Empolyee e = staff.get(i);

等价于：

​	Employee e =a[i];

**注释：**如果没有泛型类时，原始的ArrayList类提供的get方法别无选择只能返回Object，因此，get方法的调用者必须对返回值进行类型转换：

​	Employee e = (Employee) staff.get(i);

​	原始的ArrayList存在一定的危险性。它的add和set方法允许接受任意类型的对象。

对于下面这个调用

​	staff.set(i,"Harry Hacker")

​	编译不会给出任何警告，只有在检索对象并试图对它进行类型转换时，才会发现有问题。如果使用ArrayList< Employee>,编译器就会检测到这个错误。

​	下面这个技巧可以一举两得，既可以灵活地拓展数组，又可以方便地访问数组元素。首先，创建一个数组，并添加所有的元素。

​	

```java
ArrayList< X> list = new ArrayList<>();

​	while(....)

{ 
	x=...;
    list.add(x);
}
```

​	执行完上述操作后，使用toArray方法将数组元素拷贝到一个数组中。

​	X[] a = new X[list.size()];

​	list.tpArray(a);

​	除了在数组列表的尾部追加元素之外，还可以在数组列表的中间插入元素，使用带索引参数的add方法。

​	int n = staff.size() / 2;

​	staff.add(n , e);  

​	为了插入一个新元素，位于n之后的所有元素都要向后移动一个位置。如果插入新元素后，数组列表的大小超过了容量，数组列表就会被重新分配存储空间。

​	同样地，可以从数组列表中间删除一个元素。

​	Employee e = staff.remove(n);

位于这个位置之后的所有元素都向前移动一个位置，并且数组的大小减1。

​	对数组实施插入和删除元素操作其效率比较低。对于小型数组来说。这一点就不必担心。如果数组存储的元素数比较多，又经常需要在中间位置插入、删除元素，就应该考虑使用**链表**了。

​	可以使用“for each”循环遍历数组列表：

​	for(Employee e :staff)

​		do something with e

这个循环和下列代码具有相同的效果

​	for(int i = 0;i<staff.size();i++)

{

 Employee e = staff.get();

​	do something with e

}

**API java.util.ArrayList< T>**

*  void set(int index,E obj)

  设置数组列表指定位置的元素值，这个操作将覆盖这个位置的原有内容。

  参数：index     位置（必须介于0~ size（）-1之间）

  ​			obj			新的值

* E get(int index)

  获得指定位置的元素值

  参数：index    获得的元素位置（必须介于0~size（）-1之间）

* void  add(int index,E obj)

  向后移动元素，以便插入元素。

  参数：index   插入位置（必须介于0 ~size（）-1之间）

  ​			obj         新元素

* E    remove(int index)

  删除一个元素，并将后面的元素向前移动。被删除的元素由返回值返回。

  参数：index   	被删除的元素位置（必须介于0~size（）-1之间）

#### 3.3.2  类型化与原始数组列表的兼容性

​		在你自己的代码中，你可能更愿意使用类型参数来增加安全性。这一节中，你会了解如何与没有使用类型参数的遗留代码交互操作。

​	假设有下面这个遗留下来的类：

```java
	public class EmployeeDB

{
        public   void  update(ArrayList list){...}
        public ArrayList find(String  query){...}
    }
```

​	可以将一个类型化的数组列表传递给update方法，而并不需要进行任何类型转换。

​	ArrayList< Employee> staff  =....;

​	employeeDB.update(staff);

也可以将staff对象传递给update方法。

****

### 3.4 对象包装器与自动装箱

​	有时，需要将int这样的基本类型转换成对象。所有的基本类型都有一个与之对应的类。例如，interger类对应基本类型int。通常，这些类称为**包装器（wrapper）**。这些对象包装器类拥有很明显的名字：Integer、Long、Float、Double、Short、Byte、Character、Void和Boolean（前六个类派生于公共的超类Number）。对象包装器类是不可变的，即一旦构成了包装器，就不允许更改包装在其中的值。同时，对象包装器类还是final，因此不能定义它们的子类。

​	假设想定义一个整型数组列表。而尖括号中的类型参数不允许是基本类型，也就是说，不允许写成ArrayList< int>。这里就用到了Integer对象包装器类。我们可以声明一个Integer对象的数组列表。

​	ArrayList< Integer> list = new ArrayList<>();

​	有一个很有用的特性，从而更加便于添加int类型的元素到ArrayList< Integer>中。下面这个调用

​	list.add(3);

将自动地变换成

​	list.add(Integer.valueOf(3));

​	这种变换就被称为**自动装箱（autoboxing）**

​	相反地，将一个Integer对象赋给一个int值时，将会自动地拆箱。也就是说，编译器将下列语句：

​	int n = list.get(i)；

翻译成

​	int n = list.get(i).intValue();

甚至在算术表达式中也能够自动地装箱和拆箱。例如，可以将自增操作符应用于一个包装器引用：

​	Integer n = 3；

​	n++;

编译器将自动地插入一条对象拆箱的指令，然后进行自增计算，最后再将结果装箱。

​	关于自动装箱还有几点需要说明。首先，由于包装器类引用可以为null，所以自动装箱有可能会抛出一个NullPointrException异常：

​	

```java
Integer n = null;

​	System.out.println(2 * n);//Throws NullPointrException
```

​	另外，如果在一个条件表达式中混合使用Integer和Double类型，Integer值会自动拆箱，提升为double，再装箱为Double：

​	

```java
Integer n = 1；

​	Double x = 2.0;

​	System.out.println(true ? n : x);//Prints 1.0
```

​	装箱和拆箱时编译器认可的，而不是虚拟机，编译器在生成类的字节码时，插入必要的方法调用。虚拟机只是执行这些字节码。

​	使用数值对象包装器还有另外一个好处。java设计者发现，可以将某些基本方法放置在包装器中，例如，将一个数字字符串转换成数值。

​	要想将字符串转换成整型，可以使用下面这条语句：

​	int x = Integer.parseInt(s);

​	这里和Integer对象没有任何关系，parseInt是一个静态方法。但Integer类是放置这个方法的一个好地方。

​	API注释说明了Integer类中包含的一些重要方法。其他数值类也实现了相应方法。

**API：java.lang.Integer 1.0**

* int intValue()

  以 int 的形式返回 Integer 对象的值（在 Number 类中覆盖了 intValue方法）。

* static String toString(int i )

  以一个新 String 对象的形式返回给定数值 i 的十进制表示。 

* static String toString(int i ,int radix)

  返回数组i的基于给定radix参数进制的表示。

* static int parseInt(String s)

* static int parseInt(String s,int radix)

  返回字符串s表示的整型数值，给定字符串表示的是十进制的整数（第一种方法），或者是radix参数进制的整数（第二种方法）。

* static Integer valueOf(String s)

* static Integer value Of(String s, int radix)

  返回用s表示的整型数值进行初始化后的一个新Integer对象，给定 字符串表示的是十进制的整数（第一种方法），或者是radix参数进制的整数（第二种方法）。

**API：java.text.NumberFomat 1.1**

* Number parse(String s)

  返回数字值，假设给定的String表示了一个数值。

****

### 3.5 参数数量可变的方法

​	现在版本的Java种提供了可以用可变的参数数量调用的方法（有时称为“变参”方法）。

​	前面已经看到过这样的方法：printf。例如，下面的方法调用：

​	System.out.printf("%d",n);

和

​	System.out.printf("%d %s",n,"widgets");

在上面两条语句中，尽管一个调用包含两个参数，另一个调用包含三个参数，但它们调用的都是同一个方法。

printf方法是这样定义的：

```java
public class PrintStream
{

public PrintStream printf(String fmt , Object... args) { return format(fmt, args); } 

}
```

这里的省略号。。。是Java代码的一部分，它代表这个方法可以接收任意数量的对象（除 fmt参数以外）。

​	事实上，printf方法接收两个参数，一个是格式字符串，另一个是Object[]数组，其中保存着所有的参数（如果调用者提供的是整型数组或者其他基本类型的值，自动装箱功能将把它们转换成对象）。现在将扫描fmt字符串，并将第i个格式说明符与args[i]的值匹配起来。

​	换句话说，对于printf的实现者来说，Object...参数类型与Object[]完全一样。

​	编译器需要对printf的每次调用进行转换，以便将参数绑定到数组上，并在必要的时候进行自动装箱：

System.out.printf("%d %s",new Object[]{new Integer(n),"widgets"});

​	用户自己也可以定义可变参数的方法，并将参数指定为任意类型，甚至是基本类型。下面是一个简单的示例：其功能为计算若干个数值的最大值。

```java
public static double max(double...values)
{
    double largest = Double.NEGATIVE_INFINITY;
    for(double v: values)if(v>largest)largest = v;
    return largest;
}
```

可以像下面这样调用这个方法：

​	double m = max（3.1,40.4,-5）;

编译器将new double[]{3.1,40.4,-5}传递给max方法。

**注释**： 允许将一个数组传递给可变参数方法的最后一个参数。 例如：

System.out.printf("%d %s“ new Object[] { new Integer(1), "widgets" } );

因此， 可以将已经存在且最后一个参数是数组的方法重新定义为可变参数的方法，而不会破坏任何已经存在的代码。例如， MessageFormat.format 在 Java SE 5.0 就采用了这种方式。甚至可以将 main 方法声明为下列形式：

public static void main(String... args)

****

### 3.6 枚举类

​	定义枚举类型。以下是一个典型例子：

​	public enum Size{SMALL , MEDIUM, LARGE, EXTRA_LARGE};

实际上，这个声明定义的类型是一个类，它刚好有四个实例，在此尽量不要构造新对象。

​	因此，在比较两个枚举类型的值时，永远不需要调用equals，而直接使用”==“就可以。

​	如果需要的话，可以在枚举类型中添加一些构造器、方法和域。当然，构造器只是子构造枚举常量的时候被调用。下面为一个示例：

​	

```java
public enum Size
{
    SMALL("S"),MEDIUM("M"),LARGE("L"),EXTRA_LARGE("XL");
    private String abbreviation;
    private Size(String abbreviation)}{this.abbreviation = abbreviation;}
public String getAbbreviation(){return abbreviation;}
}
```

​	所有的枚举类型都属Enum类的子类。它们继承了这个类的许多方法。其中最有用的一个是toString，这个方法能返沪枚举常量名。例如， Size.SMALL.toString( ) 将返回字符串

“ SMALL”

​	toString的逆方法是静态方法valueOf。例如，语句：

​	Size s = Enum.valueof(Size.class,"SMALL");

将s设置成Size.SMALL

​	每个枚举类型都有一个静态的values方法，它将返回一个包含全部枚举值的数组。例如

​	Size[] values = Size.values();

返回包含元素Size.SMALL,Size.MEDIUM,Size.LARGE和Size.EXTRA_LARGE的数组。

​	ordinal方法返回enum声明中枚举常量的位置，位置从0开始计数。例如：Size.MEDIUM.ordinal()返回1。

**API:java.lang.Enum< E>5.0**

* static Enum valueOf(Class enumClass,String name)

  返回指定名字、给定类的枚举常量。

* String  toString()

  返回枚举常量名。

* Int ordinal()

  返回枚举常量在enum声明中的位置，位置从0开始计数。

* int compareTo(E other)

  如果枚举常量出想在other之前，则返回一个负值，如果this==other,则返回0；否则，返回正值。枚举常量的出现次序在enum声明中给出。

****

### 3.7 反射

​	反射库（ reflection library) 提供了一个非常丰富且精心设计的工具集， 以便编写能够动态操纵 Java 代码的程序。这项功能被大量地应用于 JavaBeans 中， 它是 Java组件的体系结构(有关 JavaBeans 的详细内容在卷 II 中阐述)。使用反射， Java 可以支持 Visual Basic 用户习惯使用的工具。特别是在设计或运行中添加新类时， 能够快速地应用开发工具动态地查询新添加类的能力。

​	能够分析类能力的程序称为反射。反射机制的功能极其强大，在下面可以看到，反射机制可以用来：

* 在运行时分析类的能力。
* 在运行时查看对象，例如，编写一个toString方法供所有类使用。
* 实现通用的数组操作代码。
* 利用Method对象，这个对象很像C++中的函数指针。

反射是一种功能强大且复杂的机制。 使用它的主要人员是工具构造者，而不是应用程序员。如果仅对设计应用程序感兴趣， 而对构造工具不感兴趣， 可以跳过本章的剩余部分， 稍后再返回来学习。

## 四、集合

### 4.1 List（列表）

* List是元素有序并且可以重复的集合，称为序列
* List可以精确的控制每个元素的插入位置，或删除某个位置的元素
* List的两个主要实现类是ArrayList和LinkedList

#### 4.1.1 ArrayList

* ArrayList底层是由数组实现的
* 动态增长，以满足应用程序的需求
* 在列表尾部插入或删除数据非常有效
* 更适合查找或更新元素
* 其中的元素是可以为null

*****

### 4.2 Set

* Set是元素无序并且不可以重复的集合，被称为集

#### 4.2.1 HashSet

* HashSet是Set的一个重要实现类，称为哈希集
* HashSet中的元素无序并且不可以重复
* HashSet中只允许一个null元素
* 具有良好的存取和查找性能

#### 4.2.2 Iterator 迭代器

* Iterator接口可以以统一的方式对各种集合元素进行遍历
* hasNext()方法检测集合中是否还有存在下一个元素
* next()方法返回集合中的下一个元素

******

### 4.3 集合排序

1. 作为基本数据类型和字符串的集合来说，直接使用Collections.sort方法直接根据大小进行升序排列
2. 需要根据集合中的对象的某一属性进行排序时，有Comparetor和Comparable两个接口 
   * Comparetor接口是新建一个比较类来实现，重写compareTo方法可以实现多个不同的类以此来实现不同的排序
   * Comparable在定义类的时候来实现，只能实现一种排序方法 同样是重写compareTo方法



****

## 五、线程

### 5.1 创建线程的三种方式

创建多线程有三种方式

* 继承Thread类。重写run()方法，run()方法代表线程要执行的任务
* 实现Runnable接口，重写run()方法，run()方法代表线程要执行的任务
* 实现callable接口，重写call()方法，call()作为线程的执行体，具有返回值，并且可以对异常进行声明和抛出；使用start()方法来启动线程
  * 创建Callable接口的实现类，并实现call()方法，该call()方法作为线程执行体，并且有返回值。
  * 创建Callable实现类的实例，使用FutureTask类来包装Callable对象，该FutureTask对象封装了该Callable对象的call()方法的返回值。
  * 使用FutureTask对象作为Thread对象的target创建并启动新线程
  * 调用FutureTask对象的get()方法来获得子线程执行结束后的返回值

## 六、输入输出流

### 6.1 File类

* 在Java中，使用java.io.File类对文件进行操作

### 6.2FileInputStream

* 从文件系统中的某个文件中获得输入字节
* 用于读取诸如图片数据之类的原始字节流

| 方法名                                    | 描述                                                   |
| ----------------------------------------- | ------------------------------------------------------ |
| public int read()                         | 从输入流中读取一个数据字节                             |
| public int read(byte[] b)                 | 从输入流中将最多b.length个字节的数据读入一个byte数组中 |
| public int read(byte[] b,int off,int len) | 从输入流中将最多len个字节的数据读入byte数组中          |
| public void close()                       | 关闭此文件输入流并释放与此流有关的所有系统资源         |

****

### 6.3 FileOutputStream

| 方法名                                       | 描述                                                       |
| -------------------------------------------- | ---------------------------------------------------------- |
| public void write(int b)                     | 将指定字节写入此文件输出流                                 |
| public void write(byte[] b)                  | 将b.length个字节从指定byte数组写入此文件输出流中           |
| public void write(byte[] b,int off, int len) | 将指定byte数组中从偏移量off开始的len个字节写入此文件输出流 |
| public void close()                          | 关闭此文件输入流并释放与此流有关的所有系统资源             |

*****

### 6.4 缓冲流

* 主要作用就是加快输入输出的速度



****

### 6.5 字符字节转换流

* InputStreamReader
* OutputStreamWriter

### 6.6 对象序列化

* 在网络中传输文件的时候不可能只是文本信息，一般都是以类的对象传输的所以对象序列化十分重要
* ObjectInputStream
* ObjectOutputStream



## 七、Java多态

### 7.1 内部类

​	在Java中，程序是由类构建而成的。而在一个类的内部也可以声明类，这样包含在一个类中的类被称为内部类，包含此类的类被称为外部类

**特点**：

1. 内部类定义为public时可以与文件名不同
2. 内部类一旦编译成功，会成为完全不同的两类。outer.class和outer$inner.class
3. 内部类可以通过private修饰
4. 通过内部类可以更好的体现封装以及代码编写的灵活性
   * 当内部类声明为private时，只有外部类可以访问，很好的隐藏了内部类的信息
   * 内部类可以继承（extends）或实现（implements）其他的类或者接口，不受到外部类的影响
   * 内部类可以直接访问外部类的字段和方法，即使是使用private修饰的，而外部类不能直接访问内部类的成员
   * 内部类在实现线程调用或事件处理方面更加的灵活

#### 7.1.1 成员内部类

1. 内部类在外部使用时，无法直接实例化，需要借由外部类信息才能完成实例化
2. 内部类的访问修饰符，可以任意，但是访问范围会受到影响
3. 内部类可以直接访问外部类的成员；如果出现同名属性，优先访问内部类中定义的
4. 可以使用外部类、this.成员的方式，访问外部类中同名的信息
5. 外部类访问内部类信息，需要通过内部类实例，无法直接访问
6. 内部类编译后.class文件命名：外部类$内部类.class
7. 内部类中可以包含与外部类相同方法签名的方法



#### 7.1.2 静态内部类

1. 静态内部类中，只能直接访问外部类的静态成员，如果需要调用非静态成员，可以通过对象实例
2. 静态内部类对象实例时，可以不依赖与外部类对象
3. 可以通过外部类、内部类、静态成员的方式，访问内部类中的静态成员
4. 当内部类属性与外部类属性同名时，默认直接调用内部类中的成员；如果访问外部类中的静态属性，则可以通过 外部类.属性 的方式
5. 如果需要访问外部类中的非静态属性，则可以通过new外部类（）.属性的方式



#### 7.1.3  方法内部类

1. 定义在方法内部，作用范围也在方法内
2. 和方法内部成员使用规则一样，class前面不可以添加public、private、protected、static
3. 类中不能包含静态成员
4. 类中可以包含final、abstract修饰成员变量



#### 7.1.4 匿名内部类

1. 匿名内部类没有类型名称、实例对象名称
2. 编译后的文件命名：外部类$数字.class
3. 无法使用修饰字符
4. 无法编写构造方法、可以添加构造代码块
5. 不能出现静态成员
6. 匿名内部类可以实现接口也可以继承父类，但不可兼得

### 7.2 内部类的常见问题

#### 7.2.1 方法内部类为什么不能拥有静态成员？

  由于static类型的属性和方法，在类加载的时候就会存在于内存中。因此要想使用某个类的static属性和方法，这个类就必须要加载到虚拟机中，但是普通的方法内部类并不会和外部类一一起加载，只有在外部类实例化的时候才加载。因此，如果在外部类没有实例化，内部类还没有加载的时候，直接调用内部类的static方法或者成员，这是十分矛盾的。

********

#### 7.2.2 什么是方法签名？

​	在Java中，方法签名着重指代：方法名和参数列表（包括：参数的类、个数、顺序）

例如：下面代码所示的方法签名为：exam(String,Teacher,double)，可以不考虑返回值与访问修饰符

```Java
public String exam(String subject,Teacher invigilator,double score){
	return "";
}
```

#### 7.2.3 为什么要写getRead()方法，都直接卸载main方法里不可以吗

```java
public void getRead(Person person){
	person.read();
}
```

将功能拆分成方法，而不是都写死在main方法中，一方面可以多角度体现不同方式对象传参的应用；另一方面实际开发中也推荐按功能合理拆分，以便于后续拓展维护。

