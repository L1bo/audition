[TOC]

# java类型
基本类型 primitive type
枚举类型 enumeration type
引用类型 reference type

## 整型数据

### short

#### 代码1
```java
short s1 = 1;
s1 = s1 + 1;  // 1

short s2 = 1;
s2 += 1;  // 2
```
对于注释 1 来说，在 s1+1 运算时会自动提升表达式的类型为 int，所以将 int 赋予给 short 类型的变量 s1 会出现类型转换错误（无法编译，IDE 提示错误），除非主动加上强转。对于注释 2 来说 += 是 java 语法规定的运算符，所以 java 编译器会对它进行转换特殊处理，故可以正确编译执行。

### int

#### java 的 Integer 和 int 有什么区别？
int 是 java 内置基本数据类型之一，java 为每个基本类型都提供了一个封装类，Integer 就是 int 的封装类（也叫包装类型）;
int 变量的默认值为 0，Integer 变量的默认值为 null，所以 Integer 可以区分出未赋值和值为 0 的区别;
Integer 类内部提供了一些关于整数操作的方法，例如上文用到的表示整数的最大值和最小值。

## 浮点型

### float

#### java 中 3*0.1 == 0.3 将会返回什么？true 还是 false？
false，因为浮点数不能完全精确的表示出来，一般都会损失精度。

#### Java 中的 Math. round(-1.5) 等于多少？
等于 -1，因为在数轴上取值时，中间值（0.5）向右取整，所以正 0.5 是往上取整，负 0.5 是直接舍弃。

### double

### 相关

#### java 中 float f = 3.4; 是否正确？
不正确，3.4 是双精度数，将双精度型（double）赋值给浮点型（float）属于向下转型（down-casting，也称为窄化）会造成精度损失，因此需要强制类型转换 float f = (float)3.4; 或者写成 float f = 3.4F; 才可以。

## char

### java 中 char 类型变量能不能储存一个中文的汉字，为什么？
java 的 char 类型变量是用来储存 Unicode 编码字符的，Unicode 字符集包含了汉字，所以 char 类型自然就能存储汉字，但是在某些特殊情况下某个生僻汉字可能没有包含在 Unicode 编码字符集中，这种情况下 char 类型就不能存储该生僻汉字了。

## String

### String常量池
原文地址http://blog.csdn.net/gaopeng0071/article/details/11741027

关于string内存分配不错的博客：http://blog.csdn.net/rj042/article/details/6871030
#### 示例1：
```java
String s0="kvill";
String s1="kvill";
String s2="kv" + "ill";
System.out.println( s0==s1 ); // true
System.out.println( s0==s2 ); // true
```
分析：
首先，我们要知结果为道Java 会确保一个字符串常量只有一个拷贝。
因为例子中的 s0和s1中的”kvill”都是字符串常量，它们在编译期就被确定了，所以s0==s1为true；而”kv”和”ill”也都是字符串常量，当一个字 符串由多个字符串常量连接而成时，它自己肯定也是字符串常量，所以s2也同样在编译期就被解析为一个字符串常量，所以s2也是常量池中” kvill”的一个引用。所以我们得出s0==s1==s2；

#### 示例2：
```java
String s0="kvill";
String s1=new String("kvill");
String s2="kv" + new String("ill");
System.out.println( s0==s1 ); // result = false
System.out.println( s0==s2 ); // result = false
System.out.println( s1==s2 ); // result = false
```
分析：
用new String() 创建的字符串不是常量，不能在编译期就确定，所以new String() 创建的字符串不放入常量池中，它们有自己的地址空间。
s0还是常量池 中"kvill”的应用，s1因为无法在编译期确定，所以是运行时创建的新对象”kvill”的引用，s2因为有后半部分 new String(”ill”)所以也无法在编译期确定，所以也是一个新创建对象”kvill”的应用;明白了这些也就知道为何得出此结果了。

#### 示例3：
```Java
String a = "a1";
String b = "a" + 1;
System.out.println((a == b)); //result = true 
String a = "atrue";
String b = "a" + "true";
System.out.println((a == b)); //result = true 
String a = "a3.4";
String b = "a" + 3.4;
System.out.println((a == b)); //result = true
```
分析：
JVM对于字符串常量的"+"号连接，将程序编译期，JVM就将常量字符串的"+"连接优化为连接后的值，拿"a" + 1来说，经编译器优化后在class中就已经是a1。在编译期其字符串常量的值就确定下来，故上面程序最终的结果都为true。

#### 示例4：
```java
String a = "ab";
String bb = "b";
String b = "a" + bb;
System.out.println((a == b)); //result = false
```
分析：
JVM对于字符串引用，由于在字符串的"+"连接中，有字符串引用存在，而引用的值在程序编译期是无法确定的，即"a" + bb无法被编译器优化，只有在程序运行期来动态分配并将连接后的新地址赋给b。所以上面程序的结果也就为false。

#### 示例5：
```java
String a = "ab";
final String bb = "b";
String b = "a" + bb;
System.out.println((a == b)); //result = true
```
分析：
和[4]中唯一不同的是bb字符串加了final修饰，对于final修饰的变量，它在编译时被解析为常量值的一个本地拷贝存储到自己的常量 池中或嵌入到它的字节码流中。所以此时的"a" + bb和"a" + "b"效果是一样的。故上面程序的结果为true。

