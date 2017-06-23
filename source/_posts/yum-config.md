---
title: 内网yum源配置
date: 2017-06-22 15:15:43
tags: WORK
categories: 配置
---
### 说明
在公司内网中，为了节约外网流量已经带宽，也为了安全目的，需要配置内网安全的yum源。
### 步骤
1. 需要一台主机，作为内网yum源，这台主机通过rsync协议，rsync命令，去同步centos或者阿里云镜像。
2. 配置yum服务器主机
3. 配置客户端

### 配置
- 在yum主机上面新建存储仓库包的目录。并安装所需软件 createrepo 和 rsync

```bash 
#安装软件
yum -y install createrepo rsync
#创建目录，我只用来放centos6.5的数据包
mkdir -p /data/centos6.5 
cd /data/centos
#创建该仓库的索引目录
createrepo -pdo /data/centos6.5 /data/centos6.5
#注意，我们在看官方源时候，分不清这个目录是什么。这个目录里面有os和update等子目录，这些子目录可以直接从官方源同步。理解为centos6.5总目录，里面放所有包
```

- 提供yum源文件查看。可用（nginx和python和apatch都可以）

```bash
# 可以用Apache或nginx提供web服务，但用Python的http模块更简单，适用于内网环境
cd /data/centos6.5
python -m SimpleHTTPServer 80 &>/dev/null &
#可以通过浏览器输入本机IP查看。
```

- 放rpm包在仓库中。

```bash
# 只下载软件不安装,若没有yumdownloader，就安装。
yumdownloader pcre-devel openssl-devel 
createrepo --update /data/centos6.5  
# 每加入一个rpm包就要更新一下。
#平时yum安装软件时不删除安装包
# cat /etc/yum.conf 
keepcache=1
# 安装包存储目录
cachedir=/var/cache/yum/$basearch/$releasever
# /var/cache/yum/x86_64/6/base/packages
```

- 用rcync同步官方源到我的本地源服务器 。

上面第3步只是将自己制作的rpm包，放入yum源。但还有一种企业需求，说的更具体一点，平时学生上课yum安装软件都是从公网下载的，占用带宽，因此在学校里搭建一个内网yum服务器，但又考虑到学生回家也要使用yum安装软件，如果yum软件的数据库文件repodata不一样，就会有问题。因此我想到的解决方法就是直接使用公网yum源的repodata。

镜像同步公网yum源
上游yum源必须要支持rsync协议，否则不能使用rsync进行同步。
http://mirrors.ustc.edu.cn/status/
CentOS官方标准源：rsync://mirrors.ustc.edu.cn/centos/
epel源：rsync://mirrors.ustc.edu.cn/epel/
同步命令：
使用rsync同步yum源，为了节省带宽、磁盘和下载时间，我只同步了CentOS6的rpm包，这样所有的rpm包只占用了21G，全部同步需要300G左右。
同步base源，小技巧，我们安装系统的光盘镜像含有部分rpm包，大概3G，这些就不用重新下载。

```bash
#将官方的源，同步到本地，-a是保持所有源属性。
/usr/bin/rsync -avzL rsync://mirrors.ustc.edu.cn/centos/6/ /data/centos6.5/
/usr/bin/rsync -avzL rsync://mirrors.ustc.edu.cn/centos/7/ /data/centos7.2/
/usr/bin/rsync -avzL rsync://mirrors.ustc.edu.cn/centos/5/ /data/centos5/
# epel源
/usr/bin/rsync -av --exclude=debug rsync://mirrors.ustc.edu.cn/epel/6/x86_64/ /data/yum_data/epel/6/x86_64/
```

结果展示
```bash
[root@KVM data]# du -sh yum_data    
21G     yum_data
[root@KVM data]# tree -L 3 yum_data/
yum_data/
├── centos
│   ├── 6.5
│   │   ├── extras
│   │   ├── os
│   │   └── updates
│   └── RPM-GPG-KEY-CentOS-6
├── epel
│   └── 6
│       └── x86_64
```

5. 客户端配置

```bash
# cd /etc/yum.repos.d
[root@B yum.repos.d]# vi zingsman.repo
[zingsman]
name=Server
baseurl=http://10.0.0.5
enable=1
gpgcheck=0
[root@YUM ~]# yum --enablerepo=zingsman --disablerepo=base,extras,updates,epel list 
# 指定使用zingsman库
```
上面是临时使用内网yum源，想永久并简单使用yum -y install lrzsz命令，就需要修改配置文件将默认的repo文件关闭。
```bash
[root@oldboy ~]# cd /etc/yum.repos.d/
[root@oldboy yum.repos.d]# vim CentOS-Base.repo
# 在每一个启动的源加上
# enabled=0   #改为1就启用，没有此参数也是启用。
[base]
…………
enabled=0
[updates]
…………
enabled=0
[extras]
…………
enabled=0
# 还有其他开启的仓库就使用这个办法关闭。
```