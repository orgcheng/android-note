## Jar重新打包
把当前目录下的文件打包到name.jar
`jar cvf name.jar ./*`

另外，打包程jar后，里面会生成一个文件，修改该文件可以指定jar的运行规则
[具体可以参考该链接](http://jingyan.baidu.com/article/ff42efa904b4d7c19e220282.html)
## logcat
查看使用帮助`adb logcat --help`
```
Usage: logcat [options] [filterspecs]
options include:
  -s              Set default filter to silent. Like specifying filterspec '*:S'

filterspecs are a series of 
  <tag>[:priority]

where <tag> is a log component tag (or * for all) and priority is:
  V    Verbose (default for <tag>)
  D    Debug (default for '*')
  I    Info
  W    Warn
  E    Error
  F    Fatal
  S    Silent (suppress all output)
```
需要注意的是
`adb logcat -s tag_name`等价与`adb logat *:S tag_name`
只输出TAG为tag_name的日志

`adb logat tag_name:S`
输出除TAG为tag_name以外的所有日志



## 屏幕录像和截屏(适用于api 19以上)
- 截屏
`adb shell screenrecord -p sdcard/demo.png`

- 录像
`adb shell`
`screenrecord sdcard/demo.mp4`
`Ctrl+C`停止

## dumpsys命令
- 查看手机崩溃的日志
`adb shell dumpsys dropbox system_app_crash --print`

另外dumpsys还有其他常用的参数,详细可查看帮助，例如`adb shell dumpsystem meminfo -h`
　- activity: 显示运行activity信息
　- cpuinfo: 显示CPU信息
　- meminfo:　显示内存信息
　- package: 显示package信息
　- battery/batteryinfo: 显示电池/使用信息
　- alarm:　显示alarm信息



## Ubuntu卡死解决方法
sudo kill -9  `ps -ef | grep tty7 | awk 'NR==1 {print $2}'`
或者
1.Ctrl+Alt+F1 进入 tty1
2.ps -t tty7 查看进程,找到Xorg进程的进程号例如1303
3.sudo kill 1303 杀死进程,接着会自动重启到登陆界面



## AAPT
AAPT是Android Asset Packagint Tool
按照上面环境变量的配置,把aapt添加到环境变量,可以使用`./aapt`查看帮助
常用`aapt`查看apk的版本信息和权限
- 查看版本
`./aapt d[ump] badging apk-path`
- 查看权限
`./aapt d[ump] permissions apk-path`
- 查看使用帮助
`./aapt`

另外`aapt`源代码位于`/frameworks/base/tools/aapt/`目录下面,可以修改源码来实现某些功能----公司内部提供的获取所有应用的权限列表apk, 保存到`sdcard`上的xls文件中,该功能是否可以修改这个工具来实现?

### Lint
在Android Studio的终端,输入`./gradlew lint`,会在每个Module的`/build/outputs/`下生成检测报告

#### ADB
**Android 4.3引入的wm工具**
- 获取Android设备屏幕分辨率： `adb shell wm size`
- 获取android设备屏幕密度： `adb shell wm density`

这两个功能是由代码提供的,类似`aapt`,可以修改相应代码来实现一些功能
[参考链接](http://www.cnblogs.com/fanfeng/p/3263853.html)

**获取手机型号和设备信息**
`adb shell`
`cat /system/build.prop | grep "product" #获取设备信息,如device=GIONEE_SWG1613`
