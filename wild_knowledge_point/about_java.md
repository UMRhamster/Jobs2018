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




