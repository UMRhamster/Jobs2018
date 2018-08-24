>参考《疯狂Android讲义（第3版）》
<h1 id="1">一、概述</h1>
<h2 id="1.1">Android的发展和简介</h2>
Android是由Andy Rubin 创立的一个手机操作系统，后来被Google收购。Google希望与各方共同建立一个标准化、开放式的移动电话软件平台，从而在移动产业内形成一个开放式的操作平台。

Android系统已经称为全球应用最广泛的手机操作系统。
<h2 id ="1.2">Android平台架构及特性</h2>
Android系统的底层是建立在Linux系统之上，该平台由操作系统、中间件、用户界面和应用软件4层组成，它采用一种被称为软件层叠的方式进行构建。这种软件层叠结构使得层与层之间相互分离，明确各层的分工。这种分工保证了层与层之间的低耦合，当下层的层内或层下发生改变时，上层应用程序无须任何改变。

![图片](https://github.com/UMRhamster/Jobs2018/raw/master/android/img/android_system_architecture.png)

1. 应用程序层
   
   Android系统将会包含一些列的核心应用程序，包括电子邮件客户端、SMS程序、日历、地图、浏览器、联系人等。这些应用程序通常都是用Java编写的。
2. 应用程序框架

   Android应用程序框架提供了大量API供开发者使用。应用程序框架除了可作为应用程序开发的基础之外，也是软件复用的重要手段，任何一个应用程序都可发布它的功能模块——只要发布时遵守了框架的约定，那么其他应用程序就可以使用这个功能模块。
3. 函数库

   Android包含一套被不同组件所使用的C/C++库的集合。一般来说，Android应用开发者不能直接调用这套C/C++库集，但可以通过它上面的应用程序框架来调用这些库。

4. Android运行时

   Android运行时由两部分组成：Android核心库集和ART。其中核心库集提供了Java语言核心库所能使用的绝大部分功能，而虚拟机则负责运行Android应用程序。

5. Linux内核

   Android系统建立在Linux 2.6之上。Linux内核提供了安全性、内存管理、进程管理、网络协议和驱动模型等核心系统服务。除此之外，Linux内核也是系统硬件和软件叠层之间的抽象层。

<h2 id="1.3">Android应用的清单文件：AndroidManifest.xml</h2>
AndroidManifest.xml 清单文件是每个Android项目所必须的，它是整个Android应用的全局描述文件。AndroidManifest.xml清单文件说明了该应用的名称、所使用的图标以及包含的细节等。

AndroidMainifest.xml清单文件通常可以包含一下信息：

* 应用程序的包名；
* 应用程序包含的组件，如Acticity、Service、BroadcastReceiver和ContentProvider等；
* 应用程序兼容的最低版本；
* 应用程序使用系统所需的权限声明；
* 其他程序访问该程序所需要的权限声明。

### 应用程序权限说明
1. 声明运行该应用本身所需要的权限

   在<manifest.../>元素中添加<uses-permission.../>即可为程序本身声明权限。

       //声明该应用本身需要的网络权限
       <uses-permission android:name="android.permission.INTERNET">
2. 声明调用该应用所需要的权限

<h2>Android应用的基本组件介绍</h2>

### Activity
Activity是Android应用中负责与用户交互的组件——大致上可以把它想象成Swing编程中的JFrame控件

Activity为Android应用提供了可视化用户界面，如果该Android应用需要多个用户界面，那么这个Android应用将会包含多个Activity，多个Activity组成Activity栈，当前活动的Activity位于栈顶。
### Service
Service与Activity的地位是并列的，它也代表一个单独的Android组件。Service与Activity的区别在于：Service通常位于后台运行，它一般不需要与用户交互，因此Service组件没有图形用户界面。
### BroadcastReceiver
BroadcastReceiver是Android应用中另一个重要组件，它代表广播消息接收器。BroadcastReceiver类似于事件编程中的监听器。但是普通时间监听器监听的事件源是程序中的对象；而BroadcastReceiver监听的事件源是Android应用中的其他组件。
### ContentProvider
对于Android应用而言，它们必须是相互独立，各自运行在各自的进程中。Android系统为跨应用数据交换提供了一个标准：ContentProvider。通常与ContentProvider结合使用的是ContentResolver,一个应用程序使用ContentProvider暴露自己的数据，而另一个应用程序则通过ContentProvider来访问数据。
### Intent
Intent并不是Android应用的组件，但它对于Android应用的作用非常强大——它是Android应用内不同组件之间通信的载体。Activity、Service、BroadcastReceiver三种组件之间的通信都是以Intent作为载体。
<h2>Android的事件处理</h2>
当用户在程序界面上执行各种操作时，应用程序必须为用户动作提供响应动作，这种响应动作就需要通过事件处理来完成。

Android提供了两种方式的事件处理：基于回调的事件处理和基于监听的事件处理。

### 基于监听的事件处理
在时间监听的处理模型中，主要涉及如下三类对象：

Event Source(事件源)：事件发生的场所，通常就是各个组件，例如按钮、窗口、菜单等。<br/>
Event(时间)：事件封装了界面组件上发生的特定事情（通常是一次用户操作）。<br/>
Event Listener(时间监听器)：负责监听事件源所发生的事件，并对各种时间做出相应的响应。

Android的事件处理机制是一种委派式（Delegation）事件处理方式：普通组件（事件源）将整个事件处理委托给特定的对象（事件监听器）；当该时间源发生指定的事件时，就通知所委托的事件监听器，由事件监听器来处理这个事件。

Android中基于监听的时间处理模型的编程步骤如下：
1. 获取普通界面组建（事件源），也就是被监听的对象。
2. 实现事件监听器类，该监听器类是一个特殊的Java类，必须实现一个XxxListener接口。
3. 调用事件源的setXxxListener方法将时间监听器对象注册给普通组件（事件源）。
   
当事件源上发生指定事件时，Android会触发事件监听器，由事件监听器调用相应的方法（事件处理器）来处理事件。

    Button button = findViewById(R.id.btn);
    button.setOnClickListener(new View.onClickListener(){ //此处通过匿名内部类方式实现事件监听器类
        @Override
        public void onClick(View view) {
            //事件处理
            //Log.d("MainActivity","点击了按钮");
        }
    });

所谓事件监听器，其实就是实现了特定接口的Java类的实例。在程序中实现事件监听器，通常有如下几种形式：
* 内部类形式：将事件监听器类定义成当前类的内部类。
* 外部类形式：将事件监听器类定义成一个外部类。
* Activity本身作为事件监听器类：让Activity本身实现监听器接口，并实现事件处理方法。
* 匿名内部类形式：使用匿名内部类创建事件监听器对象。

当然，还有一种直接在界面布局文件中为指定标签绑定事件处理方法。在xml文件中为控件指定onClick属性，其实就是一个方法名。
 
    <Button
        android:id="@+id/btn"
        android:layout_width="wrap_content"
        android:layout_weight="wrap_content"
        android:onClick="click"/>

然后在对应Activity中定义一个click(View view)方法。

    public class MainActivity extends AppCompatActivity {
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
        }

        //对应布局文件中onclick属性，注意public修饰，返回值为void，形参为View对象
        public void click(View view){
            Log.d("MainActivity","点击了按钮");
        }
    }

