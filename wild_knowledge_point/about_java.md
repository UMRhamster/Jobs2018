<h2 id="1">1. Java中创建对象的5种方式</h2>

### 1. 使用new关键字
   
   最常见最简单的创建对象的方式。通过这种方式，可以调用任意构造函数（无参和带参）。

       Student student = new Student();   //无参构造函数
       Student student = new Student("张三");  //带参构造函数

### 2. 使用Class类的newInstance方法
   
   通过Class类的newInstanc方法调用无参构造函数创建对象。

       Student student = (Student) Class.forName("com.whut.umrhamster.Student").newInstance();

       Student student = Student.class.newInstance;

### 3. 使用Constructor类的newInstance方法
   
   和Class类的newInstance方法很像， java.lang.reflect.Constructor类里也有一个newInstance方法可以创建对象。我们可以通过这个newInstance方法调用有参数的和私有的构造函数。

   
       Constructor<Student> constructor = Student.class.getConstructor();
       Student student = constructor.newInstance();

   newInstance方法内部调用Constructor的newInstance方法。这也是众多框架，如Spring、Hibernate、Struts等使用后者的原因。

### 4. 使用clone方法

   当我们调用一个对象的clone方法，jvm就会创建一个新的对象，将前面对象的内容全部拷贝进去。用clone方法创建对象并不会调用任何构造函数。

   要使用clone方法，我们需要先实现Cloneable接口并实现其定义的clone方法。

       Student student = new Student("张三");
       Student studentClone = (Student) student.clone();

### 5. 使用反序列化
   
   当我们序列化和反序列化一个对象，jvm会给我们创建一个单独的对象。在反序列化时，jvm创建对象并不会调用任何构造函数。

   为了反序列化一个对象，我们需要让我们的类实现Serializable接口。

       ObjectInputStream in = new ObjectInputStream(new FileInputStream("test.txt"));
       Student student = (Student) in.readObject();

<h2>深拷贝与浅拷贝</h2>

### 基本数据类型的拷贝
基本数据类型的复制比较简单,通过一下方式就可以达到复制的目的，并且修改新值不会影响到旧值。

    int i=5;
    int j = i;
    j = 7;
    System.out.println(i);  //5
    System.out.println(j);  //7

### 引用数据类型的拷贝
#### 引用拷贝

    Student student = new Student();
    Student student1 = student;
    System.out.println(student);  //test.Student@7ea987ac
    System.out.println(student1);  //test.Student@7ea987ac
    student.setName("Kitty");   //修改student的姓名
    System.out.println(student1.getName());  //Kitty   

可以发现student和student1的输出结果是一样，即引用的同一个对象，并没有创建新对象。并且我们通过修改student的姓名，发现student1的姓名也发生了变化。

### 浅拷贝
浅拷贝时，会创建一个新的对象，如果属性是基本数据类型，则拷贝的是基本数据类型的值，如果属性是引用类型，则拷贝的是内存地址。

    public class Student implements Cloneable{
        private String name;
        private int age;

        public Student(){}

        public Student(String name, int age){
            this.name = name;
            this.age = age;
        }

        //get和set方法省略
        //...

        @Override
        public Object clone() throws CloneNotSupportedException {
            return super.clone();
        }
    }

    public static void main(String[] args){
        Student student = new Student("Alice",18);
        try {
            Student student1 = (Student) student.clone();
            System.out.println(student);  //test.Student@7ea987ac
            System.out.println(student1);  //test.Student@12a3a380
            System.out.println(student.getName() == student1.getName()); //true
        } catch (CloneNotSupportedException e) {
            e.printStackTrace();
        }
    }

通过以上代码可以看出student和student1的地址确实不同了，但是它们的name还是指向的同一个对象。你可能会认为这是字符串常量池导致的，可以尝试通过 new String("...") 的方式去创建String对象，会发现结果还是一样的。

这就是浅拷贝，对于引用类型来说，仅仅只是拷贝了引用。

不过String类型有点特殊，因为它是不可变类型，任何修改操作都会产生新字符串，并重新指向。

    student.setName("Peter");
    System.out.println(student1.getName());  //Alice

如果我们对student对象修改名字，对student1的名字输出，发现student1的名字并没有改变。因为String的不可变类型，修改时，会指向新的对象，对原来的对象并没有任何改变。

