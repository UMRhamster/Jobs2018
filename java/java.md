* [一、数据类型](#1)
  - [基本数据类型及其包装类型](#1.1)
  - [引用类型](#1.2)
  - [值传递和引用传递](#1.3)
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