### 基于回调的事件处理
基于回调的事件处理模型中，事件源与时间监听器是统一的，或者说事件监听器完全消失了。当用户在GUI组件上激发某个事件时，组件自己特定的方法会负责处理该事件。

为了实现回调机制的事件处理，Android为所有GUI组件都提供了一些事件处理的回调方法。

回调机制可通过自定义View来实现，自定义View时重写该View的事件处理方法即可。

    public class MyButton extends Button{
        public MyButton(Context context, AttributeSet set){
            super(context,set);
        }
        @Override
        public boolean onKeyDown(int ketCode, KeyEvent event){
            super.onKeyDown(keyCode,event);
            Log.d("test","onKeyDown in MyButton");
            return true;
        }
    }
在xml文件中使用MyButton组件，在Activity中就不需要绑定事件监听器，因为该按钮自己重写了onKeyDown(int keyCode, KeyEvent event)方法，意味着该按钮将会自己处理相应的事件。

#### 基于回调的事件传播
几乎所有基于回调的时间处理方法都有一个boolean类型的返回值，该返回值用于标识该处理方法是否能完全处理该事件。
* 如果处理事件的回调方法返回true，表明该处理方法已完全处理该事件，该事件不会传播出去。
* 如果处理事件的回调方法返回false，表明该处理方法并未完全处理该事件，该事件会传播出去。

<h2>Handler消息传递机制</h2>
处于性能的考虑，Android的UI操作并不是线程安全的，这意味者如果有多个线程并发操作UI组件，则可能导致线程安全问题。因此，只允许UI线程修改Activity里的UI组件。

当程序第一次启动时，Android会同时启动一条主线程，主线程主要负责与UI相关的事件。主线程通常又被称为UI线程。

Adnroid的消息传递机制主要主要就是为了解决Android应用的多线程问题——Android平台只允许UI线程修改Activity里的UI组件，这样就会导致新启动的线程无法动态改变界面组件的属性值。通过Handler的消息传递机制，我们可以在子线程中修改界面组件的属性值。

### Handler类
Handler类的主要作用有两个
* 在新启动的线程中发送消息。
* 在主线程中获取、处理消息。

Handler类包含如下方法用于发送、处理消息：

void handleMessage(Message msg)：处理消息的方法。该方法通常用于被重写。<br/>
final boolean hasMessage(int what)：检查消息队列中是否包含what属性为指定值的消息。<br/>
final boolean hasMessage(int what, Object object)：检查消息队列中是否包含what属性为指定值且object属性为指定对象的消息。<br/>
多个重载的Message(int what)：获取消息。<br/>
sendEmptyMessage(int what)：发送空消息。<br/>
final boolean sendEmptyMessageDelayed(int what, long delayMilis)：指定多少毫秒之后发送空消息。<br/>
final boolean sendMessage(Message msg)：立即发送消息。<br/>
final boolean sendMessageDelayed(Message msg, long delayMilis)：指定多少毫秒之后发送消息。

