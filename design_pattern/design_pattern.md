<h1>一、设计模式遵循的六个原则</h1>

### 单一职责原则
单一职责原则指的是应该有且仅有一个原因引起类的变更。即一个类负责一项职责。
### 里氏替换原则
所有引用基类的地方必须能透明地使用其子类的对象，通俗的来讲就是父类能出现的地方子类就可以出现，但是反过来就不行了。子类可以扩展父类的功能，但不能改变父类原有的功能。
### 依赖倒置原则
高层模块不应该依赖低层模块，二者都应该依赖其抽象；抽象不应该依赖细节；细节应该依赖抽象。
### 接口隔离原则
客户端不应该依赖它不需要的接口；一个类对另一个类的依赖应该建立在最小的接口上。
### 迪米特法则
一个对象应该对其他对象有最少的了解，一个类只需要知道自己需要耦合或者调用类的public方法即可。
### 开闭原则
一个软件实体如类、模块和函数应该对扩展开放，对修改关闭。
<h1>java设计模式分类</h1>
java的设计模式大体上分为三类：

* 创建型模式（5种）：是对对象创建过程的各种问题和解决方案的总结。包括工厂方法模式，抽象工厂模式，单例模式，建造者模式，原型模式。
* 结构型模式（7种）：是针对软件设计结构的总结，关注于类、对象继承、组合方式的实践经验。包括适配器模式，装饰器模式，代理模式，外观模式，桥接模式，组合模式，享元模式。
* 行为型模式（11种）：是从类或对象之间交互、职责划分等角度总结的模式。包括策略模式、模板方法模式、观察者模式、迭代子模式、责任链模式、命令模式、备忘录模式、状态模式、访问者模式、中介者模式、解释器模式。
<h1>创建型模式</h1>

<h2>单例模式</h2>
所谓单例，指的就是单实例，有且仅有一个类实例。

常见单例模式有两种创建方式：懒汉式与饿汉式。

### 懒汉式
   
   顾名思义，就是在需要的时候才去创建对象，懒汉式不会在类加载时就创建对象，而是在第一次使用时才会创建对象。
       
       public class Singleton{
           //定义私有静态类变量，私有是让外部无法直接访问,静态是让其唯一
           private static Singleton instance = null;
           //定义私有构造方法，使其只能在类内部使用，即无法在外部通过new Singleton()创建对象
           private Singleton(){}
           //定义一个公共方法，用于返回该类实例给外部使用，在第一次使用时，才会创建类对象
           public static Singleton getInstance(){
               if(instance == null){
                   instance = new Singleton();
               }
               return instance;
           }
       }
### 饿汉式
   
   饿汉式则会在类加载的时候就创建对象。

       public class Singleton{
           //声明为 static ，在类加载的时候就会初始化创建类对象
           private static Singleton instance = new Singleton();
           private Singleton(){}
           public static Instance getInstance(){
               return instance;
           }
       }

通过懒汉式与饿汉式比较，可以发现懒汉式实现了懒加载，在第一次使用时创建对象，改善了初始的内存开销，但是在多线程情况下，会产生线程安全问题（如果多个线程同时进入 if(instance == null) 就会创建多个对象），可以通过synchronized关键字进行加锁，同一时间只允许一个线程访问。如下：

    public static synchronized Singleton getInstance(){  //对整个方法进行加锁
        //内部不变
        //...
    }
    或者
    public static Singleton getInstance(){
        synchronized(Singleton.class){  //对代码块进行加锁
            //内部不变
            //..
        }
    }

饿汉式由于提前实例化，就不会出现多线程下的线程安全问题。