#### 示例6：
```java
String a = "ab";
final String bb = getBB();
String b = "a" + bb;
System.out.println((a == b)); //result = false
private static String getBB() {  return "b";   }
```
分析：
JVM对于字符串引用bb，它的值在编译期无法确定，只有在程序运行期调用方法后，将方法的返回值和"a"来动态连接并分配地址为b，故上面 程序的结果为false。

#### 关于String是不可变的
通过上面例子可以得出得知：
```java
String  s  =  "a" + "b" + "c";
```
就等价于String s = "abc";

```java
String  a  =  "a";
String  b  =  "b";
String  c  =  "c";
String  s  =   a  +  b  +  c;
```
这个就不一样了，最终结果等于：

```java
　　StringBuffer temp = new StringBuffer();
　　temp.append(a).append(b).append(c);
　　String s = temp.toString();
```

由上面的分析结果，可就不难推断出String 采用连接运算符（+）效率低下原因分析，形如这样的代码：
```java
public class Test {
    public static void main(String args[]) {
        String s = null;
        for(int i = 0; i < 100; i++) {
            s += "a";
        }
    }
}
```
每做一次 + 就产生个StringBuilder对象，然后append后就扔掉。下次循环再到达时重新产生个StringBuilder对象，然后 append 字符串，如此循环直至结束。如果我们直接采用 StringBuilder 对象进行 append 的话，我们可以节省 N - 1 次创建和销毁对象的时间。所以对于在循环中要进行字符串连接的应用，一般都是用StringBuffer或StringBulider对象来进行 append操作。

由于String类的immutable性质,这一说又要说很多，大家只 要知道String的实例一旦生成就不会再改变了，比如说：String str=”kv”+”ill”+” “+”ans”; 就是有4个字符串常量，首先”kv”和”ill”生成了”kvill”存在内存中，然后”kvill”又和” ” 生成 “kvill “存在内存中，最后又和生成了”kvill ans”;并把这个字符串的地址赋给了str,就是因为String的”不可变”产生了很多临时变量，这也就是为什么建议用StringBuffer的原 因了，因为StringBuffer是可改变的。

#### String中的final用法和理解
```java
final StringBuffer a = new StringBuffer("111");
final StringBuffer b = new StringBuffer("222");
a=b;//此句编译不通过
final StringBuffer a = new StringBuffer("111");
a.append("222");// 编译通过
```
可见，final只对引用的"值"(即内存地址)有效，它迫使引用只能指向初始指向的那个对象，改变它的指向会导致编译期错误。至于它所指向的对象 的变化，final是不负责的。

#### 总结
栈中用来存放一些原始数据类型的局部变量数据和对象的引用(String,数组.对象等等)但不存放对象内容
堆中存放使用new关键字创建的对象.
字符串是一个特殊包装类,其引用是存放在栈里的,而对象内容必须根据创建方式不同定(常量池和堆).有的是编译期就已经创建好，存放在字符串常 量池中，而有的是运行时才被创建.使用new关键字，存放在堆中。

###  Java 中操作字符串都有哪些类？它们之间有什么区别？
操作字符串的类有：String、StringBuffer、StringBuilder。

String 和 StringBuffer、StringBuilder 的区别在于 
String 声明的是不可变的对象，每次操作都会生成新的 String 对象，然后将指针指向新的 String 对象
StringBuffer、StringBuilder 可以在原有对象的基础上进行操作

所以在经常改变字符串内容的情况下最好不要使用 String。

StringBuffer 和 StringBuilder 最大的区别在于，
StringBuffer 是线程安全的
StringBuilder 是非线程安全的
但 StringBuilder 的性能却高于 StringBuffer，所以在单线程环境下推荐使用 StringBuilder，多线程环境下推荐使用 StringBuffer。

### String s = new String(“xyz”);创建了几个字符串对象？
两个对象，一个是静态区的”xyz”，一个是用new创建在堆上的对象。


# java 关键字

## switch

### switch 是否能作用在byte 上，是否能作用在long,float 上，是否能作用在String上？
在Java 5以前，switch(expr)中，
expr只能是byte、short、char、int。
从Java 5开始，Java中引入了枚举类型，expr也可以是enum类型，
从Java 7开始，expr还可以是字符串(String)
但是长整型(long),浮点数(float)在目前所有的版本中都是不可以的。

# java 操作符

## &和&&的区别？
&运算符有两种用法
1. 按位与
2. 逻辑与
 
&&运算符是短路与运算。
逻辑与跟短路与的差别是非常巨大的，虽然二者都要求运算符左右两端的布尔值都是true整个表达式的值才是true。
&&之所以称为短路运算是因为，如果&&左边的表达式的值是false，右边的表达式会被直接短路掉，不会进行运算。
很多时候我们可能都需要用&&而不是&，例如在验证用户登录时判定用户名不是null而且不是空字符串，应当写为：username != null && !username.equals(“”)，二者的顺序不能交换，更不能用&运算符，因为第一个条件如果不成立，根本不能进行字符串的equals比较，否则会产生NullPointerException异常。
注意：逻辑或运算符（|）和短路或运算符（||）的差别也是如此。

# java 基本语法

### java当中的四种引用
强引用,软引用,弱引用,虚引用.不同的引用类型主要体现在GC上:

强引用：如果一个对象具有强引用，它就不会被垃圾回收器回收。即使当前内存空间不足，JVM也不会回收它，而是抛出 OutOfMemoryError 错误，使程序异常终止。如果想中断强引用和某个对象之间的关联，可以显式地将引用赋值为null，这样一来的话，JVM在合适的时间就会回收该对象

