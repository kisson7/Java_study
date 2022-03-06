# 基础概念
### JVM JRE JDK
JDK 用来开发的工具，面向开发人员
JRE java运行环境，面向java文件
JVM java虚拟机，解释class文件（字节码文件）成机器码，让不同的操作系统都能够执行。

1. bin（JVM） + lib（类库）= JRE

2. JRE + java工具（javac,java,jconsole）=JDK

3. `java是解释与编译共存的语言`，原因在于.class -> 机器码这一步，JVM类加载器先加载字节码文件，然后通过解释器逐行解释，速度很慢（而且有些方法和代码块会被反复调用）。后面引入了`JIT编译器`，采用运行时编译的方式，会将第一次编译后字节码对应的机器码保留下来，下次可以直接使用。
***
### 包装类型
1. 8种基本数据类型都有对应的包装类型，包装类型不赋值就是`null`

2. Byte，Short，Integer，Long都实现了常量池技术（默认创建[-128,127]的相应类型的缓存数据），Character创建了[0, 127]的缓存数据，Boolean直接返回True或False。

```java
Integer i1 = 40; //发生装箱，等价于Integer i1 = Integer.valueOf(40)。i1直接使用常量池中的对象
Integer i2 = new Integer(40);  //创建新的对象
System.out.println(i1==i2);
```
3. **所有整型包装类对象之间值的比较，全部使用 equals 方法比较**
![](https://img2022.cnblogs.com/blog/2453617/202203/2453617-20220304213839129-1642436128.png)

***

### 拆箱、装箱
```
Integer i = 10;  //装箱, ->调用了valueOf()方法 
int n = i;   //拆箱，-> intValue()方法
```
等价于
```
Integer i = Integer.valueOf(10);
int n = i.intValue();
```
**频繁拆装箱，严重影响性能，避免该类型操作**
***

### == 和 equals
1. ==对比的是栈中的值，对于基本类型，**对比变量值**，引用类型对比**堆中对象的地址**。

2. equals存在于object中，默认采用==比较，通常需要重写
（当创建String类型的对象时，虚拟机会在`常量池中查找有没有存在的值和要创建的值相同的对象`，如果有就把它赋给当前引用，没有就在常量池中重新创建一个String对象。）

```java
String a = new String("ab"); // a 为一个引用
String b = new String("ab"); // b为另一个引用,对象的内容一样
String aa = "ab"; // 放在常量池中
String bb = "ab"; // 从常量池中查找
System.out.println(aa == bb);// true
System.out.println(a == b);// false
System.out.println(a.equals(b));// true
System.out.println(42 == 42.0);// true
```
****
### hashCode和equals

hashCode()作用是获取哈希码（散列码），返回一个int整数，作用是确定该对象在哈希表中的索引位置。定义在JDK的Object.java中，是一个**native**方法，java中任何类都含有hashCode()函数。散列表中存储的是key-value，特点是能够根据key快速检索出对应的值。

+ **以HashSet()如何检查重复为例子来说明为什么要有hashCode：**
对象加入HashSet时，计算hashcode值判断对象加入的位置，如果该位置有值，会调用`equals`方法检查两个对象是否相同。相同不会让该对象加入进去，不同则重新散列（*特殊处理*）到其他位置。**这样就减少了equals的次数，提高了执行速度**。

1. 两个对象相等，hashcode一定相等

2. 两个对象相等，分别调用equals都返回true

3. 两个对象相同hashcode，它们不一定相等（哈希碰撞，越糟糕的哈希算法越容易发送碰撞）

4. equals方法被重写过，hashCode()也必须重写（`因为1、2，若equals被重写了，但hashCode()没被重写，就可能导致equals判断相等的两个值，hashCode值却不同`）

5. hashCode()默认行为是对堆上的对象产生独特值。若未被重写，则该class的两个对象无论如何都不会相等（即使指向相同的数据）
***
### 成员变量和局部变量

1. **语法形式：**成员变量属于类，局部变量在代码块或方法中定义的变量或是方法的参数；成员变量可以被private、public、static修饰，局部变量不能。但是成员和局部都可以被final修饰

2. **存储方式：**如果成员变量被static修饰，则属于类，如果没有，则属于实例。对象存在于堆内，局部变量则存在于栈内。

3. **生存时间：**成员变量是对象的一部分，随着对象的创建而存在，而局部变量随着方法的调用而自动消失。

4. **默认值：**成员变量没有赋初值，则自动以类型的默认值赋值（例外：被final修饰的成员变量必须显式地赋值），局部变量不会自动赋值；
***
### 创建对象
+ 创建对象用new，创建的对象实例在堆内存中，对象引用指向对象实例（对象引用放在栈内存中）

+ 为什么创建对象的时候加一个括号？因为要调用对象的无参的构造方法。

+ 构造方法不能被重写，可以被重载

***
### 封装、继承、多态

1. 封装
把一个对象的状态信息隐藏在内部，不允许外部对象直接访问对象的内部信息。`属性私有化`

2. 继承
子类拥有父类所有属性和方法，但是私有方法和属性无法访问，`只是拥有`
子类可以拥有自己的属性和方法，即对父类扩展
子类可以用自己的方式实现父类的方法

3. 多态
具体表现为父类的引用指向子类的实例
引用类型的变量调用哪个类中的方法只有在`程序运行时`才知道
多态不能调用只在子类存在不在父类中存在的方法
如果子类重写了父类的方法，执行的是子类覆盖的方法，否则执行父类

***
### 接口和抽象类
+ 共同点
都不能被实例化
都可以包含抽象方法
都可以有默认实现的方法（**java 8可以用default关键字在接口中定义默认方法**）

+ 区别
接口主要对类的行为约束，抽象类用于代码复用（强调所属关系）
接口可以多继承，抽象类不能
接口成员变量只能是public static final类型，不能被修改且必须有初始值，抽象类成员变量默认default，可以在子类中重新被定义与赋值。  
***

### 深拷贝和浅拷贝

+ 浅拷贝：在堆上创建一个新的对象（`区别于引用拷贝`），如果原对象内部属性是引用类型，浅拷贝会直接复制内部对象的引用地址，即拷贝对象和原对象共用一个内部对象。

+ 深拷贝：完全复制整个对象，包括对象包含的内部对象。

+ **浅拷贝只复制所考虑的对象，不复制它引用的对象**

***
### List、Set、Queue、Map
+ List：`有序`，按对象进入`顺序`保存对象，`可重复`，允许多个null元素对象，可以使用Iterator取出所有元素逐一遍历，还可以使用get(int index)获取指定下表的对象

+ Set：`无序`，`不可重复`，最多允许一个null元素对象，只能用Iterator接口取得所有元素，再逐一遍历

+ Queue：`先后顺序`，`有序`，`可重复`

+ Map ：键值对存储，key`无序`、`不可重复`，value`无序`、`可重复`。每个键最多一个值

***
###ArrayList和LinkedList的区别


***
### 方法

+ 静态方法为什么不能调用非静态方法？


***
### final
修饰类：不可被继承
修饰方法：不可被重写，但是可以被重载
修饰变量：变量一经赋值，不可更改

+ 修饰成员变量
类变量：只能在静态初始化块中指定初始值或者声明该变量时指定初始值
成员变量：非静态初始化块、声明或在构造器中执行初始值

+ 修饰局部变量
必须由程序员初始化，可以定义时不指定，后面再赋（仅一次）

+ 修饰基本类型和引用类型
基本类型：不能更改
引用类型：不能指向另一个对象，但是引用的值可以修改

+ 为什么局部内部类和匿名内部类只能访问局部final变量
内部类和外部类是同一级别，不会因为定义在方法中随着方法执行完毕就被销毁
矛盾1：因为垃圾回收机制，导致外部类执行完毕，局部变量就被销毁，而内部类对象访问了一个不存在的变量。解决方法是copy一份给内部类。
矛盾2：但必须保证两个变量一致，如果内部类中修改了成员变量，方法中的局部变量也要跟着变，怎么办？所以需要妥协，即在局部变量上加一个final。
***
### String、StringBuilder、StringBuffer

1. String
+ `final修饰的`，不可变，每次操作都会产生新的对象（s+"d"）
浪费内存，不断创建对象

+ `+`和`+=`是专门为String重载过的运算符（也是仅有的两个），其实际过程是用StringBuilder调用append()实现的，拼接完成后调用toString()得到一个String对象。
因此String不适合在循环内使用+或+=的拼接方式，**编译器不会创建单个StringBuilder进行复用**，每一次循环都会单独创建一个StringBuilder对象。

+ `String#equals()`和`Object#equals()`的区别：String中的equals方法是被重写过的，比较的是字符串的值是否相等，Object比较的是对象的内存地址。

+ **字符串常量池**是JVM为了提高性能和减少内存消耗针对String专门开辟的一块区域，目的是为了避免字符串的重复创建。
```
String aa = "ab"; // 放在常量池中
String bb = "ab"; // 从常量池中查找
System.out.println(aa==bb);// true
```
(*JDK1.7 之前运行时常量池逻辑包含字符串常量池存放在方法区。JDK1.7 的时候，字符串常量池被从方法区拿到了堆中。*)

2. StringBuilder
原对象上操作
线程不安全（**多线程环境、共享变量，结果是正常的，不会被更改，是预计结果，不会出现并发问题，即线程安全**）

3. StringBuffer
原对象上操作
线程安全（因为里面的方法都是synchronized修饰的）
对方法加了同步锁，或对调用的方法加了同步锁  

性能：StringBuilder > StringBuffer > String
***
### 重载和重写

+ 重载：同一个类中，方法名相同，参数类型、个数、顺序，*方法返回值、访问修饰符*都可以不同（编译报错），发生在编译时

+ 重写：父子类中，方法名、参数列表必须相同，**返回值范围小于父类**，**抛出异常范围小于父类**，**访问修饰符范围大于父类**；子类不能重写**private**方法

***
### 接口和抽象类

1. 抽象类可存在普通成员函数，接口只能存在public abstract方法中
2. 抽象类中成员变量可以是各种类型的，接口中成员变量只能是**public static final**类型的
3. 抽象类只能继承一个，接口可以继承多个
***

### 泛型
Java 泛型（generics） 是 JDK 5 中引入的一个新特性, 泛型提供了编译时类型安全检测机制，该机制允许程序员在编译时检测到非法的类型。泛型的本质是`参数化类型`，也就是说所操作的数据类型被指定为一个参数。 Java 的泛型是伪泛型，这是因为 Java 在运行期间，所有的泛型信息都会被擦掉，这也就是通常所说类型擦除 。

1. 泛型类
```
//此处T可以随便写为任意标识，常见的如T、E、K、V等形式的参数常用于表示泛型
//在实例化泛型类时，必须指定T的具体类型
public class Generic<T>{ 
    //key这个成员变量的类型为T,T的类型由外部指定  
    private T key;

    public Generic(T key) { //泛型构造方法形参key的类型也为T，T的类型由外部指定
        this.key = key;
    }

    public T getKey(){ //泛型方法getKey的返回值类型为T，T的类型由外部指定
        return key;
    }
}
```

2. 泛型接口

```
public interface Generator<T> {
    public T method();
}
```
实现泛型接口，可以指定也可以不指定类型

3. 泛型方法

```
public static <E> void printArray(E[] inputArray) {
    for (E element : inputArray) {
        System.out.printf("%s ", element);
    }
    System.out.println();
}
```
```
// 创建不同类型数组： Integer, Double 和 Character
Integer[] intArray = { 1, 2, 3 };
String[] stringArray = { "Hello", "World" };
printArray(intArray);
printArray(stringArray);
```

4. 常用通配符: T、E、K、V？
+ ？ 不确定
+ T（type） 具体的一个java类型
+ K V（key value）表示键值中的key、value
+ E element

****

### 反射

赋予了我们在运行时，分析类和执行类中方法的能力，通过反射可以获取一个了类中的所有属性和方法，你还可以调用这些方法和属性。

1. 优缺点
**优点：** 代码更灵活，为各种框架提供开箱即用的功能提供了便利
**缺点：** 增加安全问题，比如会无视泛型参数的安全检查（编译时）。另外，性能稍差。
2. Spring框架大量使用了动态代理，动态代理的实现也依赖于反射

***

### 注解

可以看作一种特殊的注释，主要用于修饰类、方法或者变量
本质是一个继承了Annotation的特殊接口

注解只有被解析之后才会生效，常见解析方法：
+ 编译器直接扫描：编译代码时扫描对应注解并处理。比如@Override

+ 运行期通过反射处理：框架自带的注解（Spring的@Value、@Component）都是反射来处理的

***

### Exception和Error

1. 异常

   共同父类——Throwable类

   + Exception：程序本身可以处理的异常，可以通过`catch`捕获，分为Checked Exception（受检查异常，必须处理）和Unchecked Exception（不受检查异常，可以不处理）
   + Error：程序无法处理的错误，无法通过`catch`捕获，如Java虚拟机运行错误（Virtual MachineError）、虚拟机内存不够（OutOfMemoryError）、类定义错误（NoClassDefFoundError）。这些错误发生时，JVM一般会选择线程终止。

2. 受检查异常和不受检查异常区别
    没有被catch/throw处理则无法通过编译，除了RuntimeException及其子类，其他Exception类及其子类都是受检查异常。

2. Throwable类常用方法

  + String getMessage() ：异常发生的简要描述
  + String toString()：异常发生的详细信息
  + String getLocalizedMessage()：异常本地化信息。使用子类可以重写这个方法，若未重写则结果与getMessage一样

2. try-catch-finally

  + 无论是否捕获到异常，finally都会执行。若try或catch中有return语句，finally会在方法返回前执行。
  + **不要在finally中使用return**：当try和finally都有return时，try中return返回值会暂存在一个本地变量中，执行到finally中的return后，这个本地变量的值就变成了finally中的return返回值。
  + finally之前虚拟机被终止运行时，finally中的代码不会执行。

2. try-with-resources

  + Java 7新特性
  + 所有实现了java.lang.AutoCloseable接口的类都可以在其中使用

  ```java
  try (BufferedInputStream bin = new BufferedInputStream(new FileInputStream(new File("test.txt")));
               BufferedOutputStream bout = new BufferedOutputStream(new FileOutputStream(new File("out.txt")))) {
              int b;
              while ((b = bin.read()) != -1) {
                  bout.write(b);
              }
          }
          catch (IOException e) {
              e.printStackTrace();
          }
  ```

***

### I/O

1. 序列化和反序列化

   + 序列化：将数据结构或对象转换为二进制字节流的过程。（**主要目的是通过网络传输对象或将对象存储到文件系统、数据库、内存中**）
   + 反序列化：将在序列化过程中生成的二进制字节流转换为数据结构或对象的过程
   
2. transient

   阻止实例中用此关键字修饰的变量序列化；当对象被反序列化时，被transient修饰的变量值不会被持久化和恢复。

   + 只能修饰变量，不能修饰类和方法
   + 如果修饰的int类型，反序列化后结果就是0（默认值）

3. 键盘输入两种方法

   + Scanner

     ```java
     Scanner input = new Scanner(System.in);
     String s  = input.nextLine();
     input.close();
     ```

   + BufferedReader

     ```java
     BufferedReader input = new BufferedReader(new InputStreamReader(System.in));
     String s = input.readLine();
     ```

4. IO流划分

   + 流的流向：输入流、输出流
   + 操作单元：字节流、字符流
   + 流的角色：节点流、处理流

   IO流的40多个类都是从四个抽象类基类中派生出的：

   + InputStream/Reader：所有输入流的基类，前者是字节输入流，后者是字符输入流
   + OutputStream/Writer：所有输出流的基类，前者是字节输出流，后者是字符输出流

5. 字符流作用

   **不管是文件读写还是网络发送接收，信息最小存储单元都是字节，为什么还要有字符流呢?**

   + 字符流有JVM将字节流转换得到的，这个过程非常耗时，若不注意编码类型还会出现乱码问题。因此IO流提供了直接操作字符的接口，方便直接对字符操作
