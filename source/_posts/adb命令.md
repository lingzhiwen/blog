---
title: adb命令
---

截屏

```
adb shell screencap -p /sdcard/screenhot.png

```



录屏

```
adb shell screenrecord --size 1920x1080 /sdcard/record.mp4
```



查看屏幕分辨率

```
adb shell wm size
```



查看数据库所有表

```
adb shell
sqlite3 test
.tables

```



查看应用包名

```
adb shell pm list packages -e "qq"
```



导出所有的日志

```
adb root 、 adb remount 、 adb pull data/logs  + 电脑本地路径
```



查看正在运行的应用程序

```
adb shell ps|findstr com.company.demo
adb shell ps|grep com.company.demo
```

查看正在运行的服务

```
dumpsys activity services | grep DemoService
```


[显示磁盘空间统计信息(df)](https://docs.oracle.com/cd/E24847_01/html/819-6951/spmonitor-6.html "显示磁盘空间统计信息(df)")

```
adb shell
df -h
```



卸载系统应用

```
adb shell pm uninstall -k --user 0 com.company.demo

```



查看apk的包信息

```
 .\aapt dump badging .\demo.apk
```

本地关联远程分支
```
git branch --set-upstream-to=origin/remote_branch  your_branch
```