软引用：在使用软引用时，如果内存的空间足够，软引用就能继续被使用，而不会被垃圾回收器回收，只有在内存不足时，软引用才会被垃圾回收器回收。

弱引用：具有弱引用的对象拥有的生命周期更短暂。因为当 JVM 进行垃圾回收，一旦发现弱引用对象，无论当前内存空间是否充足，都会将弱引用回收。不过由于垃圾回收器是一个优先级较低的线程，所以并不一定能迅速发现弱引用对象

虚引用：顾名思义，就是形同虚设，如果一个对象仅持有虚引用，那么它相当于没有引用，在任何时候都可能被垃圾回收器回收。

更多了解参见深入对象引用： `http://blog.csdn.net/dd864140130/article/details/49885811`

### 内部类的作用
内部类可以有多个实例,每个实例都有自己的状态信息,并且与其他外围对象的信息相互独立.在单个外围类当中,可以让多个内部类以不同的方式实现同一接口,或者继承同一个类.创建内部类对象的时刻不依赖于外部类对象的创建.内部类并没有令人疑惑的”is-a”关系,它就像是一个独立的实体.

内部类提供了更好的封装,除了该外围类,其他类都不能访问

### 为什么要有不同的引用类型
不像C语言,我们可以控制内存的申请和释放,在Java中有时候我们需要适当的控制对象被回收的时机,因此就诞生了不同的引用类型,可以说不同的引用类型实则是对GC回收时机不可控的妥协.有以下几个使用场景可以充分的说明:

利用软引用和弱引用解决OOM问题：用一个HashMap来保存图片的路径和相应图片对象关联的软引用之间的映射关系，在内存不足时，JVM会自动回收这些缓存图片对象所占用的空间，从而有效地避免了OOM的问题.

通过软引用实现Java对象的高速缓存:比如我们创建了一Person的类，如果每次需要查询一个人的信息,哪怕是几秒中之前刚刚查询过的，都要重新构建一个实例，这将引起大量Person对象的消耗,并且由于这些对象的生命周期相对较短,会引起多次GC影响性能。此时,通过软引用和 HashMap 的结合可以构建高速缓存,提供性能.

### final 在 Java 中有什么作用？
- final 修饰的类叫最终类，该类不能被继承。
- final 修饰的方法不能被重写。
- final 修饰的变量叫常量，常量必须初始化，初始化之后值就不能被修改。

### 阐述静态变量和实例变量的区别。
静态变量是被static修饰符修饰的变量，也称为类变量，它属于类，不属于类的任何一个对象，一个类不管创建多少个对象，静态变量在内存中有且仅有一个拷贝；
实例变量必须依存于某一实例，需要先创建对象然后通过对象才能访问到它。
静态变量可以实现让多个对象共享内存。

补充：在Java开发中，上下文类和工具类中通常会有大量的静态成员。

### 是否可以从一个静态（static）方法内部发出对非静态（non-static）方法的调用？
不可以，静态方法只能访问静态成员，因为非静态方法的调用要先创建对象，在调用静态方法时可能对象并没有被初始化。

### Object中有哪些公共方法？
- equals()
- clone()
- getClass()
- notify(),notifyAll(),wait()
- toString()

### 两个对象值相同(x.equals(y) == true)，但却可有不同的hash code，这句话对不对？
不对，如果两个对象x和y满足x.equals(y) == true，它们的哈希码（hash code）应当相同。
J
ava对于eqauls方法和hashCode方法是这样规定的：
(1)如果两个对象相同（equals方法返回true），那么它们的hashCode值一定要相同；
(2)如果两个对象的hashCode相同，它们并不一定相同。
当然，你未必要按照要求去做，但是如果你违背了上述原则就会发现在使用容器时，相同的对象可以出现在Set集合中，同时增加新元素的效率会大大下降（对于使用哈希存储的系统，如果哈希码频繁的冲突将会造成存取性能急剧下降）。

补充：关于equals和hashCode方法，很多Java程序都知道，但很多人也就是仅仅知道而已，在Joshua Bloch的大作《Effective Java》（很多软件公司，《Effective Java》、《Java编程思想》以及《重构：改善既有代码质量》是Java程序员必看书籍，如果你还没看过，那就赶紧去亚马逊买一本吧）中是这样介绍equals方法的：首先equals方法必须满足自反性（x.equals(x)必须返回true）、对称性（x.equals(y)返回true时，y.equals(x)也必须返回true）、传递性（x.equals(y)和y.equals(z)都返回true时，x.equals(z)也必须返回true）和一致性（当x和y引用的对象信息没有被修改时，多次调用x.equals(y)应该得到同样的返回值），而且对于任何非null值的引用x，x.equals(null)必须返回false。

实现高质量的equals方法的诀窍包括：

1. 使用==操作符检查”参数是否为这个对象的引用”；
2. 使用instanceof操作符检查”参数是否为正确的类型”；
3. 对于类中的关键属性，检查参数传入对象的属性是否与之相匹配；
4. 编写完equals方法后，问自己它是否满足对称性、传递性、一致性；
5. 重写equals时总是要重写hashCode；
6. 不要将equals方法参数中的Object对象替换为其他的类型，在重写时不要忘掉@Override注解。

