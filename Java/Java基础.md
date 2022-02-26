# Java基础

## Java简介

### JVM、JRE、JDK

- **JVM**：Java虚拟机（JVM）针对不同操作系统有特定的实现，可在不同系统中运行相同的字节码文件(Class文件)，并得到相同的结果，保证了Java的**平台无关性**。
- **JRE**：Java运行时环境（Java Runtime Env），包括：JVM、Java系统类库等，只能运行编译好的Java程序。
- **JDK**：Java开发工具包（Java Development Kit），包括：JRE、编译器（Javac）、Java其他工具（jar、jdb、javadoc、javap(反汇编器)等）、

### 字节码

字节码（ByteCode）：是不同平台的JVM使用的**统一程序存储格式**，JVM只与由字节码组成的Class文件进行交互。

Java文件（.java）经 编译器（Javac）编译产生 Class文件（.class），Class文件 经 JVM加载并通过**解释器**逐行解释执行，这种方式执行速度相对较慢（后面引进了 JIT（just-in-time compilation） 编译器，而 JIT 属于运行时编译。当 JIT 编译器完成第一次编译后，其会将字节码对应的机器码保存下来，下次可以直接使用）。所以称 **Java 是编译与解释共存的语言** 。

### 编码方式

Java采用**Unicode**编码。**Unicode**（万国码、国际码、统一码）是计算机科学领域里的一项业界标准。对世界上大部分的文字系统进行了整理、编码，使得计算机可以用更为简单的方式来呈现和处理文字。常用的ASCII码是Unicode的子集。

广义的 Unicode 是一个标准，定义了一个字符集以及一系列的编码规则，即 Unicode 字符集和 UTF-8、UTF-16、UTF-32 等等编码规则。

## 数据类型

### 基本数据类型

- 字符型：char/16（默认值：'\u0000'）
- 整型：byte/8、short/16、int/32（默认值：0）、long/64（默认值：0L、0l）
- 浮点型：float/32（默认值：0f）、double/64（0d）
- 布尔型：boolean（默认值：false）。boolean 只有两个值：true、false，可以使用 1 bit 来存储，但是具体大小没有明确规定。JVM 会在编译时期将 boolean 类型的数据转换为 int，使用 1 来表示 true，0 表示 false。

### 包装类型

1. 基本类型对应的包装类大部分都实现了常量池（缓存池）技术。

- Byte、Short、Integer、Long 对应的缓存数据都是 [-128，127]

- Character 对应的缓存数据是 [0，127]

- Boolean则直接返回 TRUE / FALSE

- 如果传入的基本数据类型大小超出缓存池范围，则会在堆区新建一个对象。如Integer：

  ```java
  public static Integer valueOf(int i) {
      if (i >= IntegerCache.low && i <= IntegerCache.high)//先判断是否在缓存池中，Java8中缓存池大小-128~127
          return IntegerCache.cache[i + (-IntegerCache.low)];
      return new Integer(i);
  }
  //因此：
  Integer x = new Integer(123);
  Integer y = new Integer(123);
  System.out.println(x == y); // false
  Integer z = Integer.valueOf(123);
  Integer k = Integer.valueOf(123);
  System.out.println(z == k); // true
  Integer m = Integer.valueOf(123);
  Integer n = Integer.valueOf(200);
  System.out.println(m == n); // false
  ```

2. 自动装箱与拆箱

   ```java
   Integer x = 2; // 装箱 调⽤了 Integer.valueOf(2)
   int y = x;     // 拆箱 调⽤了 X.intValue()
   ```

### 包装类常用方法



## 基本语法和运算

### 标识符命名

- 所有的标识符都应该以字母（A-Z 或者 a-z）,美元符 $ 、或者下划线 _ 开始
- 首字符之后可以是字母（A-Z 或者 a-z）,美元符 $ 、下划线（_）或数字的任何字符组合
- 关键字不能用作标识符,标识符是大小写敏感的
- 合法标识符举例：age、$salary、_value、__1_value；非法标识符举例：123abc、-salary、abc*

