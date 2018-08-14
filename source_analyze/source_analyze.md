* [一、数组与链表](#1)
  - [数组](#1.1)
  - [链表](#1.2)
  - [数组与链表的选择](#1.3)
* [二、哈希表](#2)
  - [Hash函数](#2.1)
  - [哈希碰撞](#2.2)
* [三、Java集合框架源码分析](#3)
  - [ArrayList](#3.1)
<h1 id="1">一、数组与链表</h1>
<h2 id="1.1">数组</h2>
在Java中，数组属于引用类型，继承了顶级类Object。
可以通过

    int[] nums = new int[]{0,1,0,3,12};
    nums.length;

以对象的方式获取到数组的长度，还可以调用Object类所拥有的方法。

数组在内存中用一组连续的存储单元存储数据，数组中的每个元素都对应于一组下标，数组中的素有元素必须属于同一数据类型。

![图片](https://github.com/UMRhamster/Jobs2018/raw/master/source_analyze/img/source_analyze_array.png)

* 创建数组时，必须声明数组的长度，并且数组在创建之后长度就固定了，不能对其大小进行动态修改。
* 可以通过下标对数组元素进行随机访问，并且其时间复杂度为O(1)。但是，对于查询，需要对数组进行遍历，所以时间复杂度为O(n)。
* 在进行数组元素的插入与删除时，效率是比较低的。因为，需要将插入位置以后的的所有元素一位，或将删除位置后的所有元素前移一位。这种移动是比较耗时的，特别是对于很长的数组。

### 数组的创建方式

    int [] array = {0,1,2,3};  //这种方式会自动分配对应元素数量的空间
    int[] array1 = new int[9];  //指明数组大小
    int array2 [] = new int[9];  //推荐使用第二种创建方式，可读性比较强 int[] 表示引用类型，而不是 int 类型。

### 数组中的length属性
首先可以确定Java数组中的length不是方法，（方法的表现形式应该为array.length()）。实际上length也不是属性。

Java对数组的操作是指令级的，在一个数组对象上调用length，会被Java编译器编译成一条arraylength指令（Java   binary   code）。

[java数组中的length属性到底属于java中的哪个类](https://blog.csdn.net/jayzym/article/details/76643527)

<h2 id="1.2">链表</h2>
链表是一种离散存储结构,用一组任意的存储单元存储数据（这些存储单元可以说是连续的，也可以是不连续的）。数据元素中的逻辑关系由节点中的指针指出,即每个数据元素都通过一个指针指向其下一个元素的地址。根据指针域的不同，链表又分为单链表、双向链表、循环链表等，这里我们只分析单链表。


![图片](https://github.com/UMRhamster/Jobs2018/raw/master/source_analyze/img/source_analyze_linked_list.png)

下面是链表中的节点，value代表节点的数据，next是指向下一个节点的引用

    public class ListNode {
        public int value;
        public ListNode next;
        public ListNode(int value){
            this.value = value;
        }
    }

* 声明链表时，不需要指明其长度，并且链表长度是可以动态变化的。
* 链表的节点分为数据域和指针域，数据域存放实际数据，指针域指向下一个节点。和数组相比，每个元素占用的内存更大，多一个引用所占用的空间。
* 因为链表不需要连续存储空间，所以不能进行随机访问，对于与第i个元素的访问，以及查询操作，都需要对链表进行遍历，时间复杂度为O(n)。
* 对于节点的插入和操作，链表表现较好，只需要在对应的节点处理next引用，就可以将一个节点删除或添加新节点。

<h2 id="1.3">数组与链表的选择</h2>

* 数组可以随机访问，但是插入和删除需要移动大量元素，比较耗时
* 链表对任意元素的访问都需要从头开始遍历，增加和删除比较方便，只需要修改元素引用。
* 数组创建之后大小固定，不可改变。链表大小可以动态调整。
* 数组需要连续内存空间，而链表不需要。
* 每个元素的内存占用上链表大于数组。

对与需要快速随机访问的场景，应该使用数组。对于频繁进行插入和删除操作的场景，应该使用链表。

<h1 id="2">哈希表</h1>
无论是数组还是链表,其查询效率都不是很高，都需要对数组或者链表进行遍历比较。我们没有办法直接通过元素知道它的存储位置。

一种有效的存储方式，是不与其他元素进行比较，一次存取便能得到所需要的记录。这就需要在对象的存储位置和对象的关键属性（设为 k）之间建立一个特定的对应关系（设为 f），使每个对象与一个唯一的存储位置相对应。在查找时，只要根据待查对象的关键属性 k 计算f(k)的值即可。按照这种思想建立的表为哈希表。
<h2 id="2.1">Hash函数</h2>
建立key值与value值映射关系的函数就是Hash函数。

Hash表其实就是一个数组，数组元素是包含key值与value值的实体。需要插入的元素通过Hash函数计算出存储位置后再插入。

![图片](https://github.com/UMRhamster/Jobs2018/raw/master/source_analyze/img/hash_table_hash_method.png)

<h2 id="2.2">哈希碰撞</h2>
然而通过Hash函数计算hash值，可能出现两个对象的hash值相同的情况,这就是发生了哈希碰撞。

哈希碰撞的解决方法：
* 开放定址法
* 链地址法
  
### 开放定址法
 
 当发生地址冲突时，按照某种方法继续探测哈希表中的其他存储单元，直到找到空位置为止。公式表示如下：

 ![图片](https://github.com/UMRhamster/Jobs2018/raw/master/source_analyze/img/hash_table_open_addressing_formula.png)

 其中，H(key)是hash函数，m为哈希表的长度。

举例如下：

Hash函数：H(key) =  key mod 7;
哈希表长度为 7 ；
key值 序列为 23，13，49，55，22，38，21

  ![图片](https://github.com/UMRhamster/Jobs2018/raw/master/source_analyze/img/hash_table_open_addressing.png)

  首先通过Hash函数计算key值的hash值。
  32 mod 7 = 4，  13 mod 7 = 6，  49 mod 7 = 0，此时未发生哈希碰撞；

  当插入 55 时，计算其hash值，55 mod 7 = 6,发生冲突。下一个地址为(6+1) % 7 = 0，仍然发生冲突，再看下一个地址(6+2) % 7 = 1，未发生冲突，放入。

  22 mod 7 = 1，发生冲突，下一个地址为(1+1) % 7 = 2，未发生冲突，可以放入。

  38 mod 7 = 3，未发生冲突，直接放入。

  21 mod 7 = 0，发生冲突，按照上如方法继续探测至地址空间5。

  ### 链地址法

  HashMap使用的解决哈希冲突的策略就是链地址法。

  链地址法将具有相同hash的记录存储在同一线性链表中。

   ![图片](https://github.com/UMRhamster/Jobs2018/raw/master/source_analyze/img/hash_table_chaining.png)

* 拉链法处理冲突简单，且无堆积现象，即非同义词决不会发生冲突，因此平均查找长度较短；
* 由于拉链法中各链表上的结点空间是动态申请的，故它更适合于造表前无法确定表长的情况；

<h1 id="3">三、Java集合框架源码分析</h1>
<h2 id="3.1">ArrayList</h2>

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

    //元素的数量
    private int size;
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
            //如果创建ArrayList对象时使用的是不带参数的构造方法，在第一次增加元素时，会自动扩容到DEFAULT_CAPACITY大小，也就是10。
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

### 查询元素
根据索引查询

    public E get(int index) {
        rangeCheck(index);  //先检查索引位置是否合法

        return elementData(index);
    }

    private void rangeCheck(int index) {
        if (index >= size)  //如果索引大于实际元素数量，则抛出异常，其他异常由数组秀对象进行抛出
            throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
    }

    E elementData(int index) {
        return (E) elementData[index];  //通过索引获取元素，强制类型转换为泛型
    }

查询指定元素第一次出现的位置

    //从头循环到尾匹配参数对象，区分null值和正常值，若查询失败，返回-1
    public int indexOf(Object o) {
        if (o == null) {
            for (int i = 0; i < size; i++)
                if (elementData[i]==null)
                    return i;
        } else {
            for (int i = 0; i < size; i++)
                if (o.equals(elementData[i]))
                    return i;
        }
        return -1;
    }

查询指定元素最后一次行出现的位置

    //与前面的一致，只是循环方向从尾到头
    public int lastIndexOf(Object o) {
        if (o == null) {
            for (int i = size-1; i >= 0; i--)
                if (elementData[i]==null)
                    return i;
        } else {
            for (int i = size-1; i >= 0; i--)
                if (o.equals(elementData[i]))
                    return i;
        }
        return -1;
    }

### 修改元素

   public E set(int index, E element) {
        rangeCheck(index);  //检查索引是否合法

        E oldValue = elementData(index);  //先获取老值，用于返回
        elementData[index] = element;  //修改为新值
        return oldValue;  //返回老值
    }

* ArrayList在使用默认不带参数的构造函数创建ArrayList对象时，初始化容量其实是0，只有在添加第一个元素的时候，才扩容至10.
* 每次扩容至原来大小的1.5倍，如果扩容后的大小还不够，就使用需要的长度。
  
<h2>LinkedList</h2>
    
    public class LinkedList<E>
        extends AbstractSequentialList<E>
        implements List<E>, Deque<E>, Cloneable, java.io.Serializable
    {

LinkedList内部使用双向链表进行存储。继承了AbstractSequentialList，实现了List，Deque,Cloneable,Seriablizable接口。与ArrayList不同的是没有实现RandomAccess，因此不能进行随机访问。

实现了List接口，能够对它进行队列操作;实现了Deque接口，即能将LinkedList当作双端队列使用。实现Cloneable接口，因此能够被克隆;实现了Serializable接口，能够被序列化传输。

    //元素的数量
    transient int size = 0;

    //first指向链表头
    transient Node<E> first;

    //last指向链表尾
    transient Node<E> last;

双向链表节点Node类

    private static class Node<E> {
        E item;  //数据
        Node<E> next;  //指向后一个节点
        Node<E> prev;  //指向前一个节点

        Node(Node<E> prev, E element, Node<E> next) {
            this.item = element;
            this.next = next;
            this.prev = prev;
        }
    }
### 构造方法

    public LinkedList() {
    }

    //根据已有集合，将集合中的数据添加到链表中
    public LinkedList(Collection<? extends E> c) {
        this();
        addAll(c);
    }

### 添加元素

添加元素到末尾

    public boolean add(E e) {
        linkLast(e);  //添加到末尾
        return true;
    }

    public void addLast(E e) {
        linkLast(e);
    }

    void linkLast(E e) {
        final Node<E> l = last;
        final Node<E> newNode = new Node<>(l, e, null);  //构造新节点，其指向前一个的指针指向尾指针
        last = newNode;  //让尾指针指向新节点
        if (l == null)  //如果原本尾指针为null，即链表为空
            first = newNode;  //让头指针指向新节点，即新节点既是头节点，又是尾节点
        else
            l.next = newNode;  //否则，让原来的尾指针的指向下一节点的指针指向新节点
        size++;
        modCount++;
    }

添加元素到头

    public void addFirst(E e) {
        linkFirst(e);
    }

    private void linkFirst(E e) {
        final Node<E> f = first;
        final Node<E> newNode = new Node<>(null, e, f);  //构造新节点，其指向后一个节点的指针指向头指针
        first = newNode; //让头指针指向新节点
        if (f == null)  //如果原本头指针为null,即链表为空
            last = newNode;  //让尾指针也指向新节点
        else
            f.prev = newNode; //否则，让原来的头指针指向前一节点的指针指向新节点
        size++;
        modCount++;
    }
添加到指定位置

    public void add(int index, E element) {
        checkPositionIndex(index);  //检查插入位置是否合法

        if (index == size)
            linkLast(element);  //如果指定位置是末尾，直接进行尾插
        else
            linkBefore(element, node(index));  //根据原来该位置的的节点进行添加
    }

    private void checkPositionIndex(int index) {
        if (!isPositionIndex(index))
            throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
    }

    private boolean isPositionIndex(int index) {
        return index >= 0 && index <= size;
    }

    //查询节点
    Node<E> node(int index) { 
        if (index < (size >> 1)) { //确定从头开始遍历还是从尾开始遍历
            Node<E> x = first;
            for (int i = 0; i < index; i++)
                x = x.next;  //从头遍历至指定位置
            return x;  //返回该节点
        } else {
            Node<E> x = last;
            for (int i = size - 1; i > index; i--)
                x = x.prev;  //从尾遍历至指定位置
            return x;  //返回该节点
        }
    }

    void linkBefore(E e, Node<E> succ) {
        // assert succ != null;
        final Node<E> pred = succ.prev;  //得到原节点的前一节点
        final Node<E> newNode = new Node<>(pred, e, succ);  //构造新节点，让其前指向节点指向原来该位置节点的前节点，让其后指向节点指向原来该位置的节点
        succ.prev = newNode;  //让原来的节点的前指向节点指向新节点
        if (pred == null) //如果原节点为null,说明插入在头节点之前
            first = newNode;  //重置头节点
        else
            pred.next = newNode;  //否则，将将前一节点的后指向指针指向新节点即可
        size++;
        modCount++;
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

  