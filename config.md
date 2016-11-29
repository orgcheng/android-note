#### Ubuntu环境变量的配置
1. 修改用户环境变量
用户环境变量通常被存储在下面的文件中
    - ~/.bashrc
    - ~/.profile
    - ~/.bash_profile or ~/.bash_login

2. 修改系统环境变量
系统环境变量通常被存储在下面的文件中
    - /etc/profile
    - /etc/environment
    - /etc/bash.bashrc

3. 加入环境变量
我一般都是修改系统环境变量`/etc/profile`
`export PATH="$PATH:/my_new_path"`
这样下次用户登陆就会生效,如果向立刻生效使,用下面的命令
`$source /etc/profile`

#### AAPT
AAPT是Android Asset Packagint Tool
按照上面环境变量的配置,把aapt添加到环境变量,可以使用`./aapt`查看帮助
常用`aapt`查看apk的版本信息和权限
- 查看版本
`./aapt d[ump] badging apk-path`
- 查看权限
`./aapt d[ump] permissions apk-path`

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