那么如何解决浅拷贝的问题呢？
### 深拷贝
深拷贝会拷贝所有的属性，会同时拷贝引用的对象。

    public class Student implements Cloneable{
        private Course course;

        public Student(){}

        public Student(Course course){
            this.course = course;
        }

        //get和set方法省略
        //...

        @Override
        public Object clone() throws CloneNotSupportedException {
            Student newStudent = (Student) super.clone();
            newStudent.course = (Course) this.course.clone();  //将引用的Coure对象也拷贝一份
            return newStudent;
        }
    }

    class Course implements Cloneable{
        private Grade grade;

        public Course(Grade grade){
            this.grade = grade;
        }

        //get和set方法省略
        //...

        @Override
        public Object clone() throws CloneNotSupportedException {
            Course newCourse = (Course) super.clone();
            newCourse.grade = (Grade) this.grade.clone();  //将引用的Grade对象也拷贝一份
            return newCourse;
        }
    }

    class Grade implements Cloneable{
        int score;
        
        public Grade(int score){
            this.score = score;
        }

        //get和set方法省略
        //...

        @Override
        public Object clone() throws CloneNotSupportedException {
            return super.clone();  //因为Grade内部只有一个基本数据类型，直接使用默认浅拷贝就可以了
        }
    }

    pulic void main(String[] args){
        Student student = new Student(new Course(new Grade(90)));  //使用匿名类的方式创建Student对象
        try {
            Student student1 = (Student) student.clone();
            System.out.println(student == student1);  //false
            System.out.println(student.getCoure() == student1.getCourse());  //false
            System.out.println(student.getCoure().getGrade() == student1.getCourse().getGrade());  //false
        } catch (CloneNotSupportedException e) {
            e.printStackTrace();
        }
    }

按照上述代码，如果待拷贝对象内部引用了其他对象，需要显示的调用内部引用的其他对象的拷贝方法，并递推下去，直到基本数据类型。

当然如果遇到内部引用了第三方库的类，并且没有实现Cloneable接口，这样就无法做到深拷贝。

可以通过序列化与反序列实现彻底的深拷贝，即使引用了第三方库的类，新拷贝的对象也是引用新的对象，而不是源对象的。

<h2>JVM内存区域划分</h2>
JVM内存区域包括为：程序计数器、Java虚拟机栈、本地方法栈、堆、方法区。
### 程序计数器
用来指示当前正在执行的字节码指令地址。

如果线程执行的是一个Java方法，那么程序计数器记录的是正在执行的指令地址。<br/>
如果线程执行的是一个Native方法，那么程序计数器的记录值为空。

由于程序计数器所占空间大小是固定的，所以对于程序计数器不会出现内存溢出（OutOfMemoryError）情况。
### Java虚拟机栈
是Java方法执行的内存模型

Java栈中存放的是一个个的栈帧，每个栈帧对应一个被调用的方法，在栈帧中包括局部变量表(Local Variables)、操作数栈(Operand Stack)、指向当前方法所属的类的运行时常量池（运行时常量池的概念在方法区部分会谈到）的引用(Reference to runtime constant pool)、方法返回地址(Return Address)和一些额外的附加信息。当线程执行一个方法时，就会随之创建一个对应的栈帧，并将建立的栈帧压栈。当方法执行完毕之后，便会将栈帧出栈。
### 本地方法栈
为Native方法服务
### 堆
Java堆是被所有线程共享的一块内存区域，Java堆的唯一目的就是存放对象实例，几乎所有的对象实例都是在这里分配内存。

从内存回收的角度来看，堆内存可分为：新生代、年老代。

#### OutOfMemory内存溢出异常
年老代溢出：设置的内存参数过小或程序的内存泄露及使用不当问题。例如循环上万次的字符串处理、创建上千万的对象、一次申请上百M或几G内存。

持久代溢出：通常由于持久代过小，动态加载了大量java类而导致溢出
### 方法区
也是被所有线程共享的一块内存区域。它用于存储已经被虚拟机加载的类信息、常量、静态变量、即时编译器编译后的代码。
<h2>类的加载过程</h2>
类的加载过程主要分为三个部分：

* 加载
* 链接
* 初始化
其中链接又可分为：

* 验证
* 准备
* 解析

