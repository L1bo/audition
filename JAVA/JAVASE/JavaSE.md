[TOC]

# short

## hort
```java
short s1 = 1;
s1 = s1 + 1;  // 1

short s2 = 1;
s2 += 1;  // 2
```
对于注释 1 来说，在 s1+1 运算时会自动提升表达式的类型为 int，所以将 int 赋予给 short 类型的变量 s1 会出现类型转换错误（无法编译，IDE 提示错误），除非主动加上强转。对于注释 2 来说 += 是 java 语法规定的运算符，所以 java 编译器会对它进行转换特殊处理，故可以正确编译执行。

# int

## java 的 Integer 和 int 有什么区别？
int 是 java 内置基本数据类型之一，java 为每个基本类型都提供了一个封装类，Integer 就是 int 的封装类（也叫包装类型）;
int 变量的默认值为 0，Integer 变量的默认值为 null，所以 Integer 可以区分出未赋值和值为 0 的区别;
Integer 类内部提供了一些关于整数操作的方法，例如上文用到的表示整数的最大值和最小值。

# float

## java 中 3*0.1 == 0.3 将会返回什么？true 还是 false？
false，因为浮点数不能完全精确的表示出来，一般都会损失精度。

# char

## java 中 char 类型变量能不能储存一个中文的汉字，为什么？
java 的 char 类型变量是用来储存 Unicode 编码字符的，Unicode 字符集包含了汉字，所以 char 类型自然就能存储汉字，但是在某些特殊情况下某个生僻汉字可能没有包含在 Unicode 编码字符集中，这种情况下 char 类型就不能存储该生僻汉字了。

# String

## String常量池

原文地址http://blog.csdn.net/gaopeng0071/article/details/11741027

关于string内存分配不错的博客：http://blog.csdn.net/rj042/article/details/6871030
### 示例1：
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

# 反射

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

# List

## ArrayList






{"uid":8966693,"memberCode":"SXSD","name":"杨军","mobile":"15950631312","idcard":"321322198307282054",rloeGroup:"","data" : {"mx_carrier":"",...}}