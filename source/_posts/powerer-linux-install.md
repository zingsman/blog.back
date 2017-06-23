---
title: IBM惠普powerer-linux装redhat系统
date: 2017-06-23 14:33:03
tags: power-linux
categories: WORK
---
### 寻找系统
power-linux系统是`ppc64le`系统架构，平常的X86，或者64bit架构的系统都不支持。
下载特定的系统`redhat7.2`下载地址，网上自行寻找。
`RHEL-7.2-Server-ppc64le-dvd.iso`
### power-linux介绍和自己理解
1. power-linux和华为服务打不通，ibm的web管理界面，远远没有华为好用和方便。
2. power-linux web管理界面上，控制台串口登录不进去系统，java总是报错。所以
使用了了ibmitools工具，进行串口登录。他登录方式是你的pc的cmd命令行。还是很方便。
3. 听说power-linux登录web管理界面，不能远程装系统（ubuntu可以），我也没有找到挂载
镜像的地方。

### ibmitools软件的使用
1. 下载软件。百度网盘：
2. 点ibmitool.exe 运行。然后点run.bat运行，自动打开cmd命令行
3. 登录串口的终端就是这个cmd窗口。运行命令：
`ipmitool -I lanplus -H server_ip_address -U ipmi_user -P ipmi_password sol activate
` 其中ip是imana管理地址。用户名是登录管理界面的用户和密码。
4. 登录成功，就相当于登录了串口。---就是不能挂载镜像安装。

### power-linux装系统redhat.7.2
由于发现已经重装了系统，就没有装系统。
### power-linux在线配置raid 卡
这里我们运用到工具。在系统中安装这个工具，就能命令行在线做raid 
`Arcconf-1.08-21385.ppc64le.rpm` 下载后直接 rpm -i 路径   安装即可
安装步骤：
```bash
#操作步骤
#1、上传raid卡管理工具安装包到系统，用rpm直接安装
    Arcconf-1.08-21385.ppc64le.rpm
#2、查看raid卡信息
   arcconf getconfig 1 |more   #（注意硬盘的state mode ）
#3、一般默认前置硬盘state mode为RAW,将其改为ready。
   arcconf task start 1 device 0 0 initialize noprompt
#4、创建raid
   arcconf create 1 logicaldrive max 10 0 0 0 1 0 2 0 3 0 4 0 5 0 6 0 7 noprompt
# 其中 10 代表raid 10 ，后面数字就是盘符
```
**注意** 一定要注意盘符的查看：
```bash
#查看命令：
arcconf getconfig 1 |more
#结果：
Logical device number 1
   Logical device name                      : LogicalDrv 1
   Block Size of member drives              : 512 Bytes
   RAID level                               : 10          #代表RAID 10
   Unique Identifier                        : B83F682D
   Status of logical device                 : Optimal
   Additional details                       : Quick initialized
   Size                                     : 11427830 MB
   Parity space                             : 11427840 MB
   Stripe-unit size                         : 256 KB
   Read-cache setting                       : Enabled
   Read-cache status                        : On
   Write-cache setting                      : Enabled
   Write-cache status                       : On
   Partitioned                              : Yes
   Protected by Hot-Spare                   : No
   Bootable                                 : No
   Failed stripes                           : No
   Power settings                           : Disabled

#单个设备信息
----------------------------------------------------------------------
Physical Device information
----------------------------------------------------------------------
      Device #0
         Device is a Hard drive
         State                              : Online
         Block Size                         : 512 Bytes
         Supported                          : Yes
         Programmed Max Speed               : SATA 6.0 Gb/s
         Transfer Speed                     : SATA 6.0 Gb/s
         Reported Channel,Device(T:L)       : 0,0(0:0)    #这里0,0就是盘符
         Reported Location                  : Enclosure 0, Slot 0(Connector 0)
         Reported ESD(T:L)                  : 2,0(0:0)
         Vendor                             : ATA

```
### 做好raid后就是使用part进行超过2T磁盘分区
命令：
```bash
parted /dev/sdd
mklabel gpt
yes
print
mkpart primary 0 -1
i
print
quit
# 进行挂载和格式化
mkfs -t xfs /dev/sdb1   #可能使用mke2fs格式化，或者其他ext4系统
# 挂载
mount /dev/sdb1 /data
# 更新 /etc/fstab 文件
/dev/sdb1               /data                   xfs     defaults        0 0

```
### 然后进行最后的各项配置。
配置包括：YUM源。用户，监控，网卡绑定等一系列操作
> 这里在配置YUM源的方面。由于系统架构为ppc这种架构。以前搭建的yum源无法使用。
>直接，将这个系统镜像里边的包解压放到yum源某个目录。
>在客户端配置文件中。对应的url就能使用镜像yum源。

### 总结
后面学习看看ansible自动化工具作配置。已经公司已经写好脚本的使用。