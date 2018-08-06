>参考《Java高级特性编程及实战》

* [一、集合框架](#1)
  - [List接口及其实现类](#1.1)
  - [Set接口及其实现类](#1.2)
  - [Map接口及其实现类](#1.3)
* [二、集合源码分析](#2)
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

<h1 id="2">二、集合源码分析</h1>

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
            //如果楚航舰ArrayList对象时使用的是不带参数的构造方法，在第一次增加元素时，会自动扩容到DEFAULT_CAPACITY大小。
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