### Handler、Looper、MessageQueue的工作原理
* Message：Handler接收和处理的消息对象。
* Looper：每个线程只能拥有一个Looper。它的loop方法负责读取MessageQueue中的消息，读到信息之后就把消息交给发送该消息的Handler进行处理。
* MessageQueue：消息队列，采用先进先出的方式管理Message。程序创建Looper对象时，会在它的构造器中创建MessageQueue对象。
* Handler：它能把消息发送给Looper管理的MessageQueue，并负责处理Looper分给它的消息。

在UI线程中，系统已经初始化了一个Looper对象，因此直接创建Handler即可。在新建子线程中，必须自己创建一个Looper对象，并启动它。通过调用Looper的静态方法prepare() 创建Looper对象，在调用它的静态方法loop()方法来启动它。loop()方法使用一个死循环不断取出Messagequeue中的消息，并将取出的消息分给该消息对应的Handker进行处理。

<h2>异步任务</h2>
Android的UI线程主要负责处理用户的按键事件、用户触屏事件及屏幕绘图事件等，因此我们不应该在UI线程中进行耗时的操作，否则可能会导致ANR（Application Not Responding）异常。应当将耗时操作放在子线程中去完成，但是子线程也可能需要动态更新UI，而Android又不允许子线程直接更新UI。

为了解决新线程不能更新UI组件的问题，Android提供了如下几种解决方案。
* 使用Handler实现线程之间的通信。
* Activity.runOnUiThread(Runnable)
* View.post(Runnable)
* View.postDelayed(Runnable,long)
  
 使用异步任务（Async Task）可以简化子线程更新UI操作。Async更轻量级一些，适用于简单的异步处理，不需要借助线程和Handler即可实现。

 AsyncTask<Params,Progress,Result>是一个抽象类，通常用于被继承，继承AsyncTask时需要指定如下三个泛型参数。
* Params：启动任务执行的输入参数的类型
* Progress：后台任务完成后返回结果的类型。
* Result：后台执行任务完成后返回结果类型

使用AsyncTask如下

1. 创建AsyncTask的子类，并为三个泛型参数指定类型，如果某个泛型参数不需要指定类型，则可以将它指定为Void。

2. 根据需要，实现一下方法
   * doInBackground(Params...)：重写该方法就是后台线程将要完成的任务。该方法可以调用publishProgress(Progress...values)方法更新人物的执行进度。
   * onProgressUpdate(Progress...values)：publishProgress方法会触发该方法。
   * onPreExecute()：该方法将在执行后台耗时操作前被调用。通常用于完成一些初始化准备工作。
   * onPostExecute(Result result)：当doInBackground()完成后，会自动调用此方法，并将返回值传给此方法。
3. 调用AsyncTask子类的实例的execute(Params...params)开始执行耗时任务。

必须在UI线程中创建AsyncTask实例和调用execute()方法，并且只能被执行一次，多次调用会引发异常。

<h1>Activity</h1>
<h2>Activity生命周期</h2>

![图片](https://github.com/UMRhamster/Jobs2018/raw/master/android/img/activity_lifecycle.png)

onCreate()：创建Activity时被回调。该方法只会被调用一次。<br/>
onStart()：启动Activity时被回调。<br/>
onRestart()：重新启动activity时被回调。<br/>
onResume()：恢复Activity时被回调。在onStart()方法后一定会回调onResume()方法。<br/>
onPause()：暂停Activity时被回调。<br/>
onStop()：停止Activity时被回调。<br/>
onDestroy()：销毁Activity时被回调。该方法只会被调用一次。<br/>

<h2>Activity的4中加载模式</h2>

1. standard：标准模式，默认的加载模式。
2. singleTop： Task栈顶单例模式。
3. singleTask：Task栈内单例模式。
4. singleInstance：全局单例模式。

Android使用Task来管理多个Activity当我们启动一个应用时，Android就会为只创建一个Task，然后启动入口Activity。Task以栈的形式来管理Activity：先启动的ctivity被放在Task栈底，后启动的Activity被放在Task栈顶。

Activity的加载模式，就负责管理实例化、加载Activity的方式，并可以控制Activity与Task之间的加载关系。
### standard模式
在这种模式下，每次启动Activity都会创建一个新的实例，并将该Activity添加到当前Task栈中——这种模式不会启动新的Task，新Activity将被添加到原有的Task中。
### singleTop模式
当要启动的Activity已经处于Task栈顶时，直接复用已有的Activity，否则创建新的实例。
### singleTask模式
在这种模式下，Activity在同一个Task内只有一个实例。如果要启动的Activity不存在，则创建Activity实例，加入Task栈；如果要启动的Activity已经位于Task栈顶，直接复用已有的Activity；如果要启动的Activity已经存在但不处于栈顶，则将该Activity之上的所有Activity出栈，从而使该Activity位于栈顶。
### singleInstance模式
在这种模式下，无论从哪个Task中启动目标Activity，都只会创建一个Activity实例，并使用一个全新的Task栈来加载该Activity实例。

