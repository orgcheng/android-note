## adb shell top　命令

示例：
```
orgcheng@orgcheng:~$ adb shell top

User 8%, System 12%, IOW 0%, IRQ 0%  // CPU占用率 
User 30 + Nice 0 + Sys 45 + Idle 280 + IOW 0 + IRQ 0 + SIRQ 0 = 355  // CPU使用情况 

  PID PR CPU% S  #THR     VSS     RSS PCY UID      Name  // 进程属性 
 1474  0   4% S    81 1746496K 146708K  fg u0_a3    com.android.systemui
  298  0   4% S    31 1374256K  47336K  fg system   /system/bin/surfaceflinger
 1263  0   3% S   133 3113268K 142616K  fg system   system_server

```

**１.CPU占用率： **


| 字段 | 功能 |
|--------|--------|
|user|用户进程|
|System|系统进程|
|IOW|IO等待时间|
|IRQ|系统进程|
|System|硬中断时间|


**２.CPU使用情况：**（指一个最小时间片内所占时间，单位jiffies。或者指所占进程数）：

| 字段 | 功能 |
|--------|--------|
|User|处于用户态的运行时间，不包含优先值为负进程|
|Nice|优先值为负的进程所占用的CPU时间|
|Sys|处于核心态的运行时间|
|Idle|除IO等待时间以外的其它等待时间|
|IOW|IO等待时间|
|IRQ|硬中断时间|
|SIRQ|软中断时间|

**３.进程属性：**

| 字段 | 功能 |
|--------|--------|
|PID|进程在系统中的ID|
|CPU%|当前瞬时所以使用CPU占用率|
|S|进程的状态，其中S表示休眠，R表示正在运行，Z表示僵死状态，N表示该进程优先值是负数。|
|#THR|程序当前所用的线程数|
|VSS|Virtual Set Size 虚拟耗用内存（包含共享库占用的内存）|
|RSS|Resident Set Size 实际使用物理内存（包含共享库占用的内存）|
|PCY|OOXX，不知道什么东东|
|UID|运行当前进程的用户id|
|Name|程序名称|



    VSS - Virtual Set Size 虚拟耗用内存（包含共享库占用的内存）
    RSS - Resident Set Size 实际使用物理内存（包含共享库占用的内存）
    PSS - Proportional Set Size 实际使用的物理内存（比例分配共享库占用的内存）
    USS - Unique Set Size 进程独自占用的物理内存（不包含共享库占用的内存）

一般来说内存占用大小有如下规律：VSS >= RSS >= PSS >= USS


**4.内存耗用：VSS/RSS/PSS/USS**

**Overview**

The aim of this post is to provide information that will assist in interpreting memory reports from various tools so the true memory usage for Linux processes and the system can be determined.

Android has a tool called procrank (/system/xbin/procrank), which lists out the memory usage of Linux processes in order from highest to lowest usage. The sizes reported per process are VSS, RSS, PSS, and USS.

For the sake of simplicity in this description, memory will be expressed in terms of pages, rather than bytes. Linux systems like ours manage memory in 4096 byte pages at the lowest level.

**VSS** (reported as VSZ from ps) is the total accessible address space of a process. This size also includes memory that may not be resident in RAM like mallocs that have been allocated but not written to. **VSS is of very little use for determing real memory usage of a process.**

**RSS** is the total memory actually held in RAM for a process. RSS can be misleading, because it reports the total all of the shared libraries that the process uses, even though a shared library is only loaded into memory once regardless of how many processes use it. **RSS is not an accurate representation of the memory usage for a single process**.

**PSS** differs from RSS in that it reports the proportional size of its shared libraries, i.e. if three processes all use a shared library that has 30 pages, that library will only contribute 10 pages to the PSS that is reported for each of the three processes. PSS is a very useful number because when the PSS for all processes in the system are summed together, that is a good representation for the total memory usage in the system. When a process is killed, the shared libraries that contributed to its PSS will be proportionally distributed to the PSS totals for the remaining processes still using that library. In this way PSS can be slightly misleading, because when a process is killed, **PSS does not accurately represent the memory returned to the overall system.**

**USS** is the total private memory for a process, i.e. that memory that is completely unique to that process. **USS is an extremely useful number because it indicates the true incremental cost of running a particular process.** When a process is killed, the USS is the total memory that is actually returned to the system. USS is the best number to watch when initially suspicious of memory leaks in a process.

For systems that have Python available, there is also a nice tool called smem that will report memory statistics including all of these categories.

`# procrank`
```
procrank
PID      Vss      Rss      Pss      Uss cmdline
481   31536K   30936K   14337K    9956K system_server
475   26128K   26128K   10046K    5992K zygote
526   25108K   25108K    9225K    5384K android.process.acore
523   22388K   22388K    7166K    3432K com.android.phone
574   21632K   21632K    6109K    2468K com.android.settings
521   20816K   20816K    6050K    2776K jp.co.omronsoft.openwnn
474    3304K    3304K    1097K     624K /system/bin/mediaserver
37     304K     304K     289K     288K /sbin/adbd
29     720K     720K ****    261K     212K /system/bin/rild
601     412K     412K     225K     216K procrank
   1     204K     204K     185K     184K /init
35     388K     388K     182K     172K /system/bin/qemud
284     384K     384K     160K     148K top
27     376K     376K     148K     136K /system/bin/vold
261     332K     332K     123K     112K logcat
33     396K     396K     105K      80K /system/bin/keystore
32     316K     316K     100K      88K /system/bin/installd
269     328K     328K      95K      72K /system/bin/sh
26     280K     280K      93K      84K /system/bin/servicemanager
45     304K     304K      91K      80K /system/bin/qemu-props
34     324K     324K      91K      68K /system/bin/sh
260     324K     324K      91K      68K /system/bin/sh
600     324K     324K      91K      68K /system/bin/sh
25     308K     308K      88K      68K /system/bin/sh
28     232K     232K      67K      60K /system/bin/debuggerd
```
