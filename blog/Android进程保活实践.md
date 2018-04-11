

### 一、 ###
**微信公众号上推送的文字，地址：[Android进程保活实践](http://mp.weixin.qq.com/s?__biz=MzAxMTI4MTkwNQ==&mid=2650825271&idx=1&sn=2f71bc9a6a5f3b638cb561c777b713b3&chksm=80b7b6a9b7c03fbfc0b72341f1b0dd4e014acddbd49152914a5360a6f72cd7c8803e50021d68&mpshare=1&scene=1&srcid=0411A5RImGxPRihq4M17V5pw#rd)**

文章中提到进程优先级，有个对应数值和进程优先级关联，称作oom_adj，进程优先级越高数值越低。

至于怎么查看该数值，文字中给出了一种方式：先找到进程的pid,然后在proc/pid/oom_adj文件中查看具体的值。

>查看28321进程oom_adj的值

>adb shell

>cat proc/28321/oom_adj


<br/>



### 二、 ###

在学习`adb shell dumpsys meminfo`命令时,看到过oom adj

    
	//  查看帮助文档
	#adb shell dumpsys meminfo -h
	
	meminfo dump options: [-a] [-d] [-c] [-s] [--oom] [process]
	  -a: include all available information for each process.
	  -d: include dalvik details.
	  -c: dump in a compact machine-parseable representation.
	  -s: dump only summary of application memory usage.
	  -S: dump also SwapPss.
	  --oom: only show processes organized by oom adj.
	  --local: only collect details locally, don't call process.
	  --package: interpret process arg as package, dumping all
	             processes that have loaded that package.
	  --checkin: dump data for a checkin
	If [process] is specified it can be the name or 
	pid of a specific process to dump.


最后通过`adb shell dumpsys meminfo --oom [process]`命令就可以查看指定进程的优先级，显示的是优先级类型，而不是具体数值: 

>Native > System > Persistent > Foreground > Visible > Perceptible > Backup > A Services > B Services > Cached

>其中B Services表示进程比较老、使用可能性较小


<br/>

### 三、 ###
**常用内存类型**

- VSS - Virtual Set Size 虚拟耗用内存 （包含共享库占用的内存）
- RSS - Resident Set Size 实际使用的物理内存 （包含共享库占用的内存）
- PSS - Proportional Set Size  实际使用的物理内存（包含比例分配共享库占用的内存）
- USS - Unique Set Size 进程独自占用的物理内存（不包含共享库占用的内存）
一般来说内存占用大小规律如下：VSS >= RSS >= PSS >= USS