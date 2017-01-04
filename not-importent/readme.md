## 命令
- 屏幕录像(适用于api 19以上)
`screenrecord storage/emulated/0/demo.mp4`
`screenrecord sdcard/demo.mp4`
`Ctrl+C`停止

- 查看手机崩溃的日志
`adb shell dumpsys dropbox system_app_crash --print`


- Ubuntu卡死解决方法
sudo kill -9  `ps -ef | grep tty7 | awk 'NR==1 {print $2}'`
或者
1.Ctrl+Alt+F1 进入 tty1
2.ps -t tty7 查看进程,找到Xorg进程的进程号例如1303
3.sudo kill 1303 杀死进程,接着会自动重启到登陆界面

## 快捷键
A small sampling of Android Studio Live Templates
![](./img/TemplateCodeitWillInsert.png)