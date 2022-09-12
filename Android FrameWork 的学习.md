# Android FrameWork 的学习

## 一、APK程序的运行过程



![image-20220803140438088](C:\Users\22859\AppData\Roaming\Typora\typora-user-images\image-20220803140438088.png)

 	**重点说明** ，ActivityThread是一个类，实例所在线程即为UI主线程，main方法就放在ActivityThread类里面，是安卓应用程序的入口。ActivityThread对象在创建之前调用的prepareMainLooper()方法会实例一个Looper对象，该Looper对象会创建一个消息队列，调用loop()方法后，UI线程会进入消息循环体，不断从消息队列中抽取消息并处理。ActivityThread的执行流程里面并没有主动创建一个Activity，而是通过创建一个ApplicationThread的Binder监听来自远方Ams的IPC调用，在收到创建Activity消息时才开始创建一个主Activity。创建Activity的具体流程如下：

![image-20220803141324439](C:\Users\22859\AppData\Roaming\Typora\typora-user-images\image-20220803141324439.png)

​	值得注意的是，W类继承Binder类，它负责接收来自Wms的IPC调用，并将消息发送至DectorView，如果DectorView没有处理，则传递给PhoneWindow，如果PhoneWindow没有处理，则继续传递给Activity，Activity通过Handler来处理此消息。

总结如下: 一个安卓应用程序在运行之初总共会创建三个线程：ActivityThread、ApplicationThread、W。其中ActivityThread为UI线程，通过绑定一个Looper不断抽取消息并处理，ApplicationThread与W均为Binder类，负责与远方服务器端通讯，ApplicationThread在主Activity创建之前创建，负责监听AmS传来的创建Activity消息，在Activity创建完毕后，W负责监听Wms发来的消息，并将此消息传给Activity