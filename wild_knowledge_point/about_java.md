<h2 id="1">1. Java中创建对象的5种方式</h2>

1. 使用new关键字
   
   最常见最简单的创建对象的方式。通过这种方式，可以调用任意构造函数（无参和带参）。

       Student student = new Student();   //无参构造函数
       Student student = new Student("张三");  //带参构造函数

2. 使用Class类的newInstance方法
   
   通过Class类的newInstanc方法调用无参构造函数创建对象。

       Student student = (Student) Class.forName("com.whut.umrhamster.Student").newInstance();

       Student student = Student.class.newInstance;

3. 使用Constructor类的newInstance方法
   
   和Class类的newInstance方法很像， java.lang.reflect.Constructor类里也有一个newInstance方法可以创建对象。我们可以通过这个newInstance方法调用有参数的和私有的构造函数。

   
       Constructor<Student> constructor = Student.class.getConstructor();
       Student student = constructor.newInstance();

   newInstance方法内部调用Constructor的newInstance方法。这也是众多框架，如Spring、Hibernate、Struts等使用后者的原因。

4. 使用clone方法

   当我们调用一个对象的clone方法，jvm就会创建一个新的对象，将前面对象的内容全部拷贝进去。用clone方法创建对象并不会调用任何构造函数。

   要使用clone方法，我们需要先实现Cloneable接口并实现其定义的clone方法。

       Student student = new Student("张三");
       Student studentClone = (Student) student.clone();

5. 使用反序列化
   
   当我们序列化和反序列化一个对象，jvm会给我们创建一个单独的对象。在反序列化时，jvm创建对象并不会调用任何构造函数。

   为了反序列化一个对象，我们需要让我们的类实现Serializable接口。

       ObjectInputStream in = new ObjectInputStream(new FileInputStream("test.txt"));
       Student student = (Student) in.readObject();

