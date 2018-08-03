* [一、数据类型](#1)
  - [基本数据类型及其包装类型](#1.1)
  - [引用类型](#1.2)
  - [值传递和引用传递](#1.3)
* [二、String](#2)
  - [String类](#2.1)
  - [字符串常量池](#2.2)
  - [intern()](#2.3)
  - [==和equals](#2.4)
  - [String、StringBuilfer、StringBuffer](#2.5)
* [三、Object](#3)
  - [Object类的方法](#3.1)
* [四、继承](#4)
  - [访问权限](#4.1)
<h1 id="1">一、数据类型</h1>
<h2 id="1.1">基本数据类型及其包装类型</h2>
<table>
<tr>
<td>基本类型</td><td>大小</td><td>包装类型</td>
</tr>
<tr>
<td>boolean</td><td>1bit</td><td>Boolean</td>
</tr>
<tr>
<td>byte</td><td>8bit</td><td>Byte</td>
</tr>
<tr>
<td>char</td><td>16bit</td><td>Character</td>
</tr>
<tr>
<td>short</td><td>16bit</td><td>Short</td>
</tr>
<tr>
<td>int</td><td>32bit</td><td>Integer</td>
</tr>
<tr>
<td>long</td><td>64bit</td><td>Long</td>
</tr>
<tr>
<td>float</td><td>32bit</td><td>Float</td>
</tr>
<tr>
<td>double</td><td>64bit</td><td>Double</td>
</tr>
</table>
Java中的基本类型不是面向对象的，它们只是纯粹的数据，除了数值本身的信息之外，基本类型数据不带有其他信息或者可操作方法。这在实际使用中存在很多不足，为了解决这个不足，对每个基本类型都对应了一个引用的类型，称为装箱基本类型。

### 装箱、拆箱
装箱：根据数据创建相应的包装对象。

    Integer i = new Integer(9);
    Integer j = 9;   //jdk1.5之后通过这种方式自动装箱
拆箱：将包装类型转换成基本数据类型。
    
    int k = i.intValue();
    int l = j;   //自动拆箱
### 缓存池
jdk1.5增加了一个新的方法：
    
    public static Integer valueOf(int i) {
        if (i >= IntegerCache.low && i <= IntegerCache.high)
            return IntegerCache.cache[i + (-IntegerCache.low)];
        return new Integer(i);
    }
在自动装箱过程时，编译器调用的是static Integer valueOf(int i)这个方法。于是  Integer a=3;   ==>    Integer a=Integer.valueOf(3);

多个 Integer 实例使用自动装箱来创建并且值相同，那么就会引用相同的对象。

    Integer i = 9;
    Integer j = 9;
    System.out.println(i == j);   //true

在JAVA 8 中，Integer的缓存池大小默认是-128~127。

    private static class IntegerCache {
        static final int low = -128;  //最小值是固定的
        static final int high;
        static final Integer cache[];   //缓存，存放Integer类型的数组

        static {
            // 最大值可以配置
            int h = 127;
            String integerCacheHighPropValue =
                sun.misc.VM.getSavedProperty("java.lang.Integer.IntegerCache.high");
            if (integerCacheHighPropValue != null) {
                try {
                    int i = parseInt(integerCacheHighPropValue);
                    i = Math.max(i, 127);
                    // Maximum array size is Integer.MAX_VALUE
                    h = Math.min(i, Integer.MAX_VALUE - (-low) -1);
                } catch( NumberFormatException nfe) {
                    // If the property cannot be parsed into an int, ignore it.
                }
            }
            high = h;

            cache = new Integer[(high - low) + 1];   //初始化数组
            int j = low;
            for(int k = 0; k < cache.length; k++)
                cache[k] = new Integer(j++);   //将low-high包装成Integer对象放入缓存中（默认是-128~127，256个对象）

            // range [-128, 127] must be interned (JLS7 5.1.7)
            assert IntegerCache.high >= 127;
        }

        private IntegerCache() {}
    }
其他基本数据类型的缓存池：

* Boolean：TRUE FALSE
* Byte： 所有值都被缓存
* Short： -128~127
* Long： -128~127
* Character： 前128个字符
<h2 id="1.2">引用类型</h2>
引用类型是指除基本类型之外的所有类型（类类型，接口类型，数组）。所有的类型在内存中都会分配一定的存储空间(形参在使用的时候也会分配存储空间,方法调用完成之后,这块存储空间自动消失), 基本的变量类型只有一块存储空间(分配在stack中), 而引用类型有两块存储空间(一块在stack中,一块在heap中)。

![图片](https://github.com/UMRhamster/Jobs2018/raw/master/java/img/reference_type.png)

引用就像一个对象的名字或者别名。引用也是一种数据类型，需要一定的内存空间（satck，栈）来保存。
    
    Student student = new Student();

创建对象时会在内存中会请求一块空间来保存数据，根据对象的大小，它可能需要占用的空间大小也不等。访问对象时，我们是通过引用去访问的。上述代码中的student就是引用，实际对象由new Student()产生，student指向该对象。其中引用student保存在栈（stack）中，实际对象保存在堆（heap）中。
<h2 id="1.3">值传递与引用传递</h2>

### 值传递
是指在调用函数时将实际参数复制一份传递到函数中，这样在函数中如果对参数进行修改，将不会影响到实际参数。
### 引用传递
是指在调用函数时将实际参数的地址直接传递到函数中，那么在函数中对参数所进行的修改，将影响到实际参数。

java中的参数传递只有"按值传递"。如何去理解呢？

    public static void change(int j){
        j = 10;
        System.out.println("change：j="+j);
    }
    public static void main(String[] args) {
        int i = 5;
        change(i);
        System.out.println("main：i="+i);
    }
上述代码中通过change(int j)方法修改参数的值，输出结果如下：

    change：j=10
    main：i=5
可以发现change方法内的修改并没有改变实际参数的值。

    static class Student{
        String name;

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }
    }
    public static void change(Student s){
        s.setName("李四");
        System.out.println("change：s.name="+s.getName());
    }
    public static void main(String[] args) {
        Student student = new Student();
        student.setName("张三");
        change(student);
        System.out.println("main：student.name="+student.getName());
    }
同样是通过change方法修改参数值，输出结果如下：

    change：s.name=李四
    main：student.name=李四
可以发现studen对象中的name属性的值发生了变化。

再来看一个例子：

    public static void change(String s){
        s = "hello java";
        System.out.println("change：s="+s);
    }
    public static void main(String[] args) {
        String str = "hello word";
        change(str);
        System.out.println("main：str="+str);
    }
change方法使用String类型为参数，其结果如下：
    
    change：s=hello java
    main：str=hello word
发现同样是对象，但是str的值并没有改变。

![图片](https://github.com/UMRhamster/Jobs2018/raw/master/java/img/reference_pass.png)

过程如上图，一开始str引用指向堆中的"hello word"，当执行change(String s)方法的时候,先把实际参数的地址复制给了s,然后s又指向了"hello java"，最终结果就是str指向"hello word"，s指向"hello java"，实际参数str并没有发生任何变化。

其实，java中参数为引用类型时，是把实际参数的引用的地址复制了一份，传递给了形式参数。所以，上面的参数其实是值传递，把实参对象引用的地址当做值传递给了形式参数。如果还是不懂，可以回到[引用类型](#1.2)查看形参为引用类型时的内存情况。

值传递和引用传递的区别并不是传递的内容。而是实参到底有没有被复制一份给形参。

所以说，Java中其实还是值传递的，只不过对于对象参数，值的内容是对象的引用。

<h1 id="2">二、String</h1>
<h2 id="2.1">String类</h2>

    public final class String
        implements java.io.Serializable, Comparable<String>, CharSequence {
        /** The value is used for character storage. */
        private final char value[];

从上面可以看出String类是final类，因此String不可以被继承。
String内部使用char数组存储数据，并且该数组也被final修饰，这意味着 value 数组初始化之后就不能再引用其它数组。并且 String 内部没有改变 value 数组的方法，因此可以保证 String 不可变。

    public String substring(int beginIndex, int endIndex) {
        if (beginIndex < 0) {
            throw new StringIndexOutOfBoundsException(beginIndex);
        }
        if (endIndex > value.length) {
            throw new StringIndexOutOfBoundsException(endIndex);
        }
        int subLen = endIndex - beginIndex;
        if (subLen < 0) {
            throw new StringIndexOutOfBoundsException(subLen);
        }
        return ((beginIndex == 0) && (endIndex == value.length)) ? this
                : new String(value, beginIndex, subLen);
    }

    public String concat(String str) {
        int otherLen = str.length();
        if (otherLen == 0) {
            return this;
        }
        int len = value.length;
        char buf[] = Arrays.copyOf(value, len + otherLen);
        str.getChars(buf, len);
        return new String(buf, true);
    }

    public String replace(char oldChar, char newChar) {
        if (oldChar != newChar) {
            int len = value.length;
            int i = -1;
            char[] val = value; /* avoid getfield opcode */

            while (++i < len) {
                if (val[i] == oldChar) {
                    break;
                }
            }
            if (i < len) {
                char buf[] = new char[len];
                for (int j = 0; j < i; j++) {
                    buf[j] = val[j];
                }
                while (i < len) {
                    char c = val[i];
                    buf[i] = (c == oldChar) ? newChar : c;
                    i++;
                }
                return new String(buf, true);
            }
        }
        return this;
    }
从上面的三个方法可以看出，无论是sub操、concat还是replace操作都不是在原有的字符串上进行的，而是重新生成了一个新的字符串对象。也就是说进行这些操作后，最原始的字符串并没有被改变。

String对象创建之后就是固定不变的了，对String对象的任何改变不影响原对象，都会生成新对象。
<h2 id="2.2">字符串常量池</h2>

    String a = "hello";
    String b = new ("hello");
以上创建String对象的两种方式，

第一种方式，存储在方法区的字符串常量池中。

第二种方式，如果常量池中没有该字符串，先在方法区字符串常量池中创建对象，再将其拷贝至堆中。

![图片](https://github.com/UMRhamster/Jobs2018/raw/master/java/img/string_create.png)

在 Java 7 之前，字符串常量池被放在运行时常量池中，它属于永久代。而在 Java 7，字符串常量池被放在堆中。这是因为永久代的空间有限，在大量使用字符串的场景下会导致 OutOfMemoryError 错误。

一道面试题：
>String str = new String("abc"); 创建了几个对象?

如果字符串常量池中没有"abc"，则创建两个对象，否则，创建一个对象。

<h2 id="2.3">intern()</h2>

当调用 intern() 方法时，编译器会将字符串添加到常量池中（stringTable维护），并返回指向该常量的引用。 
    
    String str2 = new String("str")+new String("01");
    str2.intern();
    String str1 = "str01";
    System.out.println(str2==str1);
在JDK 1.7下，当执行str2.intern();时，因为常量池中没有“str01”这个字符串，所以会在常量池中生成一个对堆中的“str01”的引用(注意这里是引用 ，就是这个区别于JDK 1.6的地方。在JDK1.6下是生成原字符串的拷贝)，而在进行String str1 = “str01”;字面量赋值的时候，常量池中已经存在一个引用，所以直接返回了该引用，因此str1和str2都指向堆中的同一个字符串，返回true。

    String str2 = new String("str")+new String("01");
    String str1 = "str01";
    str2.intern();
    System.out.println(str2==str1);
将中间两行调换位置以后，因为在进行字面量赋值（String str1 = “str01″）的时候，常量池中不存在，所以str1指向的常量池中的位置，而str2指向的是堆中的对象，再进行intern方法时，对str1和str2已经没有影响了，所以返回false。

[几张图轻松理解String.intern()](https://blog.csdn.net/soonfly/article/details/70147205)

<h2 id="2.4">==和equals</h2>

1. 对于==，如果作用于基本数据类型的变量（byte,short,char,int,long,float,double,boolean ），则直接比较其存储的"值"是否相等；如果作用于引用类型的变量（String），则比较的是所指向的对象的地址（即是否指向同一个对象）。

2. equals方法是基类Object中的方法，因此对于所有的继承于Object的类都会有该方法。在Object类中，equals方法是用来比较两个对象的引用是否相等，即是否指向同一个对象。

3. 对于equals方法，注意：equals方法不能作用于基本数据类型的变量。如果没有对equals方法进行重写，则比较的是引用类型的变量所指向的对象的地址；而String类对equals方法进行了重写，用来比较指向的字符串对象所存储的字符串是否相等。其他的一些类诸如Double，Date，Integer等，都对equals方法进行了重写用来比较指向的对象所存储的内容是否相等。

<h2 id="2.5">String、StringBuilder、StringBuffer</h2>

* String 是 Java 语言非常基础和重要的类，提供了构造和管理字符串的各种基本逻辑。它是典型的 Immutable 类，被声明成为 final class，所有属性也都是 final 的。也由于它的不可变性，类似拼接、裁剪字符串等动作，都会产生新的 String 对象。由于字符串操作的普遍性，所以相关操作的效率往往对应用性能有明显影响。

* StringBuffer 是为解决上面提到拼接产生太多中间对象的问题而提供的一个类，我们可以用 append 或者 add 方法，把字符串添加到已有序列的末尾或者指定位置。StringBuffer 本质是一个线程安全的可修改字符序列，它保证了线程安全，也随之带来了额外的性能开销，所以除非有线程安全的需要，不然还是推荐使用它的后继者，也就是 StringBuilder。

* StringBuilder 是 Java 1.5 中新增的，在能力上和 StringBuffer 没有本质区别，但是它去掉了线程安全的部分，有效减小了开销，是绝大部分情况下进行字符串拼接的首选。

<h1 id="3">三、Object</h1>
<h2 id="3.1">Object类的方法</h2>

Object是所有类的父类，任何类都默认继承Object。

    public class Object {
        private static native void registerNatives();
        static {
            registerNatives();
        }

    
        public final native Class<?> getClass();

    
        public native int hashCode();

    
        public boolean equals(Object obj) {
            return (this == obj);
        }

    
        protected native Object clone() throws CloneNotSupportedException;

    
        public String toString() {
            return getClass().getName() + "@" + Integer.toHexString(hashCode());
        }

    
        public final native void notify();

    
        public final native void notifyAll();

    
        public final native void wait(long timeout) throws InterruptedException;

    
        public final void wait(long timeout, int nanos) throws InterruptedException {
            if (timeout < 0) {
                throw new IllegalArgumentException("timeout value is negative");
            }

            if (nanos < 0 || nanos > 999999) {
                throw new IllegalArgumentException(
                                "nanosecond timeout value out of range");
            }

            if (nanos > 0) {
                timeout++;
            }

            wait(timeout);
        }

    
        public final void wait() throws InterruptedException {
            wait(0);
        }

    
        protected void finalize() throws Throwable { }
}


1．clone方法
保护方法，实现对象的浅复制，只有实现了Cloneable接口才可以调用该方法，否则抛出CloneNotSupportedException异常。

2．getClass方法
final方法，获得运行时类型。

3．toString方法
该方法用得比较多，一般子类都有覆盖。

4．finalize方法
该方法用于释放资源。因为无法确定该方法什么时候被调用，很少使用。

5．equals方法
该方法是非常重要的一个方法。一般equals和==是不一样的，但是在Object中两者是一样的。子类一般都要重写这个方法。

6．hashCode方法
该方法用于哈希查找，重写了equals方法一般都要重写hashCode方法。这个方法在一些具有哈希功能的Collection中用到。

一般必须满足obj1.equals(obj2)==true。可以推出obj1.hash-Code()==obj2.hashCode()，但是hashCode相等不一定就满足equals。不过为了提高效率，应该尽量使上面两个条件接近等价。

7．wait方法
wait方法就是使当前线程等待该对象的锁，当前线程必须是该对象的拥有者，也就是具有该对象的锁。wait()方法一直等待，直到获得锁或者被中断。wait(longtimeout)设定一个超时间隔，如果在规定时间内没有获得锁就返回。

调用该方法后当前线程进入睡眠状态，直到以下事件发生。

（1）其他线程调用了该对象的notify方法。

（2）其他线程调用了该对象的notifyAll方法。

（3）其他线程调用了interrupt中断该线程。

（4）时间间隔到了。

此时该线程就可以被调度了，如果是被中断的话就抛出一个InterruptedException异常。

8．notify方法
该方法唤醒在该对象上等待的某个线程。

9．notifyAll方法
该方法唤醒在该对象上等待的所有线程。

<h1 id="4">四、继承</h1>
<h2 id="4.1">访问权限</h2>
访问权限控制： 指的是本类及本类内部的成员（成员变量、成员方法、内部类）对其他类的可见性，即这些内容是否允许其他类访问。
<table>
<tr>
<td>权限</td><td>类内</td><td>同包</td><td>不同包子类</td><td>不同包非子类</td>
</tr>
<tr>
<td>private</td><td>√</td><td>×</td><td>×</td><td>×</td>
</tr>
<tr>
<td>default</td><td>√</td><td>√</td><td>×</td><td>×</td>
</tr>
<tr>
<td>protected</td><td>√</td><td>√</td><td>√</td><td>×</td>
</tr>
<tr>
<td>public</td><td>√</td><td>√</td><td>√</td><td>√</td>
</tr>
</table>

1. private：私有访问权限，被修饰的属性和方法只能被该类的对象访问。
2. default：默认访问权限或包访问权限，只允许在同一个包中进行访问。
3. protected：保护访问权限，被其修饰的属性和方法只能被类本身的方法及子类访问，即使子类在不同的包中也可以访问。
4. public：公共访问权限，被其修饰的类、属性以及方法不仅可以跨类访问，而且允许跨包访问。

<h2 id="4.2">使用场景</h2>

1. 外部类的访问控制
   
   外部类（外部接口） 是相对于内部类（也称为嵌套类）、内部接口而言的。外部类的访问控制只能是这两种：public 、default 。

2. 类里面的成员的访问控制
   
   类里面的成员分为三类 ： 成员变量、成员方法、成员内部类（内部接口）
   
   类里面的成员的访问控制可以是四种，也就是可以使用所有的访问控制权限。

3. 抽象方法的访问权限
   
   普通方法是可以使用四种访问权限的，但抽象方法是有一个限制：不能用private 来修饰，也即抽象方法不能是私有的，否则，子类就无法继承实现抽象方法。
4. 接口成员的访问权限
   
   接口由于其的特殊性，所有成员的访问权限都规定得死死的，下面是接口成员的访问权限：

   * 变量： public static final
   * 抽象方法： public abstract
   * 静态方法： public static，JDK1.8后才支持
   * 内部类、内部接口 ： public static
