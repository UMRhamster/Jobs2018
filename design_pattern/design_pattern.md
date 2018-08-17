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

1. 懒汉式
   
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
2. 饿汉式
   
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



