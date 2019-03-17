[TOC]

# String

## String常量池

原文地址http://blog.csdn.net/gaopeng0071/article/details/11741027

关于string内存分配不错的博客：http://blog.csdn.net/rj042/article/details/6871030
### 示例1：
```java
String s0="kvill";
String s1="kvill";
String s2="kv" + "ill";
System.out.println( s0==s1 );
System.out.println( s0==s2 );
```
分析：
首先，我们要知结果为道Java 会确保一个字符串常量只有一个拷贝。
因为例子中的 s0和s1中的”kvill”都是字符串常量，它们在编译期就被确定了，所以s0==s1为true；而”kv”和”ill”也都是字符串常量，当一个字 符串由多个字符串常量连接而成时，它自己肯定也是字符串常量，所以s2也同样在编译期就被解析为一个字符串常量，所以s2也是常量池中” kvill”的一个引用。所以我们得出s0==s1==s2；

### 示例2：
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

### 示例3：
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

### 示例4：
```java
String a = "ab";
String bb = "b";
String b = "a" + bb;
System.out.println((a == b)); //result = false
```
分析：
JVM对于字符串引用，由于在字符串的"+"连接中，有字符串引用存在，而引用的值在程序编译期是无法确定的，即"a" + bb无法被编译器优化，只有在程序运行期来动态分配并将连接后的新地址赋给b。所以上面程序的结果也就为false。

### 示例5：
```java
String a = "ab";
final String bb = "b";
String b = "a" + bb;
System.out.println((a == b)); //result = true
```
分析：
和[4]中唯一不同的是bb字符串加了final修饰，对于final修饰的变量，它在编译时被解析为常量值的一个本地拷贝存储到自己的常量 池中或嵌入到它的字节码流中。所以此时的"a" + bb和"a" + "b"效果是一样的。故上面程序的结果为true。

### 示例6：
```java
String a = "ab";
final String bb = getBB();
String b = "a" + bb;
System.out.println((a == b)); //result = false
private static String getBB() {  return "b";   }
```
分析：
JVM对于字符串引用bb，它的值在编译期无法确定，只有在程序运行期调用方法后，将方法的返回值和"a"来动态连接并分配地址为b，故上面 程序的结果为false。

### 关于String是不可变的
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

### String中的final用法和理解

```java
final StringBuffer a = new StringBuffer("111");
final StringBuffer b = new StringBuffer("222");
a=b;//此句编译不通过
final StringBuffer a = new StringBuffer("111");
a.append("222");// 编译通过
```
可见，final只对引用的"值"(即内存地址)有效，它迫使引用只能指向初始指向的那个对象，改变它的指向会导致编译期错误。至于它所指向的对象 的变化，final是不负责的。

### 总结
栈中用来存放一些原始数据类型的局部变量数据和对象的引用(String,数组.对象等等)但不存放对象内容
堆中存放使用new关键字创建的对象.
字符串是一个特殊包装类,其引用是存放在栈里的,而对象内容必须根据创建方式不同定(常量池和堆).有的是编译期就已经创建好，存放在字符串常 量池中，而有的是运行时才被创建.使用new关键字，存放在堆中。