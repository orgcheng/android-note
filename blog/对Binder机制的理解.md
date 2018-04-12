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
**下面的内容参考:[SystemServer vs ServiceManager](https://blog.csdn.net/eliot_shao/article/details/51514045)**

        # ps
        USER     PID   PPID  VSIZE  RSS     WCHAN    PC         NAME
        root      1     0     296    204   c009a694 0000c93c S /init 
        root      2     0     0      0     c004dea0 00000000 S kthreadd
        root      25    1     728    316   c003d444 afe0d6ac S /system/bin/sh
        system    26    1     796    256   c019a810 afe0ca7c S /system/bin/servicemanager 
        root      30    1     82860  26580 c009a694 afe0cba4 S zygote 
        media     31    1     20944  3184  ffffffff afe0ca7c S /system/bin/mediaserver 
        root      32    1     784    280   c0209468 afe0c7dc S /system/bin/installd
        keystore  33    1     1616   396   c01a65a4 afe0d40c S /system/bin/keystore
        root      34    1     728    272   c003d444 afe0d6ac S /system/bin/sh
        root      35    1     824    332   c00b7dd0 afe0d7fc S /system/bin/qemud
        root      37    1     1308   152   ffffffff 0000eca4 S /sbin/adbd
        root      44    34    780    304   c0209468 afe0c7dc S /system/bin/qemu-props
        system    52    30    158356 37804 ffffffff afe0ca7c S system_server 
        app_1     92    30    108640 20580 ffffffff afe0da04 S com.Android.inputmethod.pinyin
        radio     93    30    122852 23340 ffffffff afe0da04 S com.Android.phone
        app_1     98    30    143244 34888 ffffffff afe0da04 S Android.process.acore

我们注意观察进程列表的PID和PPID，我们要通过实际的列表去理清他们的亲缘关系。

servicemanager是init的子进程

mediaserver是init的子进程

zygote是init的子进程，管理所有虚拟机实例

system_server和所有的java应用程序是zygote的子进程。system_server负责管理系统服务。

<br/>
### 三、通过AIDL来理解进程间通信 ###

服务端`MyService`向`ServiceManager`注册服务

客户端`MyClient`通过`ServiceMananger`查询服务，并获取服务的引用

如果客户端和服务器端在同一个进程，返回的引用对象就是服务本身；

如果客户端和服务器端不在一个进程，返回的引用对象就是服务的代理对象；代理对象调用服务端提供的方法后，通过`transact`方法传递数据，经过Binder驱动后，由真是服务对象的`onTransact`方法处理并返回结果。

