---
title: Ubuntu下 Android7.1编译
---

RK3128 编译

1.环境配置

```
sudo apt-get install openjdk-8-jdk
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib:$JAVA_HOME/lib/tools.jar
java -version
sudo apt-get install git  gnupg flex bison gperf build-essential zip curl zlib1g-dev gcc-multilib g++-multilib libc6-dev-i386 lib32ncurses-dev x11proto-core-dev libx11-dev lib32z1-dev libgl1-mesa-dev libxml2-utils xsltproc unzip
```



2.编译uboot

```
cd u-boot
make rk3128_box_defconfig 
make clean
make distclean
make rk3128_box_defconfig   
make -j32
```



3.编译kernel

```
cd ../kernel
make  rockchip_defconfig 
make clean
make  rockchip_defconfig 
make rk3128-box-rk88.img -j32
```



4.编译系统：

```
cd ..
source build/envsetup.sh
make clean
make installclean
source build/envsetup.sh
lunch rk3128_box-userdebug
make -j32
```

5.编译后一定要执行,确认oem\system\目录下文件是否拷贝覆盖到out\target\product\rk3128_box\system目录下。 

```
cp -rf oem/system/* out/target/product/$TARGET_PRODUCT/system/
```

6.生成 ota 固件 可支持U盘升级 

```
./mkimage.sh ota  
```

7.如不需要OTA,执行 

```
./mkimage.sh
```

8.获取ota升级包

```
make otapackage -j32   
```

9.注意：

- 在编译完成之后请先确认 oem\system\目录下文件是否拷贝覆盖到out\target\product\rk3128_box\system目录下。然后在执行./mkimage.sh ota 或 ./mkimage.sh或 make otapackage。
- 打包：
  用windows工具打包固件，屏蔽AndroidTool\rockdev\package-file文件中的
  #trust       Image/trust.img.
- Git 已经删除，请自行创建。
  请自行编写脚本或者参考1.make_rom.sh,uild_base.sh ,2.clean_all.sh,3.clean_app.sh。
  编译环境 详见 RK3128 Android7.1 软件开发指南V1.00-20170823。
  工具在SDK/RKTools 目录下

10.framework.jar的位置：
```
out/target/common/obj/JAVA_LIBRARIES/framework_intermediates/classes.jar
```

11.编译过程遇到的问题：

- lex:aidl<=system/tools/aidl/aidl_language_I.II FAILED:/

  ```
  export LC_ALL=C
  ```

  

- Ensure Jack service is installed and started

  ```
  1.从/etc/java-8-openjdk/security/java.security中找到jdk.tls.disabledAlgorithms取消TLSv1, TLSv1.1 禁用; 
  
  2.检查jack 服务是否启动
  cd prebuilts/sdk/tools
  ./jack-admin start-server/stop-server/kill-server
  ```

  

-  error while loading shared libraries:libncurses.so.5

  ```
  sudo apt install libncurses5
  ```

  
