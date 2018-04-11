### 一、Binder的由来 ###
Android是基于Linux的系统，所以进程之间是隔离的。

进程中的内存空间划分为两部分：用户空间和内核空间， 其中用户空间是进程间非共享的，内核空间是非进程间。

Linux原来的进程间通信机制有信号、管道、socket等，考虑安全和性能方面不太适合手机，所以产生了Binder这种技术。

<br/>
### 二、几个关键词 ###
- system_server进程：它是系统进程，系统服务都运行在这个进程
- servicemanager进程： 它是负责添加服务，获取服务，查找服务

另外system_server进程是有zygote进程启动的， zygote进程是由init进程启动的。

>查看系统服务：`adb shell service list`
>
>查看进程：`adb shell top`

<br/>
### 三、通过AIDL来理解进程间通信 ###

服务端`MyService`向`ServiceManager`注册服务

客户端`MyClient`通过`ServiceMananger`查询服务，并获取服务的引用

如果客户端和服务器端在同一个进程，返回的引用对象就是服务本身；

如果客户端和服务器端不在一个进程，返回的引用对象就是服务的代理对象；代理对象调用服务端提供的方法后，通过`transact`方法传递数据，经过Binder驱动后，由真是服务对象的`onTransact`方法处理并返回结果。

