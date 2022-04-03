# adb命令

截屏

```bash
adb shell screencap -p /sdcard/screenhot.png

```



录屏

```bash
adb shell screenrecord --size 1920x1080 /sdcard/record.mp4
```



查看屏幕分辨率

```bash
adb shell wm size
```



查看数据库所有表

```bash
adb shell
sqlite3 test
.tables

```



查看应用包名

```bash
adb shell pm list packages -e "qq"
```



导出所有的日志

```bash
adb root 、 adb remount 、 adb pull data/logs  + 电脑本地路径
```



查看正在运行的应用程序

```bash
adb shell ps|findstr com.company.demo
```

查看正在运行的服务

```git
dumpsys activity services | grep DemoService
```


[显示磁盘空间统计信息(df)](https://docs.oracle.com/cd/E24847_01/html/819-6951/spmonitor-6.html "显示磁盘空间统计信息(df)")

```bash
adb shell
df -h
```



卸载系统应用

```java
adb shell pm uninstall -k --user 0 com.company.demo

```



查看apk的包信息

```git
 .\aapt dump badging .\demo.apk
```