[synchronized 关键字](https://github.com/UMRhamster/Jobs2018/blob/master/java/java_advanced.md#6.2)

但是无论是对方法进行加锁，还是对代码块进行加锁，我们都只是希望在newSingleton()的时候进行加锁，即只在第一次创建对象时防止线程安全问题。因此这种写法效率不是很高。

### 双重加锁机制
为了解决在懒汉式中的效率问题，使得只在第一次创建单例对象时，即instance等于null时才判断锁。

    public class Singleton{ 
        //volatile关键字提供可见性，以及保证getInstance返回的是初始化完全的对象
        private static volatile Singleton instance = null; 
        private Singleton(){}
        public static Singleton getInstance(){
            if(instance == null){ //在同步之前进行null判断，避免每次都同步加锁
                synchronized(Singleton.class){ //同一时间只允许一个线程进入
                    if(instance == null){  //再次进行null判断，因为有可能有多个线程同时都进入了第一次进行null判断，它们依次进入同步块，所以还需要再次进行判断，保证唯一单例。
                        instance == new Singleton();
                    }
                }
            }
            return instance;
        }
    }
[volatile 关键字](https://github.com/UMRhamster/Jobs2018/blob/master/java/java_advanced.md#6.3)

### 静态内部类的单例模式

    public class Singleton{
        private Singleton(){}
        public static Singleton getInstance{
            return Inner.singleton;
        }
        private static class Inner{
            private static Singleton instance = new Singleton();
        }
    }

加载外部类的时候，并不会同时加载其静态内部类，只有在发生调用时才会进行加载，加载时创建单例对象并返回，有效实现了懒加载；至于同步问题，对象初始化过程中隐含的初始化锁可以保证线程安全。

### 枚举类实现单例模式

    public enum Singleton {
        INSTANCE; //单例
        public void doSomething(){ //业务方法
            System.out.println("do something");
        }
    }
    //通过 Singleton.INSTANCE.doSomething()进行调用

枚举类的构造方法是私有的；每个枚举实例默认都是static final的，即只能被实例化一次。

### 静态成员与单例模式
* 单例更符合面向对象,静态成员破坏面向对象；
* 单例对象存储在堆内存，静态成员存储在方法区。

<h2>工厂模式</h2>

### 简单工厂模式

    //Fruit接口，并定义抽象方法用于返回水果名
    public interface Fruit{
        String getName();
    }

    //创建Apple类实现Fruit接口，并重写getName()方法
    public Apple implements Fruit{
        @Override
        public String getName(){
            return "Apple";
        } 
    }

    //Banana类实现Fruit接口，并重写getName()方法
    public Banana implements Fruit{
        @Override
        public String getName(){
            return "Banana";
        } 
    }

    //水果工厂类FruitFactory,用于创建并返回具体水果类
    public class FruitFactory{

        public static Fruit getFruit(String name){
            if("Apple".equals(name)){
                return new Apple();
            }else if("Banana".equals(name)){
                return new Banana();
            }else{
                return null;
            }
        }
    }

    public class Main{
        public static main(String[] args){
            Fruit fruit1 = FruitFactory.getFruit("Apple");
            Fruit fruit2 = FruitFactory.getFruit("Banana");

            fruit1.getName();  //Apple
            fruit2.getName();  //Banana
        }
    }

注意：以上class均属于不同java文件，一个java文件中只能存在一个pulic外部类。

* 简单工厂模式也叫静态工厂模式，工厂类一般使用静态方法，根据接收参数的不同返回不同的对象。
* 对于新的水果类加入，需要修改工厂类代码
* 不完全满足开闭原则

### 工厂方法模式

    //水果接口以及说水果具体实现类不作修改，这里不再重复
    //...

    //水果工厂接口
    public interface FruitFactory{
        Fruit getFruit();
    }

    //苹果工厂，专门用于获取苹果
    public interface AppleFactory implements FruitFactory{
        @Override
        public Fruit getFruit(){
            return new Apple();
        }
    }

    //香蕉工厂，专门用于获取香蕉
    public interface BananaFactory implements FruitFactory{
        @Override
        public Fruit getFruit(){
            return new Banana();
        }
    }

    public class Main{
        public static main(String[] args){
            Fruit fruit1 = new AppleFactory().getFruit();
            Fruit fruit2 = new BananaFactory().getFruit();

            fruit1.getName();  //Apple
            fruit2.getName();  //Banana
        }
    }
注意：以上class均属于不同java文件，一个java文件中只能存在一个pulic外部类。

* 工厂方法模式定义了一个创建对象的接口，但由子类决定要实例化哪一个。工厂方法让类把实例化推迟到了子类。
* 工厂方法模式更符合开闭原则，但是每次增加新的水果类，同时会增加新的对应工厂类。

### 抽象工厂模式
为创建一组相关或相互依赖的对象提供一个接口，而且无需指定他们的具体类。

如果除了水果，我们还需要甜点，按照工厂方法模式，除了甜点接口以及实现类，还需要增加以一个抽象工厂和多个具体实现工厂。随着产品的增多，类会越来越多。

    //水果接口以及说水果具体实现类不作修改，这里不再重复
    //...

    //水果工厂接口及具体实现工厂类也不作修改，不再重复
    //..

    //新增甜点
    public interface Dessert{
        String taste();
    }

    //甜点具体实现类，BlackForestCake
    public class BlackForestCake implements Dessert{

        @Override
        public String taste(){
            return "delicious";
        }
    }

    //甜点具体实现类，Tiramisu
    public class Tiramisu implements Dessert{

        @Override
        public String taste(){
            return "good";
        }
    }

    //修改后的工厂接口，既可以获得水果，也可以获得甜点
    public interface FoodFactory{
        Fruit getFruit();
        Dessert getDessert();
    }

    //第一个工厂，可以获得苹果和黑森林蛋糕
    public class FirstFoodFactory implements FoodFactory{

        @Override
        public Fruit getFruit(){
            return new Apple();  //如果要获得香蕉，这里修改为new Banana() 或新建工厂具体实现类
        }
        
        @Override
        public Dessert getDessert(){
            return new BlackForestCake();
        }
    }

    //第二个工厂，可以获得香蕉，但是无法获得甜点
    public class SecondFoodFactory implements FoodFactory{

        @Override
        public Fruit getFruit(){
            return new Banana();
        }
        
        @Override
        public Dessert getDessert(){
            return null;
        }
    }

    public class Main{
        public static main(String[] args){
            FirstFoodFactory firstFoodFactory = new FirstFoodFactory();
            SecondFoodFactory secondFoodFactory = new SecondFoodFactory();
            Fruit fruit1 = firstFoodFactory().getFruit();
            Dessert dessert1 = firstFoodFactory().getDessert();
            Fruit fruit2 = secondFoodFactory().getFruit();
            Dessert dessert2 = secondFoodFactory().getDessert();
            
            fruit1.getName();  //Apple
            dessert1.taste();  //delicious
            fruit2.getName();  //Banana
            //dessert2.taste();  //dessert2为null，具体使用时，需要根据情况进行相应判断  
        }
    }

注意：以上class均属于不同java文件，一个java文件中只能存在一个pulic外部类。

* 可以发现，工厂方法模式可以看作抽象工厂模式只有单一产品的情况。

<h1>结构型模式</h1>
<h2>代理模式</h2>
代理模式提供了对目标对象另外的访问方式；即通过代理对象访问目标对象.这样做的好处是：可以在目标对象实现的基础上，增强额外的功能操作，即扩展目标对象的功能。

例如，我们想查看的某个方法的执行过程:

    public void method(){
        System.ou.println("方法执行前");
        //中间方法体省略
        //...
        System.out.println("方法执行后");
    }
我们会通过以上代码对原本方法进行修改，但是这样是有问题的，违背了开闭原则，我们修改了原来的代码。

### 静态代理
静态代理在使用时,需要定义接口或者父类,被代理对象与代理对象一起实现相同的接口或者是继承相同父类。

    //定义一个接口
    public interface UserDao{
        //保存数据方法
        void save();
    }

    //定义一个具体类实现UserDao接口
    public class UserDaoImp implements UserDao{
        @Override
        public void save(){
            System.out.println("保存数据");  
        }
    }

    //定义代理类
    public class UserDaoImpProxy implements UserDao{
        private UserDao target; //目标对象
        public UserDaoImpProxy(UserDao target){
            this.target = target;
        }

        @Override
        public void save(){
            System.out.println("保存数据之前");
            target.save();//执行目标对象的方法
            System.out.println("保存数据之后");
        }
    }

    //
    public class Main{
        public static void main(String[] args){
            //目标对象
            UserDaoImp target = new UserDapImp();
            //代理对象，通过构造方法建立代理关系
            UserDaoImpProxy proxy = new UserDapImpProxy(target);
            //执行代理方法
            proxy.save();
        }
    }

* 静态代理可以做到在不修改目标对象功能的前提下，对目标功能进行增强；
* 如果接口增加了方法，则代理类也需要进行修改；因为每一个接口对象对应一个委托对象，如果委托对象非常多，代理类就会变得非常臃肿。

### 动态代理

使用动态代理，可以根据传进来的业务实现类，动态的创建代理类，同时避免静态代理类的问题

#### jdk代理
jdk动态代理只能对实现了接口的类生成代理，而不能针对类。

    public class ProxyFactory{
        private Object target;//目标对象
        public ProxyFactory(Object target){
            this.target = target;
        }

        public Object getProxyInstance(){
            return Proxy.newProxyInstance(
                target.getClass().getClassLoader(),
                target.getClass().getInterfaces(),
                new InvocationHandler(){
                    @Override
                    public Object invoke(Object proxy, Mehtod method, Object[] args) throws Throwable{
                        System.out.println("方法执行前");
                        //执行目标对象的方法
                        Object returnValue = method.invoke(target,args);
                        System.out.println("方法执行后");
                        return returnValue;
                    }
                }
            );
        }
    }

    public class Main{
        public static void main(String[] args){
            UserDao target = new UserDaoImp();  //目标对象
            UserDao proxy = (UserDao) new ProxyFactory(target).getProxyInstance();  //通过ProxyFactory动态创建代理对象
            proxy.save();  //执行代理方法
        }
    }

#### cglib代理
上面的静态代理和动态代理模式都是要求目标对象是实现一个接口的目标对象,但是有时候目标对象只是一个单独的对象,并没有实现任何的接口,这个时候就可以使用以目标对象子类的方式类实现代理,这种方法就叫做:Cglib代理。

使用cglib代理需要带入jar包，可在github上下载，以下实例基于 [cglib-nodep-3.2.7.jar](https://github.com/cglib/cglib/releases)。
注意下载 cglib-nodep 里面包含了需要用到的 asm 包，cglib 则不包含asm包，否则可能会出现版本不匹配问题。

    //首先定义一个类，它并没有实现接口
    public class UserDao{
        public void save(){
            System.out.println("保存数据");
        }
    }

    //
    public class ProxyFactory implements MethodInterceptor{
        private Object target;  //目标对象

        public ProxyFactory(Object target){
            this.target = target;
        }

        public Object getProxyInstance(){
            Enhancer enhancer = new Enhancer();  //加强器，用于创建代理对象
            enhancer.setSuperclass(target.getClass());  //指定父类
            enhancer.setCallback(this);  //设置回调，即下面的intercept()，代理类的方法调用都会调用回调方法
            return enhancer.create();  //创建代理对象
        }
        @Override
        public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
            System.out.println("方法执行前");
            Object returnValue = method.invoke(target,objects);
            System.out.println("方法执行后");
            return returnValue;
        }
    }

    public class Main{
        public static void main(String[] args){
            UserDao target = new UserDao();  //目标对象
            UserDao proxy = (UserDao) new ProxyFactory(target).getProxyInstance();  //通过ProxyFactory动态创建代理对象
            proxy.save();  //执行代理方法
        }
    }

总结：
* jdk动态代理只能对实现了接口的类生成代理，而不能针对类。
* cglib是针对类实现代理，主要是对指定的类生成一个子类，覆盖其中的方法（继承）

<h1>行为型模式</h1>
<h2>观察者模式</h2>
观察者模式，又可以称之为发布-订阅模式，观察者，顾名思义，就是一个监听者，类似监听器的存在，一旦被观察/监听的目标发生的情况，就会被监听者发现，这么想来目标发生情况到观察者知道情况，其实是由目标将情况发送到观察者的。

### 回调
是指通过函数参数传递到其它代码的，某一块可执行代码的引用。这一设计允许了底层代码调用在高层定义的子程序。

A类的a()方法调用B类的b()方法，B类的b()方法执行完之后主动调用A类的callback()方法，即回调方法。

下面使用代码模拟一个场景：老师向学生提问，学生通过此方法告诉老师答案。

    public interface Callback {
        void answer(String answer);
    }

首先是一个回调接口，只有一个方法answer()，就是学生将答案告诉老师

    public class Teacher implements Callback {
        public void ask(Student student){  //老师指定学生来回答问题
            System.out.println("老师问学生问题");
            student.solve(this);  //学生开始解决问题
        }
        @Override
        public void answer(String answer) {  //这是回调方法，由学生解决问题之后调用
            System.out.println("老师已经得到学生的答案："+answer);
        }
    }
定义一个老师类，实现回调接口，其中有两个方法，第一个方法就是Teacher对象调用Student对象的sovle()方法，即老师向学生提问，学生思考问题；第二个方法就是Callback接口中的方法，表示老师得到学生的答案之后要做的事。

    public class Student {
        public void solve(Callback callback){
            try {
                Thread.sleep(3000);  //延时3秒，模拟做题需要的时间
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("学生解决问题");  //学生解决问题
            callback.answer("mc^2");  //然后告诉老师答案
        }
    }
定义一个学生类，Student类的solve()方法表示学生思考问题，学生思考完毕之后，调用Teacher的回调方法，将答案告诉老师。

    public class Main{
        public static void main(String[] args) {
            Student student = new Student();  //创建学生对象
            Teacher teacher = new Teacher();  //创建老师对象
            teacher.ask(student);  //老师向学生提问
        }
    }
下面是程序的运行结果：

    老师问学生问题
    学生解决问题
    老师已经得到学生的答案：mc^2

### 观察者模式
定义对象间的一对多的依赖关系，当一个对象状态发生改变时，所有依赖他的对象都得到通知并被自动更新。

即A对象(观察者)对B对象(被观察者)的某种变化高度敏感，需要在B变化的一瞬间做出反应。当然，这是被动观察，不需要观察者时刻盯着检查被观察者的状态，而是在B变化的时候主动通知A。

    //被观察者接口
    public interface Subject{
        //注册观察者
        void registerObserver(Observer observer);
        //移除观察者
        void removeObserver(Observer ovserver);
        //通知观察者
        void notifyObservers();
    }

    //观察者接口
    public interface Observer{
        //观察者的回调方法，由被观察者进行回调
        void update();
    }

    //天气数据（被观察者）
    public class WeatherData implements Subject{
        private List<Observer> observers;

        private float temperature; //温度
        private float humidity; //湿度
        private float pressure; //气压

        public WeatherData(){
            observers = new ArrayList<>();
        }

        @Override
        public void registerObserver(Observer observer){
            observers.add(observer);

        }

        @Override
        public void removeObserver(Observer observer){
            observers.remove(observer);
        }

        @Override
        public void notifyObservers(){
            for(Observer observer : observers){
                observer.update();  //调用回调方法
            }
        }

        //天气数据改变时，会调用此方法通知所有观察者数据更新
        public void  weatherDataChanged(){
            notifyObservers();
        }

        //更新天气数据
        public void setWeatherData(float temperature, float humidity, float pressure){
            this.temperature = temperature;
            this.humidity = humidity;
            this.pressure = pressure;
            weatherDataChanged(); //调用weatherDataChanged()通知所有观察者
        }

        public float getTemperature(){
            return temperature;
        }

        public float getHumidity(){
            return humidity;
        }

        public float getPressure(){
            return pressure;
        }
    }

    //天气网站（观察者）
    public WeatherWebsite implements Observer{
        private WeatherData weatherData;

        private float temperature;
        private float humidity;
        private float pressure;
        public WeatherWebsite(WeatherData weatherData){
            this.weatherData = weatherData;
        }

        //此处实现update()回调方法的具体实现细节
        @Override
        public void update() { 
            temperature = weatherData.getTemperature();
            humidity = weatherData.getHumidity();
            pressure = weatherData.getPressure();
            display();  //进行天气数据显示
        }

        //显示当前天气数据
        public void display(){
            System.out.println("天气网站当前温度为："+temperature);
            System.out.println("天气网站当前湿度为："+humidity);
            System.out.println("天气网站当前气压为："+pressure);
        }
    }

    //天气应用（观察者）
    public class WeatherApp implements Observer{
        private WeatherData weatherData;

        private float temperature;
        private float humidity;
        private float pressure;
        public WeatherApp(WeatherData weatherData){
            this.weatherData = weatherData;
        }

        //此处实现update()回调方法的具体实现细节
        @Override
        public void update() {
            temperature = weatherData.getTemperature();
            humidity = weatherData.getHumidity();
            pressure = weatherData.getPressure();
            display();  //进行天气数据显示
        }

        //显示当前天气数据
        public void display(){
            System.out.println("天气应用当前温度为："+temperature);
            System.out.println("天气应用当前湿度为："+humidity);
            System.out.println("天气应用当前气压为："+pressure);
        }
    }

    public class Main{
        public static void main(String[] args){
            WeatherData weatherData = new WeatherData(); //创建天气数据对象
            WeatherWebsite weatherWebsite = new WeatherWebsite(weatherData);  //创建天气网站对象
            weatherData.registerObserver(weatherWebsite); //添加观察者
            weatherData.setWeatherData(38,10,10); //更新天气数据
            WeatherApp weatherApp = new WeatherApp(weatherData); //创建天气应用对象
            weatherData.registerObserver(weatherApp); //添加观察者
            weatherData.setWeatherData(11,11,11);
            weatherData.removeObserver(weatherWebsite); //移除观察者
            weatherData.setWeatherData(9,9,9);
        }
    }

以上代码执行结果：

    天气网站当前温度为：38.0
    天气网站当前湿度为：10.0
    天气网站当前气压为：10.0  //这是第一次更新天气数据，只有天气网站一个观察者

    天气网站当前温度为：11.0
    天气网站当前湿度为：11.0
    天气网站当前气压为：11.0  //这是第二次更新天气数据，这是天气网站观察者

    天气应用当前温度为：11.0
    天气应用当前湿度为：11.0
    天气应用当前气压为：11.0  //这是第二次更新天气数据，这是后来的观察者天气应用

    天气应用当前温度为：9.0
    天气应用当前湿度为：9.0
    天气应用当前气压为：9.0  //这是第三次更新天气数据，此时天气网站不再依赖此天气数据

上述代码，描述了气象台与天气网站、天气应用之间的关系。天气网站与天气应用依赖于气象台的天气数据。每当天气数据变化更新时，气象台主动通知天气网站与天气应用。