### 数组

```java
int[] a = new int[5];//可存储5个元素的一维数组
int[] b = {1,2,3,4};

int[][] c = new int[1][2];//多维数组
int[][] d = new int[3][];
d[0] = new int[3];//为每一维分配不同的空间

//遍历数组
for(int i = 0; i < a.length; i++){}
for(int elem : a){}
```

### 运算符

- 自增++、自减--：前缀自增自减(++a)会先执行自增自减运算在得到值，而后缀的会先得到值再进行自增自减运算

  ```java
  int	x = 1,
      y = 2,
      z = 3;
  System.out.println("" + (y += z--/++x)); //此时z=3,x=2, 所以y=3
  System.out.println(x + " " + z);//此时z=2，x=2
  ```

- “>>” ：按位右移运算符（ 正数高位补0，负数高位补1 ）

- “>>>” ：按位右移补零操作符（ 无论正负，高位均补0 ）

- &&：短路逻辑运算符。即当A&&B，若A为假，则B不再进行判断；同样的A||B，A为真则B不再判断。

### 类型转换

- **自动类型转换**：也称拓宽转换
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210526021159929.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tra2RqYmI=,size_16,color_FFFFFF,t_70)

- **强制类型转换**：语法`tar = （targetValue）value`，此时会发生截断。

  - 如小数赋值给整数`int a = (int) 1.234`，则小数部分会被舍去
  - 如果浮点值太大而不能适合目标整数类型，那么它的值将会因为对目标类型**值域取模**而减少。

- **隐式类型转换**：主要与字面量有关

  ```java
  //下面两行都不能隐式向下转型，二者都会直接报错
  float f = 1.1;		// 1.1属于double型字面量，而double精度更高
  short a = 1000000;	// 1000000属于int型字面量，而int精度更高
  //应改为
  float f = 1.1f;
  short a = (short)1000000;
  //但应注意的是，使用 ++ 或 += 时，会自动隐式转换
  short b = 1;
  b += 1;  //相当于 (short) (b + 1);
  System.out.println(b);
  ```

- **表达式中类型的自动提升**：看如下代码，当分析表达式时，Java自动提升各个byte型或short型的操作数到int型

  ```java
  byte a = 40;
  byte b = 50;
  int d = a * b;
  //但是自动提升会使得下面的代码出问题
  byte b = 50;
  b = b * 2;      // 计算时，b被自动提升为int型，在赋值给b后会出现类型不匹配问题
  b = (byte)b * 2;// 因此需要这样写
  ```

  - 自动提升的约定：如果一个操作数是long型，整个表达式将被提升到long型；如果一个操作数是float型，整个表达式将被提升到float型；如果有一个操作数是double型，计算结果就是double型。

### 值传递

Java 的参数是以**值传递**的形式传⼊⽅法中，⽽不是引⽤传递。因此调用方法 fun(String a) 时，相当于将**对象a的地址**以**值的方式**传入方法中，所以**直接改变参数a，原对象的值也会改变**；但若使 a = new String(“xxx”)，此时再改变a，对原对象就无影响了。

### 可变长参数

可变长参数：从Java5开始，允许在调用方法时传入不定长度的参数。如：

```java
public void fun(String... args) {  // 可变长参数在编译后会被转换成一个数组
    for(String arg : args)
}
```

注：

- 可变参数只能作为函数的**最后一个参数**
- 如果遇到方法重载，会**优先匹配固定参数**的方法

## 关键字

### static

- **静态变量(类变量)**：`static int y`，**类的所有实例都共享静态变量**，可以直接通过类名来访问它。静态变量在内存中只存在⼀份，局部变量不能被声明为 static 变量。

