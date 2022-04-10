---
title: Ubuntu命令
---
1.修改分辨率

```
1.sudo vim /etc/default/grub

2.修改 #GRUB_GFXMODE=640x480

3.sudo update-grub

4.重启reboot
```

2.Mac用ssh连接Ubuntu

```
1.Ubutntu上安装了SSH服务>> sudo apt-get install openssh-server

2.查看是否安装启动成功>> ps -e | grep ssh
有sshd 说明启动成功，没有的话执行如下命令：
开启执行>> service sshd start 
停止运行>> service sshd start

3.ssh summer@192.168.xx.xxx
默认端口为22，需要设置端口的话>> ssh -p 123 summer@192.168.xx.xxx
```



3.安装AndroidStudio

```
1. tar -zxvf FileName.tar.gz

2. cd android-studio/bin/

3. ./studio.sh
```

4.安装zsh

```
1.sudo apt install zsh

2.chsh -s /usr/bin/zsh

3.sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

4.安装插件>> git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions

5.sudo vim ~/.zshrc 

6.plugins=(zsh-autosuggestions)
```

5.连接raw.githubusercontent.com失败

```
1.输入raw.githubusercontent.com查询IP地址
2.sudo vi /etc/hosts
3.添加>> xxx.xxx.xxx.xxx raw.githubusercontent.com
```

6.Linux磁盘挂载

```
1.查看挂载分区>> sudo fdisk -l

2.格式化成ext4>> sudo mkfs -t ext4 /dev/sda

3.挂载到自定义文件夹>> sudo mount -t ext4 /dev/sda /home/summer/hdd
```

7.mac 格式化系统启动u盘

```
1.diskutil list

2.diskutil unmountDisk /dev/disk4

3.diskutil eraseDisk JHFS+ iDisk disk4
```

8.Ubuntu 梯子

```
1. snap install qv2ray
```