![图片](https://github.com/UMRhamster/Jobs2018/raw/master/wild_knowledge_point/img/classloader.png)

### 加载
在加载阶段虚拟机主要完成以下三件事：

1）通过类的全限定名获取该类的二进制字节流<br/>
2）将字节流所代表的静态存储结构转换成方法区的运行时结构<br/>
3）在堆中生成代表该类的java.lang.Class对象，作为方法区的数据访问入口。<br/>
### 验证
验证是链接阶段的第一步，目的是确保Class对象的字节流中包含的信息符合当前虚拟机的要求，并且不会危害虚拟机的自身安全。
### 准备
准备阶段就是正式为类变量分配内存并设置类变量的初始值的，这些变量所使用的内存都将在方法区分配。注意：是静态变量，不包括实例对象。
### 解析
解析阶段虚拟机将常量池的符号引用替换成直接引用的过程。
### 初始化
初始化阶段是真正开始执行类中定义的java程序代码。

<h2>双亲委派模型</h2>

![图片](https://github.com/UMRhamster/Jobs2018/raw/master/wild_knowledge_point/img/Parents_Delegation_Model.png)

上图的层次关系称为类加载器的双亲委派模型，每一层上面的类加载器叫做当前层类加载器的父加载器，当然它们之间的父子关系并不是用过继承来实现的，而是使用组合关系来复用父加载器中的代码。
### 双亲委派模型的工作流程
如果一个类加载器收到了类加载的请求，它首先不会自己去尝试加载这个类，而是把请求委托给父加载器去完成，一次向上，因此，所有的类加载请求最终都应该被传递到顶层的启动类加载器中，只有当父加载器在它的搜素范围中没有找到所需的类时，即无法完成该加载，子加载器才会尝试自己去加载该类。
### 双亲委派模型的优点
Java类伴随其类加载器具备了带有优先级的层次关系，确保了在各种加载环境的加载顺序。  

<h2>内部类与静态内部类</h2>
内部类是在一个类内部定义的类，相对的，包含内部类的类称为外部类。使用static关键字修饰的内部类，称为静态内部类，也只有这种情况下才能用static修饰类。

    public class Outer{
        //...

        //普通内部类
        public class InnerNormal{

        }
        //静态内部类
        public static class InnerStatic{

        }
    }

    public static void main(String[] args){
        //创建外部类实例
        Outer outer = new Outer();
        //必须通过外部类实例才能创建普通内部类实例
        InnerNormal innerNormal = outer.new InnerNormal();
        //通过
        Outer.InnerStatic innerStatic = new Outer.InnerStatic();
    }

普通内部类会持有外部类的引用

<h2>Java语法糖</h2>
几乎各种编程语言或多或少都会提供一些语法糖来方便程序员的代码开发，这些语法糖虽然不会提供实质性的功能改进，但是它们或能提高效率，或能提升语法的严谨性，或能减少编译出错的机会。

语法糖可以看作是编译器实现的一些"小把戏"，这些"小把戏"可能会使得效率"大提升"，但我们也应该去了解这些"小把戏"背后的真实世界，那样才能利用好它们，而不是被它们所迷惑。

### 泛型与类型擦除
泛型是JDK 1.5 的一项新特性，它们的本质是参数化类型（Parametersized Type）的应用，也就是说所操作的数据类型被指定为一个参数。这种参数类型可以用在类、接口和方法的创建中，分别称为泛型类、泛型接口、泛型方法。

泛型思想早在C++语言的模板（Template）中就开始生根发芽，在Java语言处于还没有出现泛型的版本时，只能通过Object是所有类型的父类和类型强制转换两个特点的配合来实现类型泛化。

泛型技术在C#和Java之中的使用方式看似相同，但实现上却有着根本性的分歧，C#里面泛型无论在程序源码中、编译后的IL中（Intermediate Language，中间语言，这时候泛型是一个占位符），或是运行期的CLR中，都是切实存在的，List\<Integer>与List\<String>就是两个不同的类型，它们在系统运行期生成，有自己的虚方法表和类型参数，这种实现称为类型膨胀，基于这种方法实现的泛型称为真实泛型。

Java语言中的泛型规则则不一样，它只在程序源码中存在，在编译后的字节码文件中，就已经替换为原来的原生类型（Raw Type，也称为裸类型）了，并且在相应的地方插入了强制类型代码，因此，对于运行期的Java语言来说，ArrayList\<Integer>和List\<String>就是同一个类，所以泛型技术实际上是Java的一颗语法糖，Java语言中的泛型实现方法称为类型擦除，基于这种方法实现的泛型称为伪泛型。

    public static void main(String[] args){
        Map<Integer,String> map = new HashMap<>();
        map.put(1,"你好");
    }

使用javac命令将这段代码编译成class文件，再通过[jd-gui](http://jd.benow.ca/)反编译工具进行反编译，将会发现泛型都不见了，程序又变回了Java泛型出现之前的写法，即如下。


    public static void main(String[] args){
        Map map = new HashMap();
        map.put(Integer.valueOf(1),"你好");
    }
[使用jd-gui反编译工具时可能遇到的问题：The application requires a java runtime environment 1.7.0](https://blog.csdn.net/k2514091675/article/details/76254729)

    public class Generic{
        public void method(List<Integer> list){}

        public void method(List<String> list){}
    }

由于Java中的泛型是伪泛型，参数List\<Integer>和List\<String>编译之后都被擦除了，变成了一样的原生类型List\<E>，擦除动作导致这两种方法的特征签名变得一模一样，因此无法通过编译。

    public class Generic{
        public Integer method(List<Integer> list){
            return 1;
        }

        public String method(List<String> list){
            return "";
        }
    }

然而这段代码却可以通过编译，首先注意这并不是方法重载，方法重载不是根据返回值来确定的。之所以能够编译成功的原因是，在class文件格式中，只要描述不是完全一致的两个方法就可以共存，也就是说，两个方法如果有相同的名称和特征签名，但返回值不同，那么它们也是可以合法地共存于一个class文件中的。

编译成功仅限于jdk1.6及以下，jdk1.7及以上是编译不通过的。

### 自动装箱与自动拆箱
    
    public class A {
       
        public void test(){
            Integer a = 1;
            Integer b = 2;
            Integer c = 3;
            Integer d = 3;
            Integer e = 321;
            Integer f = 321;
            Long g = 3L;
            System.out.println(c==d);
            System.out.println(e == f);
            System.out.println(c == (a+b));
            System.out.println(c.equals(a+b));
            System.out.println(g == (a+b));
            System.out.println(g.equals(a+b));
        }
    }

以上代码的输出结果为：

    true
    false
    true
    true
    true
    false

再来看一下经过反编译以后的代码，可以很明显的解释上述结果：

    public class A
    {

    public void test()
    {
        Integer localInteger1 = Integer.valueOf(1);
        Integer localInteger2 = Integer.valueOf(2);
        Integer localInteger3 = Integer.valueOf(3);
        Integer localInteger4 = Integer.valueOf(3);
        Integer localInteger5 = Integer.valueOf(321);
        Integer localInteger6 = Integer.valueOf(321);
        Long localLong = Long.valueOf(3L);
        System.out.println(localInteger3 == localInteger4);
        System.out.println(localInteger5 == localInteger6);
        System.out.println(localInteger3.intValue() == localInteger1.intValue() + localInteger2.intValue());
        System.out.println(localInteger3.equals(Integer.valueOf(localInteger1.intValue() + localInteger2.intValue())));
        System.out.println(localLong.longValue() == localInteger1.intValue() + localInteger2.intValue());
        System.out.println(localLong.equals(Integer.valueOf(localInteger1.intValue() + localInteger2.intValue())));
    }
    }

可以发现，通过 Integer integer = 1; 这种方式而非new的方式创建Integer对象，即为自动装箱，这是编译器代我们去完成的，当然我们也可以直接调用valueOf显示的去创建对象。

再来看一下Integer.valueOf(int i)的源码：

    public static Integer valueOf(int i) {
        if (i >= IntegerCache.low && i <= IntegerCache.high)
            return IntegerCache.cache[i + (-IntegerCache.low)];
        return new Integer(i);
    }
实际上在valueOf中调用了Integer的构造方法完成了对象创建的过程，Integer还包含一个Integer对象的缓存池，默认缓存了-128~127的Integer对象。当我们通过自动装箱创建的对象，并且其值在-128~127中时，所有值相同的对象都指向同一个实例，即缓存池中的对象。

Integer对象直接使用 == 符号进行比较时（符号两边都是对象），是比较的它们的地址，也就是它们是否引用同一个对象。

当一边是基本数据类型，而另一边是包装类型，时包装类型会自动拆箱成基本类型进行值的比较。同时，包装类型的任何运算都是拆箱成基本类型进行运算。

看一下包装类型重写的equals方法（这里只提供Integer的equlas方法，其他包装类型的equals方法类似）：

    public boolean equals(Object obj) {
        if (obj instanceof Integer) {
            return value == ((Integer)obj).intValue();
        }
        return false;
    }

首先通过 instanceof 关键字判断参数类型是否是对应的包装类型，只有当其类型一致时，再通过比较其值是否相等。

看到这里应该可以明白为什么是那样的输出结果了吧。

对于第一和第二行输出语句，比较的是它们的地址，由于c和d在-128~127范围内，并且都等于3所以它们引用的是缓存池中的同一对象。而e和f并在这个范围内，所以它们通过自动装箱创建的是不同的对象。

对于第三行，首先会计算a+b的值，前面说到过，涉及到算术运算时，包装类型 会自动拆箱为基本类型，因此 == 左边就是基本数据类型，同时左边也会进行自动拆箱为基本数据类型进行值的比较。

对于第四行，同样是先转换成基本类型计算a+b的值，但是equals方法需要包装类型参数，所以会对结果进行一个自动装箱。然后通过equals方法进行比较，如何比较上面已经说过了。

第五行和第三行类似，最终都是转换成值进行比较。

第六行与第四行是有区别的，由于a和b是Integer类型，它们先拆箱计算和然后在装箱成Integer对象，传入Long的equals方法进行比较，Integer instanceof Long 自然是 false, 所以返回false。

总的来说，对于包装类型，我们需要掌握的是什么是装箱，什么是拆箱，何时会自动装箱，何时会自动拆箱。装箱就是将基本数据类型包装成包装类型，拆箱就是将包装类型转换成基本数据类型。自动装箱与拆箱一般发生在对象创建与运算过程中。