- **静态常量**：如`Math`类中有`public static final double PI = 3.1215...;`，则可通过`Math.PI`直接调用。

- **静态方法**：静态变量和方法在**类加载的时候就存在了**，它不依赖于任何类的实例，所以静态⽅法必须有实现（即不能是抽象⽅法）。

  - 在**静态方法中只能访问所属类的静态字段和静态⽅法**，⽅法中不能有 this 和 super 关键字，因为这两个关键字与具体对象关联。
  - 而实例方法可以访问静态字段、静态⽅法、实例字段和实例⽅法

- **静态语句块**：`static {...}`，静态语句块在类初始化时运⾏⼀次。

- **静态内部类**

  - **静态内部类**的实例可以直接再`main`方法中创建，但不能访问外部类的非静态的变量和⽅法
  - **非静态内部类**依赖于外部类的实例，也就是说需要先创建外部类实例，才能⽤这个实例去创建非静态内部类。

- **初始化顺序**

  - 首先，初始化 **静态变量**和**静态语句块**（初始化顺序取决于它们在代码中的顺序）；

  - 然后，初始化 **实例变量**和**普通语句块**（初始化顺序取决于它们在代码中的顺序）；

  - 最后，初始化**构造函数**

  - 若有**继承**关系，则初始化顺序如下：

    ```java
    1. ⽗类（静态变量、静态语句块）
    2. ⼦类（静态变量、静态语句块）
    3. ⽗类（实例变量、普通语句块）
    4. ⽗类（构造函数）
    5. ⼦类（实例变量、普通语句块）
    6. ⼦类（构造函数）
    ```

### final

- 用于**数据类型**前：声明数据为常量，可以是**编译时常量**，也可以是在**运⾏时被初始化后不能被改变的常量**。
  - 对于**基本类型**， final 使**数值不变**： `final double PI = 3.141`，一般被final修饰的常量均大写。
  - 对于**引⽤类型**，final 使**引⽤(地址)不变**，即**不能指向其它对象**，但是**被引⽤的对象的属性本身是可以修改的**
- 用于**方法**前：声明**⽅法不能被⼦类重写**。
  - `private`方法隐式的指定为`final`类型，此时若父类与子类中`private`方法名称相同，则不能认为是复写了子类的方法，而是在子类中定义了一个新的方法。
- 用于**类**前：声明类不允许被继承。

### instanceof

`instanceof`：用于检查对象是否是一个特定类型（类或接口），返回`true/false`。

```java
String name = "str";
boolean res = name instanceof String;  //由于name是String类型，返回true
boolean res = child instanceof parent; //由于child继承parent，返回true
```

## 面向对象

### 面向对象与面向过程

- 面向过程：将问题分解成多个步骤，每个步骤用函数实现，最后再依次调用；
- 面向对象：将事物或问题的属性、行为等封装成对象，然后进行业务逻辑的实现。
  - 面向对象开发的程序一般更易维护、易复用、易扩展。

### 封装、继承、多态

面向对象的三大特征：

- **封装**：将客观事物的**数据和操作方法**封装成抽象的类。在对象的内部，一些数据和方法可以是私有的，禁止外界访问，也可以提供`get/set`方法来操纵数据。
- **继承**：
- **多态**：

### 访问权限

## 常用类

### String

### Math

### Object

### Collections

### 时间

### 枚举Enum

## 动态代理

## 序列化

transient

## 异常

## 泛型

## 反射

## 注解

## Java8新特性

## 参考

- [https://snailclimb.gitee.io/javaguide/#/](https://snailclimb.gitee.io/javaguide/#/)
- [https://github.com/CyC2018/CS-Notes/blob/master/notes/Java%20%E5%9F%BA%E7%A1%80.md](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java%20%E5%9F%BA%E7%A1%80.md)
- [https://hollischuang.gitee.io/tobetopjavaer/#/menu](https://hollischuang.gitee.io/tobetopjavaer/#/menu)

