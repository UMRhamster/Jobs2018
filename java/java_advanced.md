>参考《Java高级特性编程及实战》

* [一、集合框架](#1)
  - [List接口及其实现类](#1.1)
  - [Set接口及其实现类](#1.2)
  - [Map接口及其实现类](#1.3)
* [二、Java I/O](#2)
  - [File类](#2.1)
  - [Java的流](#2.2)
  - [读写文本文件](#2.3)
  - [序列化与反序列化](#2.4)
* [三、反射](#3)
<h1 id="1">一、集合框架</h1>
如果想要存储多个同类型的数据，可以使用数组来实现；但是使用数组存在一些明显的缺陷：
 
* 数组长度固定，不能很好地适应元素数量动态变化的情况。
* 可通过 数组名.length 获取数组的长度，却无法直接获取数组中实际存储的元素个数。
* 数组采用在内存中分配连续空间的存储方式存储，根据元素信息查找时效率比较低，需要多次比较。

Java中的集合类：是一种工具类，就像是容器，存储任意数量的具有共同属性的对象。

Java集合框架提供了一套性能优良、使用方便的接口和类，他们都位于java.util包中。

![图片](https://github.com/UMRhamster/Jobs2018/raw/master/java/img/collection_relationship.png)

<h2 id="1.1">List接口及其实现类</h2>
List是有序的Collection，使用此接口能够精确的控制每个元素插入的位置。用户能够使用索引来访问List中的元素，类似于数组。

### ArrayList
ArrayList实现了可变大小的数组。适用于大量随机访问的情况，但是插入和删除上，性能相对较差。
### LinkedList
LinkedList类似于链表，进行插入、删除比较高校，但是随机访问性能要比动态数组慢。
### Vecotr
Vector类似于ArrayList，但是Vector是线程安全的

如果需要快速插入、删除元素，应该考虑LinkedList，如果需要快速随机访问元素，应该考虑ArrayList。
<h2 id="1.2">Set接口及其实现类</h2>
Set是一种不包含重复元素的Collection。

### HashSet
HashSet使用了散列函数实现，极大的提高了访问速度。存入HashSet的对象必须定义hashCode()。HashSet不允许重复元素，也不能保证集合中元素的顺序，允许但只能有一个null值。
### TreeSet
TreeSet使用红黑树来实现存储元素， 红黑树的好处是可以插入之后维持集合的有序性。
<h2 id="1.3">Map接口及其实现类</h2>
Map通过散列机制，提供key到value的映射。

### HashMap
底层是哈希表数据结构，允许使用 null 值和 null 键，该集合是不同步的。
### HashTable
底层是哈希表数据结构，不可以存入null键null值。该集合是线程同步的。
### TreeMap
底层是二叉树数据结构。线程不同步。可以用于给map集合中的键进行排序。

<h1 id="2">Java I/O</h1>

java.io包提供了一些接口和类，对文件进行基本的操作，包括文件和目录属性的操作、对文件的读写的操作等等。
<h2 id="2.1">File类</h2>
File对象既可以表示文件，也可以表示目录，在程序中一个File对象可以代表一个文件或目录。利用它可用来对文件或目录进行基本操作。它可以查出与文件相关的信息，如名称、最后修改日期、文件大小等。

### File类的构造方法
（1）File(String pathname)

用指定的文件路径来创建文件对象

（2）File(String dir, String subpath);

在指定的目录下创建指定文件名的文件对象；dir：指定目录路径；subpath：指定文件名

（3）File(File parent, String subpath);

根据一个文件对象和指定文件名创建文件对象；parent：指定目录文件；subpath：指定文件名

### File类常用方法
<table>
<tr>
<td>方法</td><td>说明</td>
</tr>
<tr>
<td>boolean exists()</td><td>判断文件是否存在</td>
</tr>
<tr>
<td>String getAbsolutePath()</td><td>返回此对象的文件的绝对路径</td>
</tr>
<tr>
<td>String getName()</td><td>返回此对象表示的文件的名称</td>
</tr>
<tr>
<td>String getParent()</td><td>返回此对象的路径名的上一级，如果没有上一级，则返回null;即返回文件所在目录的路径</td>
</tr>
<tr>
<td>boolean delete()</td><td>删除此对象指定的文件</td>
</tr>
<tr>
<td>boolean createNewFile()</td><td>创建空文件，不创建文件夹</td>
</tr>
<tr>
<td>boolean isDirectory()</td><td>判断此对象表示的是否为目录</td>
</tr>
<tr>
<td>boolean mkdir()</td><td>用于创建目录，其父目录必须由当前File对象指定</td>
</tr>
<tr>
<td>boolean mkdirs()</td><td>用于创建目录，若父目录不存在，会自动创建</td>
</tr>
</table>

    File directory = new File("C:\\Users\\12421\\Desktop\\test");  //先构造一个目录
    directory.mkdirs();  //调用mkdirs()方法创建目录，包括其父目录
    File file = new File(directory,"text.txt");  //由目录文件对象和文件名创建文件对象
    if (!file.exists()){  //判断文件是否存在，若不存在
        try {
            file.createNewFile();  //创建新文件
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    file.delete();  //调用delete()删除文件

<h2 id="2.2">Java的流</h2>
File类能够对文件或目录的属性进行操作，但File类不能访问文件的内容，即不能从文件中读取数据或往文件里写数据。

读文件是指把文件中的数据读取到内存中。写文件是指把内存中的数据写到文件中。Java中通过流来读写文件。

流是指一连串流动的字符，是以先进先出的方式发送和接受数据的通道。

[图片]

### 分类
#### 按方向分
输入流和输出流

输入/输出流是相对于计算机内存来说的，如果数据输入到内存，则称为输入流，如果从内存中输出则称为输出流。
#### 按处理数据的单位分
字节流和字符流；字节流读取的最小单位是一个字节（8位），字符流一次可以读取一个字符（16位Unicode字符）。
#### 按照功能分
节点流和处理流；节点流是直接从一个源读写数据的流（这个流没有经过包装和修饰），处理流是在对节点流封装的基础上的一种流。

不管流的分类是多么的丰富和复杂，其根源来自于四个基本的抽象类。
<table>
<tr>
<td></td><td>字节流</td><td>字符流</td>
</tr>
<tr>
<td>输入流</td><td>InputStream</td><td>Reader</td>
</tr>
<tr>
<td>输出流</td><td>OutputStream</td><td>Writer</td>
</tr>
</table>

#### InputStream类的常用方法
<table>
<tr>
<td>方法</td><td>说明</td>
</tr>
<tr>
<td>int read()</td><td>从输入流中读取下一个字节数据</td>
</tr>
<tr>
<td>int read(byte[] b)</td><td>从输入流中读取数据，并将数据存储在缓冲区数组b中，返回实际读取的字节数</td>
</tr>
<tr>
<td>int read(byte[] b,int off, int len)</td><td>从输入流中读取最多len长度的字节，保存到字节数组b中，保存的位置从off开始</td>
</tr>
<tr>
<td>void close()</td><td>关闭输入流</td>
</tr>
</table>

#### OutputStream类的常用方法
<table>
<tr>
<td>方法</td><td>说明</td>
</tr>
<tr>
<td>int write(int c)</td><td>将指定的字节数据写入此输出流</td>
</tr>
<tr>
<td>int write(byte[] buf)</td><td>将数组buf中的所有字节写入此输出流中</td>
</tr>
<tr>
<td>int write(byte[] b,int off, int len)</td><td>将字节数组中从偏移量off开始的长度为len的字节数据输出到输出流中</td>
</tr>
<tr>
<td>void close()</td><td>关闭输出流</td>
</tr>
</table>


#### Reader类的常用方法
<table>
<tr>
<td>方法</td><td>说明</td>
</tr>
<tr>
<td>int read()</td><td>从输入流中读取单个字符，返回所读取的字符数据</td>
</tr>
<tr>
<td>int read(char[] c)</td><td>从输入流中最多读取c.length个字符，保存到字符数组c中，返回实际读取的字符个数</td>
</tr>
<tr>
<td>int read(char[] c,int off, int len)</td><td>从输入流中读取最多len个字符，保存到字符数组c中，保存的位置从off开始，返回实际读取的字符数</td>
</tr>
<tr>
<td>void close()</td><td>关闭输入流</td>
</tr>
</table>

#### Write类的常用方法
<table>
<tr>
<td>方法</td><td>说明</td>
</tr>
<tr>
<td>int write(String str)</td><td>将sre字符串里包含的字符输出到输出流中</td>
</tr>
<tr>
<tr>
<td>int write(String str,int off, int len)</td><td>将str字符串里从off位置开始，长度为len的多个字符输出到输出流中</td>
</tr>
<tr>
<td>void close()</td><td>关闭输出流</td>
</tr>
<td>void flush()</td><td>刷新输出流</td>
</tr>
</table>

<h2 id=“2.3”>读写文本文件</h2>

1. 使用字节流读写文本文件
   1. 使用FileInputStream读文本文件
   
      FileInputStream 称为文件输入流，它是字节输入流InputStream抽象类的一个子类,它的作用是将文件中的数据输入到内存中，可以利用它来读写文本文件中的数据。

      FileInputStream(File file)&nbsp;&nbsp;&nbsp;&nbsp;//通过File对象指定文件，构造FileInputStream对象

      FileInputStream(String name)&nbsp;&nbsp;&nbsp;&nbsp;//通过路径指定文件，构造FileInputStream对象

          InputStream inputStream = new FileInputStream(new File("C:\\Users\\12421\\Desktop\\test.txt"));
          int data = 0;
          while((data = inputStream.read()) != -1){
              System.out.print(data+" ");  //97 98 99
              //应为read()方法返回的是int类型,对应字符的ASCII码，需要进行强制类型转换才能输出字符串。
              System.out.print((char)data+" ");  //a b c
          }
          inputStream.close();
    1. 使用FileOuputStream写文本文件

       FileOutputStream 称为文件输出流，它是字节输出流OutputStream抽象类的子类，它的作用是把内存中的数据输出到文件中，可以利用它把内存中的数据写入到文本文件中。

       FileOutputStream(File file)  //通过File对象指定文件，创建FileOutputStream对象

       FileOutputStream(File file, boolean append)  //通过File对象指定文件，创建FileOutputStream对象，并指定是否以追加方式写入

       FileOutputStream(String name)  //通过路径指定文件，创建FileOutputStream对象
     
       FileOutputStream(String name, boolean append)  //通过路径指定文件，创建FileOutputStream对象，并指定是否以追加方式写入

           OutputStream outputStream = new FileOutputStream(new File("C:\\Users\\12421\\Desktop\\test.txt"));
           outputStream.write("abc".getBytes());  //将字符串转换成字节数组
           outputStream.close();

2. 使用字符流读写文件
   2. 使用BufferedReader和FileReader读文本文件
      
      BufferedReader和FileReader两个类都是Reader抽象类的子类，它们可以通过字符流的方式读取文件，并使用缓冲区，提高了读文本文件的效率。

          FileReader fileReader = new FileReader("C:\\Users\\12421\\Desktop\\test.txt");
          BufferedReader bufferedReader = new BufferedReader(fileReader);
          String line = bufferedReader.readLine();  //读取一行数据
          while(line!=null){
              System.out.println(line);
              line = bufferedReader.readLine();
          }
          bufferedReader.close();
          fileReader.close();
    1. 使用BufferedWriter和FileWriter写文本文件
       
       BufferedWriter和FileWriter都是字符流输出流Writer抽象类的子类，它们可以通过字符流的方式并通过缓冲区把数据写入文本文件，提高了写文本文件的效率。

           FileWriter fileWriter = new FileWriter("C:\\Users\\12421\\Desktop\\test.txt");
           BufferedWriter bufferedWriter = new BufferedWriter(fileWriter);
           bufferedWriter.write("abc");
           bufferedWriter.newLine();
           bufferedWriter.flush();
           bufferedWriter.close();
           fileWriter.close();

        在输出流在进行输出时，比如向某个文件中写入内容，其实是先将输出流写入到缓冲区，当缓冲区写满后才将缓冲区的内容输出到文件中。但是当主机完成输出流的输出后，有可能缓冲区这个时候还没有被填满，这样的话，就会一直等待主机发送内容，这时候，就可以使用flush将缓冲区的内容强制输出到文件中，清空缓冲区。 

        close方法在关闭流时会同时刷新缓冲区。

<h2 id="2.4">序列化与反序列化</h2>

### 序列化
序列化就是将对象的状态存储到特定存储介质中的过程，也就是将对象状态转换为可保持或可传输格式的过程。在序列化过程中，会将对象的公有成员、私有成员包括类名，转换为字节流，然后再把字节流写入数据流，存储到存储介质中。


使用序列化的意义在于将Java对象序列化之后，可以将其转换为字节序列，这些字节序列可以被保存在磁盘上，也可以借助网络进行传输，同时序列化后的对象保存的是二进制状态，这样实现了平台无关性。

Java中只有实现了 java.io.Serializable 接口的类的对象才能被序列化，Serializable表示可串行的、可序列化的。JDK类库中，如String、包装类和Date类等都实现了Serializable接口。
   
    static class Student implements Serializable{  //注意实现Serializable，否则会产生java.io.NotSerializableException异常
        String name;
        public Student(String name){
            this.name = name;
        }

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }
    }

    File directory = new File("C:\\Users\\12421\\Desktop\\test");
        directory.mkdirs();
        File file = new File(directory,"test.txt");
        if (!file.exists()){
            try {
                file.createNewFile();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        try {
            FileOutputStream fileOutputStream = new FileOutputStream(file);  //创建文件输出流
            ObjectOutputStream objectOutputStream = new ObjectOutputStream(fileOutputStream);  //包装文件输出流，创建对象输出流
            Student student = new Student("张三");  
            objectOutputStream.writeObject(student);  //序列化对象
        } catch (IOException e) {
            e.printStackTrace();
        }

### 反序列化
既然能够将对象的状态保存到存储介质中，那么如何将这些对象状态读取出来呢？这就用到反序列化。

序列化就是将对象的状态信息保存到存储介质中，反序列化则是从特定的存储介质中读取数据并重新构建成对象的过程。通过反序列化，可以将存储在文件上的对象信息读取出来，然后重新构建为对象。这样就不需要再将文件上的信息一一读取、分析再组织为对象。

    try {
            FileInputStream fileInputStream = new FileInputStream(file);
            ObjectInputStream objectInputStream = new ObjectInputStream(fileInputStream);
            Student student = (Student) objectInputStream.readObject();  //通过readObject读取数据并创建对象，再强制类型转换为Student
            System.out.println(student.getName());  //张三
        } catch (IOException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
通常，对象中的所有属性都会被序列化，但是对于一些比较敏感的信息，如用户的密码，一旦序列化后，人们完全可以通过读取文件或拦截网络传输出数据的方式获得这些信息。因此，可以通过使用transient修饰某些属性，限制被序列化。

注：
* 如果一个类可以序列化，则它的父类要么是可序列化的，要么有无参构造函数；否则会抛出异常。
* 如果一个类对象成员包含其他类的对象，当序列化此对象时，必须保证当前类和成员对象类都是可序列化的。
* 序列化与反序列化时，必须要保证序列化ID一致。即 private static final long serialVersionUID 如果没有特殊需求，就是用默认的 1L 就可以。

<h1>反射</h1>

|

|

|

|

|














<h1 >二、集合源码分析</h1>
<h2 >ArrayList源码分析</h2>

    public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
    {
ArrayList实际上是一个动态数组，容量可以动态的增长，其继承了AbstractList，实现了List, RandomAccess, Cloneable, java.io.Serializable这些接口。

实现了RandomAccess接口，因此支持随机访问，可通过下表获取元素对象。
实现了Cloneable接口，可以被克隆。
实现了Serializable接口，可以被序列化。

    //默认容量
    private static final int DEFAULT_CAPACITY = 10;

    //空的数组对象
    private static final Object[] EMPTY_ELEMENTDATA = {};

    //默认的空数组
    private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};

    //存放数据的数组的缓存变量
    transient Object[] elementData; 
### 构造方法
带有容量initialCapacity的构造方法

    public ArrayList(int initialCapacity) {
        //如果初始化容量大于0
        if (initialCapacity > 0) {
            //创建一个该大小的Object数组赋值给elementData
            this.elementData = new Object[initialCapacity];
        } else if (initialCapacity == 0) {  //如果容量为0
            //则将空数组赋值给elementData
            this.elementData = EMPTY_ELEMENTDATA;
        } else {
            //其他情况抛出异常
            throw new IllegalArgumentException("Illegal Capacity: "+
                                               initialCapacity);
        }
    }

不带参数的构造方法

    public ArrayList() {
        //直接将默认空数组赋值给elementData
        this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
    }

带参数Collection的构造方法

    //将一个Collection实现类的对象转换成一个ArrayList，但是c容器的内容必须是ArrayList容器内容的子类。
    public ArrayList(Collection<? extends E> c) {
        //调用toArray()方法，将c容器转换成Object数组
        elementData = c.toArray();
        if ((size = elementData.length) != 0) {
            //有些容器的toArray()方法不能装换成Object数组
            if (elementData.getClass() != Object[].class)
                elementData = Arrays.copyOf(elementData, size, Object[].class);
        } else {
            如果c容器的大小不大于0，则初始化为空数组
            this.elementData = EMPTY_ELEMENTDATA;
        }
    }
### 添加元素

添加元素到末尾

    public boolean add(E e) {
        ensureCapacityInternal(size + 1);  // 确定是否需要扩容
        elementData[size++] = e;
        return true;
    }

    private void ensureCapacityInternal(int minCapacity) {
        if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
            //如果创建ArrayList对象时使用的是不带参数的构造方法，在第一次增加元素时，会自动扩容到DEFAULT_CAPACITY大小。
            minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
        }

        ensureExplicitCapacity(minCapacity);
    }

    private void ensureExplicitCapacity(int minCapacity) {
        modCount++;

        //如果增加元素后的容量大于现在的容量，则需要进行扩容
        if (minCapacity - elementData.length > 0)
            grow(minCapacity);
    }

    private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1);  //扩容50%
        //如果扩容后还是小于所需要的容量，则将容量改为所需要的容量
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
            //如果扩容后的容量大于最大容量
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
        elementData = Arrays.copyOf(elementData, newCapacity);
    }

    private static int hugeCapacity(int minCapacity) {
        if (minCapacity < 0) // 判断是否溢出
            throw new OutOfMemoryError();
            //得到一个合适的容量
        return (minCapacity > MAX_ARRAY_SIZE) ?
            Integer.MAX_VALUE :
            MAX_ARRAY_SIZE;
    }

添加元素到指定位置

    public void add(int index, E element) {
        //先检查添加的位置是否合法
        rangeCheckForAdd(index);

        ensureCapacityInternal(size + 1);  // 这里和上面一样
        //调用System.arraycopy进行数组移动，将指定位置开始以后的元素往后移一位
        System.arraycopy(elementData, index, elementData, index + 1,
                         size - index);
        //将element放置指定位置
        elementData[index] = element;
        size++;
    }

### 删除元素
根据索引删除

    public E remove(int index) {
        //检查删除位置是否合法
        rangeCheck(index);

        modCount++;
        //获取删除元素，用于返回
        E oldValue = elementData(index);

        //计算出需要移动的元素的数量
        int numMoved = size - index - 1;
        if (numMoved > 0)
            System.arraycopy(elementData, index+1, elementData, index,
                             numMoved);
        将末尾元素置空，让GC自动回收
        elementData[--size] = null;

        return oldValue;
    }

根据元素值来删除

    public boolean remove(Object o) {
        //如果要删除元素为空
        if (o == null) {
            for (int index = 0; index < size; index++)
                //循环使用==判断是否为空即可，fastMove与上面的删除方法基本一致，只是不返回删除元素
                if (elementData[index] == null) {
                    fastRemove(index);
                    return true;
                }
        } else {
            for (int index = 0; index < size; index++)
                //使用equals匹配需要删除的元素，如果没有重写equals()方法，默认是比较地址，根据需要可进行重写
                if (o.equals(elementData[index])) {
                    fastRemove(index);
                    return true;
                }
        }
        return false;
    }

删除所有元素

    public void clear() {
        modCount++;

        //循环将全部元素置空，由GC处理内存回收
        for (int i = 0; i < size; i++)
            elementData[i] = null;

        size = 0;
    }

<h2 >HashMap源码分析</h2>

HashMap基于哈希表的 Map 接口的实现。此实现提供所有可选的映射操作，并允许使用 null 值和 null 键。（除了不同步和允许使用 null 之外，HashMap 类与 Hashtable 大致相同。）此类不保证映射的顺序，特别是它不保证该顺序恒久不变。

HashMap底层主要是基于数组和链表来实现的,通过计算散列值来决定存储的位置,所以查询速度非常快。HashMap中是通过keyd额hashCode来计算hash值的,只要hashCode相同,计算粗来的hash值就相同。如果存储的对象对多了，就有可能不同的对象所算出来的hash值是相同的，这就出现了所谓的hash冲突。解决hash冲突的方法有很多，HashMap底层是通过链表来解决hash冲突的。

    public class HashMap<K,V> extends AbstractMap<K,V>
        implements Map<K,V>, Cloneable, Serializable {

继承了AbstractMap抽象类，实现了Map接口。而HashTable则是继承Dictionary抽象类

    static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; //默认初始容量为16

    static final int MAXIMUM_CAPACITY = 1 << 30; //最大容量为2^30

    static final float DEFAULT_LOAD_FACTOR = 0.75f; //默认加载因子为0.75

    static final int TREEIFY_THRESHOLD = 8;

    static final int MIN_TREEIFY_CAPACITY = 64;

    static class Node<K,V> implements Map.Entry<K,V> {
        final int hash;
        final K key;
        V value;
        Node<K,V> next;

        Node(int hash, K key, V value, Node<K,V> next) {
            this.hash = hash;
            this.key = key;
            this.value = value;
            this.next = next;
        }

        public final K getKey()        { return key; }
        public final V getValue()      { return value; }
        public final String toString() { return key + "=" + value; }

        public final int hashCode() {
            return Objects.hashCode(key) ^ Objects.hashCode(value);
        }

        public final V setValue(V newValue) {
            V oldValue = value;
            value = newValue;
            return oldValue;
        }

        public final boolean equals(Object o) {
            if (o == this)
                return true;
            if (o instanceof Map.Entry) {
                Map.Entry<?,?> e = (Map.Entry<?,?>)o;
                if (Objects.equals(key, e.getKey()) &&
                    Objects.equals(value, e.getValue()))
                    return true;
            }
            return false;
        }
    }

    transient Node<K,V>[] table;   //哈希数组

    transient Set<Map.Entry<K,V>> entrySet;

    transient int size; //键值对数量

### 构造方法

    public HashMap(int initialCapacity, float loadFactor) {
        if (initialCapacity < 0)
            throw new IllegalArgumentException("Illegal initial capacity: " +
                                               initialCapacity);
        if (initialCapacity > MAXIMUM_CAPACITY)
            initialCapacity = MAXIMUM_CAPACITY;
        if (loadFactor <= 0 || Float.isNaN(loadFactor))
            throw new IllegalArgumentException("Illegal load factor: " +
                                               loadFactor);
        this.loadFactor = loadFactor;
        this.threshold = tableSizeFor(initialCapacity);
    }

    public HashMap(int initialCapacity) {
        this(initialCapacity, DEFAULT_LOAD_FACTOR);
    }


    public HashMap() {
        this.loadFactor = DEFAULT_LOAD_FACTOR; 
    }

可以看到HashMap的构造方法非常简单，仅仅是对初始容量和扩容因子进行初始化，其他属性使用默认值。

添加元素

    public V put(K key, V value) {
        return putVal(hash(key), key, value, false, true);
    }

    final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
        Node<K,V>[] tab; Node<K,V> p; int n, i;
        if ((tab = table) == null || (n = tab.length) == 0)  //table为空或length等于0
            n = (tab = resize()).length;
        if ((p = tab[i = (n - 1) & hash]) == null)
            tab[i] = newNode(hash, key, value, null);
        else {
            Node<K,V> e; K k;
            if (p.hash == hash &&
                ((k = p.key) == key || (key != null && key.equals(k))))
                e = p;
            else if (p instanceof TreeNode)
                e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
            else {
                for (int binCount = 0; ; ++binCount) {
                    if ((e = p.next) == null) {
                        p.next = newNode(hash, key, value, null);
                        if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                            treeifyBin(tab, hash);
                        break;
                    }
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        break;
                    p = e;
                }
            }
            if (e != null) { // existing mapping for key
                V oldValue = e.value;
                if (!onlyIfAbsent || oldValue == null)
                    e.value = value;
                afterNodeAccess(e);
                return oldValue;
            }
        }
        ++modCount;
        if (++size > threshold)
            resize();
        afterNodeInsertion(evict);
        return null;
    }