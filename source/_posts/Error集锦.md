---
title: Error集锦
---


## 一.电脑安装过多个版本的jdk导致的错误。
### 问题描述：
1、先安装了jdk1.7.0_80，后安装了jdk1.8.0_181，环境变量中配置了前者；
2、在cmd中输入java -version出现以下错误：Registry key Error: Java version has value '1.8', but '1.7' is required

### 解决方案：
jdk1.7升级到jdk1.8的时候，有三个地方要确认一下：
#### 1.系统环境变量

#### 2.注册表
计算机\HKEY_LOCAL_MACHINE\SOFTWARE\JavaSoft\Java Runtime Environment

#### 3.更新 C:\Windows\System32 下面的java.exe javaw.exe javaws.exe
安装1.7的时候 没问题；在安装1.8，修改环境变量后，C:\Windows\System32 下面的java.exe javaw.exe javaws.exe 还是1.7安装包里面的。

#### **将1.7的java.exe 替换这几个文件为java1.8的就好。**

## 二.Kotlin 中使用AspectJ
### 问题描述：
java.util.zip.ZipException: zip file is empty
### 解决方案：
```
build.gradle增加如下配置：
android {
...
    aspectjx {
        exclude 'versions.9'  
    }
}
```


