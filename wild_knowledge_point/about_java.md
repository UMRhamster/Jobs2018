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