### java中==和eqauls()的区别,equals()和`hashcode的区别
==是运算符,用于比较两个变量是否相等
equals是Object类的方法,用于比较两个对象是否相等
默认Object类的equals方法是比较两个对象的地址,此时和==的结果一样
换句话说:基本类型比较用==,比较的是他们的值
默认下,对象用==比较时,比较的是内存地址,如果需要比较对象内容,需要重写equal方法

### a==b与a.equals(b)有什么区别
如果a 和b 都是对象，则 a==b 是比较两个对象的引用，只有当 a 和 b 指向的是堆中的同一个对象才会返回 true，而 a.equals(b) 是进行逻辑比较，所以通常需要重写该方法来提供逻辑一致性的比较。例如，String 类重写 equals() 方法，所以可以用于两个不同对象，但是包含的字母相同的比较。

### 接口是否可继承（extends）接口？抽象类是否可实现（implements）接口？抽象类是否可继承具体类（concrete class）？
接口可以继承接口，而且支持多重继承。抽象类可以实现(implements)接口，抽象类可继承具体类也可以继承抽象类。

### 什么要重写hashcode()和equals()以及他们之间的区别与关系？
可以参考：
- [为什么要重写hashCode()方法和equals()方法以及如何进行重写](https://blog.csdn.net/xlgen157387/article/details/63683882)
- [Java hashCode() 和 equals()的若干问题解答](https://www.cnblogs.com/skywang12345/p/3324958.html)
- [Java中equals()与hashCode()方法详解](http://bijian1013.iteye.com/blog/1972404)

### Java 中的final关键字有哪些用法？
1. 修饰类：表示该类不能被继承；
2. 修饰方法：表示方法不能被重写；
3. 修饰变量：表示变量只能一次赋值以后值不能被修改（常量）。


## 对象拷贝

### 为什么要使用克隆？
克隆的对象可能包含一些已经修改过的属性，而 new 出来的对象的属性都还是初始化时候的值，所以当需要一个新的对象来保存当前对象的“状态”就靠克隆方法了。

### 如何实现对象克隆？
实现 Cloneable 接口并重写 Object 类中的 clone() 方法。
实现 Serializable 接口，通过对象的序列化和反序列化实现克隆，可以实现真正的深度克隆。

### 深拷贝和浅拷贝区别是什么？
- 浅克隆：当对象被复制时只复制它本身和其中包含的值类型的成员变量，而引用类型的成员对象并没有复制。被复制对象的所有变量都含有与原来的对象相同的值，而所有的对其他对象的引用仍然指向原来的对象。换言之，浅拷贝仅仅复制所考虑的对象，而不复制它所引用的对象。
- 深克隆：除了对象本身被复制外，对象所包含的所有成员变量也将复制。被复制对象的所有变量都含有与原来的对象相同的值，而那些引用其他对象的变量将指向被复制过的新对象，而不再是原有的那些被引用的对象。换言之，深拷贝把要复制的对象所引用的对象都复制了一遍。

### 如何实现对象克隆？
有两种方式：
1. 实现Cloneable接口并重写Object类中的clone()方法；
2. 实现Serializable接口，通过对象的序列化和反序列化实现克隆，可以实现真正的深度克隆。

代码如下：

```java
import java.io.ByteArrayInputStream;
import java.io.ByteArrayOutputStream;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;

public class MyUtil {

    private MyUtil() {
        throw new AssertionError();
    }

    public static <T> T clone(T obj) throws Exception {
        ByteArrayOutputStream bout = new ByteArrayOutputStream();
        ObjectOutputStream oos = new ObjectOutputStream(bout);
        oos.writeObject(obj);

        ByteArrayInputStream bin = new ByteArrayInputStream(bout.toByteArray());
        ObjectInputStream ois = new ObjectInputStream(bin);
        return (T) ois.readObject();

        // 说明：调用ByteArrayInputStream或ByteArrayOutputStream对象的close方法没有任何意义
        // 这两个基于内存的流只要垃圾回收器清理对象就能够释放资源，这一点不同于对外部资源（如文件流）的释放
    }
}
```

下面是测试代码：

```java
import java.io.Serializable;

/**
 * 人类
 */
class Person implements Serializable {
    private static final long serialVersionUID = -9102017020286042305L;

    private String name;    // 姓名
    private int age;        // 年龄
    private Car car;        // 座驾

    public Person(String name, int age, Car car) {
        this.name = name;
        this.age = age;
        this.car = car;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public Car getCar() {
        return car;
    }

    public void setCar(Car car) {
        this.car = car;
    }

    @Override
    public String toString() {
        return "Person [name=" + name + ", age=" + age + ", car=" + car + "]";
    }

}
```

```java
/**
 * 小汽车类
 */
class Car implements Serializable {
    private static final long serialVersionUID = -5713945027627603702L;

    private String brand;       // 品牌
    private int maxSpeed;       // 最高时速

    public Car(String brand, int maxSpeed) {
        this.brand = brand;
        this.maxSpeed = maxSpeed;
    }

    public String getBrand() {
        return brand;
    }

    public void setBrand(String brand) {
        this.brand = brand;
    }

    public int getMaxSpeed() {
        return maxSpeed;
    }

    public void setMaxSpeed(int maxSpeed) {
        this.maxSpeed = maxSpeed;
    }

    @Override
    public String toString() {
        return "Car [brand=" + brand + ", maxSpeed=" + maxSpeed + "]";
    }

}
```

```java
class CloneTest {

    public static void main(String[] args) {
        try {
            Person p1 = new Person("Hao LUO", 33, new Car("Benz", 300));
            Person p2 = MyUtil.clone(p1);   // 深度克隆
            p2.getCar().setBrand("BYD");
            // 修改克隆的Person对象p2关联的汽车对象的品牌属性
            // 原来的Person对象p1关联的汽车不会受到任何影响
            // 因为在克隆Person对象时其关联的汽车对象也被克隆了
            System.out.println(p1);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

注意：基于序列化和反序列化实现的克隆不仅仅是深度克隆，更重要的是通过泛型限定，可以检查出要克隆的对象是否支持序列化，这项检查是编译器完成的，不是在运行时抛出异常，这种是方案明显优于使用Object类的clone方法克隆对象。让问题在编译的时候暴露出来总是优于把问题留到运行时。


# 反射

## 什么是反射？
反射是在运行状态中，对于任意一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意一个方法和属性；这种动态获取的信息以及动态调用对象的方法的功能称为 Java 语言的反射机制。

## 说说反射的用途及实现
反射机制是Java语言中一个非常重要的特性，它允许程序在运行时进行自我检查，同时也允许对其内部成员进行操作。

反射机制提供的功能主要有：得到一个对象所属的类；获取一个类的所有成员变量和方法；在运行时创建对象；在运行时调用对象的方法

## 什么是 Java 序列化？什么情况下需要序列化？
Java 序列化是为了保存各种对象在内存中的状态，并且可以把保存的对象状态再读出来。

以下情况需要使用 Java 序列化：

1. 想把的内存中的对象状态保存到一个文件中或者数据库中时候；
2. 想用套接字在网络上传送对象的时候；
3. 想通过RMI（远程方法调用）传输对象的时候。

## 动态代理是什么？有哪些应用？
动态代理是运行时动态生成代理类。

动态代理的应用有 spring aop、hibernate 数据查询、测试框架的后端 mock、rpc，Java注解对象获取等。

## 怎么实现动态代理？
JDK 原生动态代理和 cglib 动态代理。JDK 原生动态代理是基于接口实现的，而 cglib 是基于继承当前类的子类实现的。

## 通用DAO层

```java
import com.exler.bos.dao.base.BaseDao;
import com.exler.bos.utils.PageBean;
import org.hibernate.Query;
import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.criterion.DetachedCriteria;
import org.hibernate.criterion.Projections;
import org.springframework.orm.hibernate5.support.HibernateDaoSupport;

import javax.annotation.Resource;
import java.io.Serializable;
import java.lang.reflect.ParameterizedType;
import java.lang.reflect.Type;
import java.util.List;

/**
 * 持久层通用实现
 *
 * @param <T>
 */
public class BaseDaoImpl<T> extends HibernateDaoSupport implements BaseDao<T> {
    // 代表的是某个实体的类型
    private Class<T> entityClass;

    // 注入sessionFactory 根据类型注入spring工厂中的 对象
    @Resource
    public void setMySessionFactory(SessionFactory sessionFactory) {
        super.setSessionFactory(sessionFactory);
    }

    // 在父类的构造方法中动态获得entityClass
    public BaseDaoImpl() {
        ParameterizedType superclass = (ParameterizedType) this.getClass().getGenericSuperclass();
        // 获得父类上声明的泛型数组
        Type[] actualTypeArguments = superclass.getActualTypeArguments();
        entityClass = (Class<T>) actualTypeArguments[0];
    }

    @Override
    public void save(T entity) {
        this.getHibernateTemplate().save(entity);
    }

    @Override
    public void delete(T entity) {
        this.getHibernateTemplate().delete(entity);
    }

    @Override
    public void update(T entity) {
        this.getHibernateTemplate().update(entity);
    }

    @Override
    public void saveOrUpdate(T entity) {
        getHibernateTemplate().saveOrUpdate(entity);
    }

    @Override
    public T findById(Serializable id) {
        return this.getHibernateTemplate().get(entityClass, id);
    }

    @Override
    public List<T> findAll() {
        String hql = "from " + entityClass.getSimpleName();
        return (List<T>) this.getHibernateTemplate().find(hql);
    }

    @Override
    public void executeUpdate(String queryName, Object... objects) {
        Session session = this.getSessionFactory().getCurrentSession();
        Query query = session.getNamedQuery(queryName);
        // 为hql语句中的?赋值
        int i = 0;
        for (Object object :
                objects) {
            query.setParameter(i++, object);
        }
        // 执行更新
        query.executeUpdate();
    }

    /**
     * 通用分页查询方法
     *
     * @param pageBean
     */
    @Override
    public void pageQuery(PageBean pageBean) {
        int currentPage = pageBean.getCurrentPage();
        int pageSize = pageBean.getPageSize();
        DetachedCriteria dc = pageBean.getDc();
        // 查询total 总数据量
        dc.setProjection(Projections.rowCount());   // 指定hibernate框架发出sql的形式    select count(*) from bc_staff;
        List<Long> countList = (List<Long>) getHibernateTemplate().findByCriteria(dc);
        Long count = countList.get(0);
        pageBean.setTotal(count.intValue());
        // 查询rows 当前页需要展示的数据集合
        dc.setProjection(null);    // select * from bc_staff;
        int firstResult = (currentPage - 1) * pageSize;
        int maxResults = pageSize;
        List rows = getHibernateTemplate().findByCriteria(dc, firstResult, maxResults);
        pageBean.setRows(rows);
    }
}
```

# 异常

## throw 和 throws 的区别？
- throw：是真实抛出一个异常。
- throws：是声明可能会抛出一个异常。

throw用于主动抛出java.lang.Throwable 类的一个实例化对象，意思是说你可以通过关键字 throw 抛出一个 Error 或者 一个Exception，如：throw new IllegalArgumentException(“size must be multiple of 2″)。

throws 的作用是作为方法声明和签名的一部分，方法被抛出相应的异常以便调用者能处理。Java 中，任何未处理的受检查异常强制在 throws 子句中声明。

## Error和Exception有什么区别？
Error表示系统级的错误和程序不必处理的异常，是恢复不是不可能但很困难的情况下的一种严重问题；比如内存溢出，不可能指望程序能处理这样的情况；

Exception表示需要捕捉或者需要程序进行处理的异常，是一种设计或实现问题；也就是说，它表示如果程序运行正常，从不会发生的情况。

## Java语言如何进行异常处理，关键字：throws、throw、try、catch、finally分别如何使用？
Java通过面向对象的方法进行异常处理，把各种不同的异常进行分类，并提供了良好的接口。在Java中，每个异常都是一个对象，它是Throwable类或其子类的实例。当一个方法出现异常后便抛出一个异常对象，该对象中包含有异常信息，调用这个对象的方法可以捕获到这个异常并可以对其进行处理。

Java的异常处理是通过5个关键词来实现的：try、catch、throw、throws和finally。

一般情况下是用try来执行一段程序，如果系统会抛出（throw）一个异常对象，可以通过它的类型来捕获（catch）它，或通过总是执行代码块（finally）来处理；
try用来指定一块预防所有异常的程序；
catch子句紧跟在try块后面，用来指定你想要捕获的异常的类型；
throw语句用来明确地抛出一个异常；
throws用来声明一个方法可能抛出的各种异常（当然声明异常时允许无病呻吟）；
finally为确保一段代码不管发生什么异常状况都要被执行；

try语句可以嵌套，每当遇到一个try语句，异常的结构就会被放入异常栈中，直到所有的try语句都完成。如果下一级的try语句没有对某种异常进行处理，异常栈就会执行出栈操作，直到遇到有处理这种异常的try语句或者最终将异常抛给JVM。

## 运行时异常与受检异常有何异同？
异常表示程序运行过程中可能出现的非正常状态，运行时异常表示虚拟机的通常操作中可能遇到的异常，是一种常见运行错误，只要程序设计得没有问题通常就不会发生。受检异常跟程序运行的上下文环境有关，即使程序设计无误，仍然可能因使用的问题而引发。Java编译器要求方法必须声明抛出可能发生的受检异常，但是并不要求必须声明抛出未被捕获的运行时异常。

异常和继承一样，是面向对象程序设计中经常被滥用的东西，在Effective Java中对异常的使用给出了以下指导原则：

- 不要将异常处理用于正常的控制流（设计良好的API不应该强迫它的调用者为了正常的控制流而使用异常）
- 对可以恢复的情况使用受检异常，对编程错误使用运行时异常
- 避免不必要的使用受检异常（可以通过一些状态检测手段来避免异常的发生）
- 优先使用标准的异常
- 每个方法抛出的异常都要有文档
- 保持异常的原子性
- 不要在catch中忽略掉捕获到的异常

## final、finally、finalize 有什么区别？
- final：是修饰符，如果修饰类，此类不能被继承；如果修饰方法和变量，则表示此方法和此变量不能在被改变，只能使用。
- finally：是 try{} catch{} finally{} 最后一部分，表示不论发生任何情况都会执行，finally 部分可以省略，但如果 finally 部分存在，则一定会执行 finally 里面的代码。

- finalize： 是 Object 类的一个方法，在垃圾收集器执行的时候会调用被回收对象的此方法。
 final：修饰符（关键字）有三种用法：如果一个类被声明为final，意味着它不能再派生出新的子类，即不能被继承，因此它和abstract是反义词。将变量声明为final，可以保证它们在使用中不被改变，被声明为final的变量必须在声明时给定初值，而在以后的引用中只能读取不可修改。被声明为final的方法也同样只能使用，不能在子类中被重写。
- finally：通常放在try…catch…的后面构造总是执行代码块，这就意味着程序无论正常执行还是发生异常，这里的代码只要JVM不关闭都能执行，可以将释放外部资源的代码写在finally块中。
- finalize：Object类中定义的方法，Java中允许使用finalize()方法在垃圾收集器将对象从内存中清除出去之前做必要的清理工作。这个方法是由垃圾收集器在销毁对象时调用的，通过重写finalize()方法可以整理系统资源或者执行其他清理工作。


## try-catch-finally 中哪个部分可以省略？
try-catch-finally 其中 catch 和 finally 都可以被省略，但是不能同时省略，也就是说有 try 的时候，必须后面跟一个 catch 或者 finally。

## try-catch-finally 中，如果 catch 中 return 了，finally 还会执行吗？
finally 一定会执行，即使是 catch 中 return 了，catch 中的 return 会等 finally 中的代码执行完之后，才会执行。

## 常见的异常类有哪些？
ArithmeticException算术异常
ClassNotFoundException 指定类不存在
ClassCastException 数据类型转换异常
FileNotFoundException 文件未找到异常
IllegalArgumentException 非法参数异常
IndexOutOfBoundsException 数组下标越界异常
IOException IO 异常
NoSuchMethodException 方法不存在异常
NullPointerException 空指针异常
NumberFormatException 字符串转换为数字异常
SecurityException 安全异常
SocketException Socket 异常

# List

## 数组转集合

```java
String[] array = {"1", "2", "3"};
List<String> list = Arrays.asList(array);
list.add("4");
System.out.println(list.size());
System.out.println(list.get(2));
```
此段代码运行报错

```java
    @SafeVarargs
    @SuppressWarnings("varargs")
    public static <T> List<T> asList(T... a) {
        return new ArrayList<>(a);
    }

    /**
     * @serial include
     */
    private static class ArrayList<E> extends AbstractList<E>
        implements RandomAccess, java.io.Serializable
    {
        private static final long serialVersionUID = -2764017481108945198L;
        private final E[] a;

        ArrayList(E[] array) {
            a = Objects.requireNonNull(array);
        }
```

数组转集合,只能获取集合长度,不能对集合进行操作,
转换后的List是工具包下自定义的ArrayList,并不是集合的ArrayList类,没有提供add,remove等操作
所以会报：java.lang.UnsupportedOperationException

## ArrayList

### Array 和 ArrayList 有何区别？
Array 可以存储基本数据类型和对象，ArrayList 只能存储对象。
Array 是指定固定大小的，而 ArrayList 大小是自动扩展的。
Array 内置方法没有 ArrayList 多，比如 addAll、removeAll、iteration 等方法只有 ArrayList 有。


# 注解

## 说说自定义注解的场景及实现
登录、权限拦截、日志处理，以及各种 Java 框架，如 Spring，Hibernate，JUnit 提到注解就不能不说反射，Java 自定义注解是通过运行时靠反射获取注解。

实际开发中，例如我们要获取某个方法的调用日志，可以通过 AOP（动态代理机制）给方法添加切面，通过反射来获取方法包含的注解，如果包含日志注解，就进行日志记录。
反射的实现在 Java 应用层面上讲，是通过对 Class 对象的操作实现的，Class 对象为我们提供了一系列方法对类进行操作。在 JVM 这个角度来说，Class 文件是一组以 8 位字节为基础单位的二进制流，各个数据项目按严格的顺序紧凑的排列在 Class 文件中，里面包含了类、方法、字段等等相关数据。

通过对 Class 数据流的处理我们即可得到字段、方法等数据。

# Java8

## Stream

### Stream有以下特性及优点：
1. **无存储**。Stream不是一种数据结构，它只是某种数据源的一个视图，数据源可以是一个数组，Java容器或I/O channel等。
2. **为函数式编程而生**。对Stream的任何修改都不会修改背后的数据源，比如对Stream执行过滤操作并不会删除被过滤的元素，而是会产生一个不包含被过滤元素的新Stream。
3. **惰式执行**。Stream上的操作并不会立即执行，只有等到用户真正需要结果的时候才会执行。
4. **可消费性**。Stream只能被“消费”一次，一旦遍历过就会失效，就像容器的迭代器那样，想要再次遍历必须重新生成。

### Stream 创建
1. 通过已有的集合来创建流
```java
List<String> strings = Arrays.asList("a","b")
strings.stream(strings)
```

2. 通过Stream创建流
```java
Stream.of("a", "b")
```

### Stream 中间操作
#### filter
filter 方法用于通过设置的条件过滤出元素。以下代码片段使用 filter 方法过滤掉空字符串：
```java
List<String> strings = Arrays.asList("Hollis", "", "HollisChuang", "H", "hollis");
strings.stream().filter(string -> !string.isEmpty()).forEach(System.out::println);
//Hollis, , HollisChuang, H, hollis
```

#### map
map 方法用于映射每个元素到对应的结果，以下代码片段使用 map 输出了元素对应的平方数：
```java
List<Integer> numbers = Arrays.asList(3, 2, 2, 3, 7, 3, 5);
numbers.stream().map( i -> i*i).forEach(System.out::println);
//9,4,4,9,49,9,25
```

#### limit/skip
limit 返回 Stream 的前面 n 个元素；skip 则是扔掉前 n 个元素。以下代码片段使用 limit 方法保理4个元素：
```java
List<Integer> numbers = Arrays.asList(3, 2, 2, 3, 7, 3, 5);
numbers.stream().limit(4).forEach(System.out::println);
//3,2,2,3
```

#### sorted
sorted 方法用于对流进行排序。以下代码片段使用 sorted 方法进行排序：
```java
List<Integer> numbers = Arrays.asList(3, 2, 2, 3, 7, 3, 5);
numbers.stream().sorted().forEach(System.out::println);
//2,2,3,3,3,5,7
```

#### distinct
distinct主要用来去重，以下代码片段使用 distinct 对元素进行去重：
```java
List<Integer> numbers = Arrays.asList(3, 2, 2, 3, 7, 3, 5);
numbers.stream().distinct().forEach(System.out::println);
//3,2,7,5
```
接下来我们通过一个例子和一张图，来演示下，当一个Stream先后通过filter、map、sort、limit以及distinct处理后会发生什么。

```java
List<String> strings = Arrays.asList("Hollis", "HollisChuang", "hollis", "Hello", "HelloWorld", "Hollis");
Stream s = strings.stream().filter(string -> string.length()<= 6).map(String::length).sorted().limit(3)
            .distinct();
```
![Exler](../../picture/java/Stream/1.webp)
![Exler](../../picture/java/Stream/2.webp)

### Stream 最终操作 
terminal operation

最终操作会消耗流，产生一个最终结果。也就是说，在最终操作之后，不能再次使用流，也不能在使用任何中间操作，否则将抛出异常：
```
java.lang.IllegalStateException: stream has already been operated upon or closed
```
俗话说，“你永远不会两次踏入同一条河”也正是这个意思。

常用的最终操作如下图：￼
![Exler](../../picture/java/Stream/3.webp)

#### forEach
Stream 提供了方法 'forEach' 来迭代流中的每个数据。以下代码片段使用 forEach 输出了10个随机数：
```java
Random random = new Random();
random.ints().limit(10).forEach(System.out::println);
```

#### count
count用来统计流中的元素个数。
```java
List<String> strings = Arrays.asList("Hollis", "HollisChuang", "hollis","Hollis666", "Hello", "HelloWorld", "Hollis");
System.out.println(strings.stream().count());
//7
```

#### collect
collect就是一个归约操作，可以接受各种做法作为参数，将流中的元素累积成一个汇总结果：
```java
List<String> strings = Arrays.asList("Hollis", "HollisChuang", "hollis","Hollis666", "Hello", "HelloWorld", "Hollis");
strings  = strings.stream().filter(string -> string.startsWith("Hollis")).collect(Collectors.toList());
System.out.println(strings);
//Hollis, HollisChuang, Hollis666, Hollis
```

使用一张图，来演示下，前文的例子中，当一个Stream先后通过filter、map、sort、limit以及distinct处理后会，在分别使用不同的最终操作可以得到怎样的结果。

下图，展示了文中介绍的所有操作的位置、输入、输出以及使用一个案例展示了其结果。
![Exler](../../picture/java/Stream/4.webp)

# 锁

## 简单说说你所了解的 Java 锁分类和特点有哪些？
其实对于 Java 锁的分类没有严格意义的规则，我们常说的分类一般都是依据锁的特性、锁的设计、锁的状态等进行归纳整理的，所以常见的分类如下：
- 公平锁、非公平锁：公平锁指多个线程按照申请锁的顺序来获取锁，非公平锁就是没有顺序完全随机，所以能会造成优先级反转或者饥饿现象；synchronized 就是非公平锁，ReentrantLock（使用 CAS 和 AQS 实现） 通过构造参数可以决定是非公平锁还是公平锁，默认构造是非公平锁；非公平锁的吞吐量性能比公平锁大好。
- 可重入锁：又名递归锁，指在同一个线程在外层方法获取锁的时候在进入内层方法会自动获取锁，synchronized 和 ReentrantLock 都是可重入锁，可重入锁可以在一定程度避免死锁。
- 独享锁、共享锁：独享锁是指该锁一次只能被一个线程持有，共享锁指该锁可以被多个线程持有；synchronized 和 ReentrantLock 都是独享锁，ReadWriteLock 的读锁是共享锁，写锁是独占锁；ReentrantLock 的独享锁和共享锁也是通过 AQS 来实现的。
- 互斥锁、读写锁：其实就是独享锁、共享锁的具体说法；互斥锁实质就是 ReentrantLock，读写锁实质就是 ReadWriteLock。
- 乐观锁、悲观锁：这个分类不是具体锁的分类，而是看待并发同步的角度；悲观锁认为对于同一个数据的并发操作一定是会发生修改的（哪怕实质没修改也认为会修改），因此对于同一个数据的并发操作悲观锁采取加锁的形式，因为悲观锁认为不加锁的操作一定有问题；乐观锁则认为对于同一个数据的并发操作是不会发生修改的，在更新数据的时候会采用不断的尝试更新，乐观锁认为不加锁的并发操作是没事的；由此可以看出悲观锁适合写操作非常多的场景，乐观锁适合读操作非常多的场景，不加锁会带来大量的性能提升，悲观锁在 java 中很常见，乐观锁其实就是基于 CAS 的无锁编程，譬如 java 的原子类就是通过 CAS 自旋实现的。
- 分段锁：实质是一种锁的设计策略，不是具体的锁，对于 ConcurrentHashMap 而言其并发的实现就是通过分段锁的形式来实现高效并发操作；当要 put 元素时并不是对整个 hashmap 加锁，而是先通过 hashcode 知道它要放在哪个分段，然后对分段进行加锁，所以多线程 put 元素时只要放在的不是同一个分段就做到了真正的并行插入，但是统计 size 时就需要获取所有的分段锁才能统计；分段锁的设计是为了细化锁的粒度。
- 偏向锁、轻量级锁、重量级锁：这种分类是按照锁状态来归纳的，并且是针对 synchronized 的，java 1.6 为了减少获取锁和释放锁带来的性能问题而引入的一种状态，其状态会随着竞争情况逐渐升级，锁可以升级但不能降级，意味着偏向锁升级成轻量级锁后无法降为偏向锁，这种升级无法降级的策略目的就是为了提高获得锁和释放锁的效率。
- 自旋锁：其实是相对于互斥锁的概念，互斥锁线程会进入 WAITING 状态和 RUNNABLE 状态的切换，涉及上下文切换、cpu 抢占等开销，自旋锁的线程一直是 RUNNABLE 状态的，一直在那循环检测锁标志位，机制不重复，但是自旋锁加锁全程消耗 cpu，起始开销虽然低于互斥锁，但随着持锁时间加锁开销是线性增长。
- 可中断锁：synchronized 是不可中断的，Lock 是可中断的，这里的可中断建立在阻塞等待中断，运行中是无法中断的。
