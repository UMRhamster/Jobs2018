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