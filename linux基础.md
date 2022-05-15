# linux基础

## 1、常用命令

### 1.1、sort

sort将文件的每一行作为一个单位，相互比较，比较原则是从首字符向后，依次按ASCII码值进行比较，最后将他们按升序输出。

```shell
-n 按照数字大小排序
-k 按照指定列数排序
-t 指定分隔符来划分列数
-u 在输出行中去除重复行
-r 排序方式改为降序
-o 排序结果输入到源文件
```

```shell
[root@centos-base ~]# cat test.txt
test 30  
Hello 95  
Linux 85 
[root@centos-base ~]# sort  test.txt 
Hello 95  
Linux 85 
test 30  
[root@centos-base ~]# sort  test.txt -o test.txt
[root@centos-base ~]# cat test.txt 
Hello 95  
Linux 85 
test 30
[root@centos-base ~]# sort -k 2nr test.txt 
Hello 95  
Linux 85 
test 30  
[root@centos-base ~]# sort -k 2n test.txt  
test 30  
Linux 85 
Hello 95  
```

其他选项

-f 将小写字母都转换为大写字母来进行比较，亦即忽略大小写

-b 忽略每一行前面的所有空白部分，从第一个可见字符开始比较。

 [详细-k 参数参照](https://www.cnblogs.com/51linux/archive/2012/05/23/2515299.html)

### 1.2、uniq

去除文件重复行

```shell
-i  忽略大小写字符的不同；
-c  进行计数
-u  只显示唯一的行
```

```shell
[root@centos-base ~]# sort test.txt | uniq 
Hello 95
Linux 85
test 30
test 30Hello 95
[root@centos-base ~]# sort test.txt | uniq -c
      2 Hello 95
      3 Linux 85
      2 test 30
      1 test 30Hello 95
[root@centos-base ~]# sort test.txt | uniq -cu
      1 test 30Hello 95
[root@centos-base ~]# sort test.txt | uniq -cui
      1 test 30Hello 95
[root@centos-base ~]# cat test.txt 
Hello 95
Linux 85
test 30
Hello 95
Linux 85
test 30Hello 95
Linux 85
test 30
```

### 1.3、cut

从一个文本文件或者文本流中提取文本列

```shell
-d  ：后面接分隔字符。与 -f 一起使用；
-f  ：依据 -d 的分隔字符将一段信息分割成为数段，用 -f 取出第几段的意思,使用“,”分开可以指定多个列；
-c  ：以字符 (characters) 的单位取出固定字符区间；
```

```shell
[root@centos-base ~]# cat test.txt 

Hello 95 aaaa
Linux 85   11122
test 30 22334
Hello 95  aassdd
Linux 85  bbccdd
test 30Hello 95
Linux 85 fffddd
test 30 fffgghh
[root@centos-base ~]# cat test.txt | cut -d " " -f 3

aaaa

22334


95
fffddd
fffgghh
[root@centos-base ~]# cat test.txt | cut -d " " -f 2

95
85
30
95
85
30Hello
85
30
[root@centos-base ~]# cat test.txt | cut -d " " -f 2-

95 aaaa
85   11122
30 22334
95  aassdd
85  bbccdd
30Hello 95
85 fffddd
30 fffgghh
```

### 1.4、tar

```shell
三种文件类型
-z .tar.gz == tgz
-j .tar.bz2 == tbz、tb2
-J .tar.xz == txz


-c 创建压缩包
-C 指定解压路径
-x 解压文件
-v 显示详情
-t 查看内容

-r 向压缩包末尾追加文件
-u 替换压缩包内指定文件

-f 使用压缩包名称


[root@centos-base ~]# tar -cvzf test.tar.gz 10.4.7.21.txt 10.4.7.22.txt
10.4.7.21.txt
10.4.7.22.txt
[root@centos-base ~]# ls
10.4.7.21.txt  anaconda-ks.cfg  iftop-1.0-0.21.pre4.el7.x86_64.rpm  original-ks.cfg  test.txt
10.4.7.22.txt  expect.sh        ip.txt                              test.tar.gz
[root@centos-base ~]# tar -rf test.tar.gz ip.txt
tar: Cannot update compressed archives
tar: Error is not recoverable: exiting now

## tar包追加、更新只支持不带压缩类型的tar包。（即打包时不指定 -z -j -J 选项
小结
1、*.tar 用 tar –xvf 解压 
2、*.gz 用 gzip -d或者gunzip 解压 
3、*.tar.gz和*.tgz 用 tar –xzf 解压 
4、*.bz2 用 bzip2 -d或者用bunzip2 解压 
5、*.tar.bz2用tar –xjf 解压 
6、*.Z 用 uncompress 解压 
7、*.tar.Z 用tar –xZf 解压 
8、*.rar 用 unrar e解压 
9、*.zip 用 unzip 解压
```

### 1.5、软链接

```shell
ln -s   [源文件目录]  [目标文件目录]
# 修改软链接
ln -snf   [新的源文件目录]  [目标文件]
# 删除软链接
rm -rf 目标文件          # 注意：此处末尾加/会删除源文件。  rm -rf 目标文件/

```

### 1.6、硬链接

```shell
ln   源文件     目标文件      # 硬链接不支持目录
```

软硬链接区别

1、软链接以存放另一个文件的路径的形式存在，硬链接以文件副本的形式存在；
2、软链接可以跨不同的文件系统而链接，硬链接不可以；
3、软链接可以对目录进行链接，而硬链接不可以；
4、软链接可以对一个不存在的文件名进行链接，硬链接必须要有源文件。
5、删除软链接并不影响被指向的文件，但若被指向的原文件被删除，则相关软连接就变成了死链接。删除硬链接的话，只要索引节点的个数不为零，则不会对原始文件造成任何影响；




## 2、网络监控

### 2.1、iftop工具

[安装包下载地址](https://pkgs.org/download/iftop)

![Snip20220406_1](https://raw.githubusercontent.com/chengxinmu/Figure-bed/main/linux_base/linux_baseSnip20220406_1-20220423215231470-20220423215420671.png)

```shell
界面上面显示的是类似刻度尺的刻度范围，为显示流量图形的长条作标尺用的。
中间的<= =>这两个左右箭头，表示的是流量的方向。
TX：发送流量
RX：接收流量
TOTAL：总流量
Cumm：运行iftop到目前时间的总流量
peak：流量峰值
rates：分别表示过去 2s 10s 40s 的平均流量
```

## 3、tcp协议

tcp状态总结

| 状态        | 描述                                                         |
| ----------- | ------------------------------------------------------------ |
| CLOSED      | 初始状态，表示 TCP 连接是”关闭着的”或”未打开的”，该状态下无法通信。 |
| LISTEN      | 表示服务器端的某个 socket 处于监听状态，可以接受客户端的连接。 |
| SYN_RCVD    | 表示服务器接收到了来自客户端请求连接的 SYN 报文。这个状态是在服务端的，但是它是一个中间状态，很短暂，平常我们用 netstat 或 ss 的时候，不太容易看到这种状态，但是遇到 SYN flood 之类的 SYN 攻击时，会出现大量的这种状态，即收不到三次握手最后一个客户端发来的 ACK，所以一直是这个状态，不会转换到 `ESTABLISHED` 状态。 |
| SYN_SENT    | 这个状态与 `SYN_RCVD` 状态相呼应，它是 TCP 连接客户端的状态，当客户端 socket 执行 connect() 进行连接时，它首先发送 SYN 报文，然后随即进入到 `SYN_SENT` 状态，并等待服务端的 SYN 和 ACK，该状态表示客户端的 SYN 已发送。 |
| ESTABLISHED | 表示 TCP 连接已经成功建立，开始传输数据。                    |
| FIN_WAIT_1  | 这个状态在实际工作中很少能看到，当客户端想要主动关闭连接时，它会向服务端发送 FIN 报文，此时 TCP 状态就进入到 `FIN_WAIT_1` 的状态，而当服务端回复 ACK，确认关闭后，则客户端进入到 `FIN_WAIT_2` 的状态，也就是只有在没有收到服务端 ACK 的情况下，`FIN_WAIT_1` 状态才能看到，然后长时间收不到 ACK，通常会在默认超时时间 60s（由内核参数 tcp_fin_timeout 控制）后，直接进入 `CLOSED` 状态。 |
| FIN_WAIT_2  | 这个状态相比较常见，也是需要注意的一个状态，`FIN_WAIT_1` 在接收到服务端 ACK 之后就进入到 `FIN_WAIT_2` 的状态，然后等待服务端发送 FIN，所以在收到对端 FIN 之前，TCP 都会处于 `FIN_WAIT_2` 的状态，也就是，在主动断开的一端发现大量的 `FIN_WAIT_2` 状态时，需要注意，可能时网络不稳定或程序中忘记调用连接关闭，`FIN_WAIT_2` 也有超时时间，也是由内核参数 tcp_fin_timeout 控制，当 `FIN_WAIT_2` 状态超时后，连接直接销毁。 |
| CLOSE_WAIT  | 表示正在等待关闭，该状态只在被动端出现，即当主动断开的一端调用 close() 后发送 FIN 报文给被动端，被动端必然会回应一个 ACK（这是由 TCP 协议层决定的），这个时候，TCP 连接状态就进入到 `CLOSE_WAIT` 状态。 |
| LAST_ACT    | 当被动关闭的一方在发送 FIN 报文后，等待对方的 ACK 报文的时候，就处于 `LAST_ACK` 的状态，当收到对方的 ACK 之后，就进入到 `CLOSED` 状态了。 |
| TIME_WAIT   | 该状态是最常见的状态，主动方在收到对方 FIN 后，就由 `FIN_WAIT_2` 状态进入到 `TIME_WAIT` 状态。 |
| CLOSING     | 这个状态是一个比较特殊的状态，也比较少见，正常情况下不会出现，但是当双方同时都作为主动的一方，调用 close() 关闭连接的时候，两边都进入 `FIN_WAIT_1`的状态，此时期望收到的是 ACK包，进入 `FIN_WAIT_2` 的状态，但是却先收到了对方的 FIN 包，这个时候，就会进入到 `CLOSING` 的状态，然后给对方一个ACK，接收到 ACK 后直接进入到 `CLOSED` 状态。 |

![tcp状态转换](https://raw.githubusercontent.com/chengxinmu/Figure-bed/main/linux_base/tcp%E7%8A%B6%E6%80%81%E8%BD%AC%E6%8D%A2.png)

[参考链接](https://getiot.tech/tcpip/tcp-connection-states.html)

[参考链接2](https://hit-alibaba.github.io/interview/basic/network/TCP.html)

## 4、OSI七层模型

![OSI协议参考](https://raw.githubusercontent.com/chengxinmu/Figure-bed/main/linux_base/OSI%E5%8D%8F%E8%AE%AE%E5%8F%82%E8%80%83.png)





![osi七层](https://raw.githubusercontent.com/chengxinmu/Figure-bed/main/linux_base/osi%E4%B8%83%E5%B1%82.jpeg)



**1.报文(message)**
报文包含了应用层的完整的数据信息。

**2.数据段(segment)**
数据段是传输层的信息单元。 

**3.数据报(datagram)**

数据报是面向无连接的数据传输。采用数据报方式传输时，被传输的分组称为数据报。如传输层TCP的分组叫做数据段，UDP的叫做数据报。

还有一种说法是数据报是数据包的分组，一个完整的数据包由一个或多个数据报组成。（待确认）

 **4.数据包(packet)**

数据包是网络层传输的数据单元。也称为IP包，包中带有足够寻址信息(IP地址)，可独立地从源主机传输到目的主机

还有一种说法是数据报是网络层的传输基本单位，数据包是IP协议中完整的数据单元，由一个或多个数据报组成。（待确认）

 **5.帧(frame)**
帧是数据链路层的传输单元。它将上层传入的数据添加一个头部和尾部，组成了帧，帧根据MAC地址寻址。 

 **6.bit流(bit)**
bit是在物理层的介质上直接实现无结构bit流传送的。

## 5、磁盘

### 5.1、parted

优势：可划分单个分区大于2T的磁盘。主要采用GPT分区格式。最多支持128主分区。

```shell
parted [选项]… [设备 [命令 [参数]…]…]
```

```shell
选项	描述
-h	–help 显示此求助信息
-l	–list 列出所有设别的分区信息
-i	–interactive 在必要时，提示用户
-s	–script 从不提示用户
-v	–version 显示版本
```

```shell
parted交互命令	说 明
check NUMBER	做一次简单的文件系统检测
mklabel,mktable LABEL-TYPE	创建新的磁盘卷标（分区表）
mkfs NUMBER FS-TYPE	在分区上建立文件系统
mkpart PART-TYPE [FS-TYPE] START END	创建一个分区 # 暂时不支持ext4、xfs格式创建
move NUMBER START END	移动分区
name NUMBER NAME	给分区命名
print [devices|free|list,all|NUMBER]	显示分区表、活动设备、空闲空间、所有分区
quit	退出
rescue START END	修复丢失的分区
resizepart NUMBER START END	修改分区大小
rm NUMBER	删除分区
select DEVICE	选择需要编辑的设备
set NUMBER FLAG STATE	改变分区标记
toggle [NUMBER [FLAG]]	切换分区表的状态
unit UNIT	设置默认的单位
Version	显示版本

```

```shell
# 指定分区表类型
[root@centos-base ~]# parted -s /dev/sdb mklabel gpt                         
[root@centos-base ~]# parted /dev/sdb print         
Model: ATA CentOS Linux-1 S (scsi)
Disk /dev/sdb: 10.7GB
Sector size (logical/physical): 512B/4096B
Partition Table: gpt
Disk Flags: 

Number  Start  End  Size  File system  Name  Flags

# 建立分区
[root@centos-base ~]# parted -s /dev/sdb mkpart primary xfs 0% 100%
[root@centos-base ~]# parted /dev/sdb print
Model: ATA CentOS Linux-1 S (scsi)
Disk /dev/sdb: 10.7GB
Sector size (logical/physical): 512B/4096B
Partition Table: gpt
Disk Flags: 

Number  Start   End     Size    File system  Name     Flags
 1      1049kB  10.7GB  10.7GB               primary

# 重命名
[root@centos-base ~]# parted -s /dev/sdb name 1 test1 
[root@centos-base ~]# parted /dev/sdb print           
Model: ATA CentOS Linux-1 S (scsi)
Disk /dev/sdb: 10.7GB
Sector size (logical/physical): 512B/4096B
Partition Table: gpt
Disk Flags: 

Number  Start   End     Size    File system  Name   Flags
 1      1049kB  10.7GB  10.7GB               test1

# 删除
[root@centos-base ~]# parted -s /dev/sdb rm 1
[root@centos-base ~]# parted /dev/sdb print  
Model: ATA CentOS Linux-1 S (scsi)
Disk /dev/sdb: 10.7GB
Sector size (logical/physical): 512B/4096B
Partition Table: gpt
Disk Flags: 

Number  Start  End  Size  File system  Name  Flags

# 扩容分区
[root@centos-base ~]# parted -s /dev/sdb mkpart primary xfs 0% 10%
[root@centos-base ~]# parted /dev/sdb print                       
Model: ATA CentOS Linux-1 S (scsi)
Disk /dev/sdb: 10.7GB
Sector size (logical/physical): 512B/4096B
Partition Table: gpt
Disk Flags: 

Number  Start   End     Size    File system  Name     Flags
 1      1049kB  1074MB  1073MB               primary

[root@centos-base ~]# parted -s /dev/sdb resizepart 1 20%                     
[root@centos-base ~]# parted /dev/sdb print              
Model: ATA CentOS Linux-1 S (scsi)
Disk /dev/sdb: 10.7GB
Sector size (logical/physical): 512B/4096B
Partition Table: gpt
Disk Flags: 

Number  Start   End     Size    File system  Name     Flags
 1      1049kB  2147MB  2146MB               primary

# 格式化
[root@centos-base ~]# mkfs.xfs /dev/sdb1
meta-data=/dev/sdb1              isize=512    agcount=4, agsize=131008 blks
         =                       sectsz=4096  attr=2, projid32bit=1
         =                       crc=1        finobt=0, sparse=0
data     =                       bsize=4096   blocks=524032, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal log           bsize=4096   blocks=2560, version=2
         =                       sectsz=4096  sunit=1 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0

# 挂载
[root@centos-base ~]# mount /dev/sdb1 /data
[root@centos-base ~]# df -Th
Filesystem                   Type      Size  Used Avail Use% Mounted on
/dev/mapper/VolGroup-lv_root ext4       50G  1.6G   46G   4% /
devtmpfs                     devtmpfs  906M     0  906M   0% /dev
tmpfs                        tmpfs     917M     0  917M   0% /dev/shm
tmpfs                        tmpfs     917M  9.0M  908M   1% /run
tmpfs                        tmpfs     917M     0  917M   0% /sys/fs/cgroup
/dev/sda1                    ext4      477M  114M  334M  26% /boot
/dev/mapper/VolGroup-lv_home ext4       12G   41M   11G   1% /home
tmpfs                        tmpfs     184M     0  184M   0% /run/user/0
/dev/sdb1                    xfs       2.0G   33M  2.0G   2% /data

# 扩容文件系统
[root@centos-base ~]# umount /dev/sdb1
[root@centos-base ~]# parted -s /dev/sdb resizepart 1 40%
[root@centos-base ~]# mount /dev/sdb1 /data
[root@centos-base ~]# xfs_growfs /dev/sdb1     #resize2fs  适用ext4、ext3格式
meta-data=/dev/sdb1              isize=512    agcount=4, agsize=131008 blks
         =                       sectsz=4096  attr=2, projid32bit=1
         =                       crc=1        finobt=0 spinodes=0
data     =                       bsize=4096   blocks=524032, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal               bsize=4096   blocks=2560, version=2
         =                       sectsz=4096  sunit=1 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
data blocks changed from 524032 to 1048320
[root@centos-base ~]# df -h
Filesystem                    Size  Used Avail Use% Mounted on
/dev/mapper/VolGroup-lv_root   50G  1.6G   46G   4% /
devtmpfs                      906M     0  906M   0% /dev
tmpfs                         917M     0  917M   0% /dev/shm
tmpfs                         917M  9.0M  908M   1% /run
tmpfs                         917M     0  917M   0% /sys/fs/cgroup
/dev/sda1                     477M  114M  334M  26% /boot
/dev/mapper/VolGroup-lv_home   12G   41M   11G   1% /home
tmpfs                         184M     0  184M   0% /run/user/0
/dev/sdb1                     4.0G   33M  4.0G   1% /data


```

### 5.2、e2label 使用

```shell
[root@centos-base ~]# parted -s /dev/sdb mkpart primary 40% 50%
[root@centos-base ~]# parted -s /dev/sdb print
Model: ATA CentOS Linux-1 S (scsi)
Disk /dev/sdb: 10.7GB
Sector size (logical/physical): 512B/4096B
Partition Table: gpt
Disk Flags: 

Number  Start   End     Size    File system  Name     Flags
 1      1049kB  4295MB  4294MB  xfs          primary
 2      4295MB  5369MB  1074MB               primary
[root@centos-base ~]# mkfs.ext4 /dev/sdb2
mke2fs 1.42.9 (28-Dec-2013)
Discarding device blocks: done                            
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
65536 inodes, 262144 blocks
13107 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=268435456
8 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks: 
        32768, 98304, 163840, 229376

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (8192 blocks): done
Writing superblocks and filesystem accounting information: done
[root@centos-base ~]# e2label /dev/sdb2 disk1
[root@centos-base ~]# e2label /dev/sdb2
disk1
[root@centos-base ~]# cat /etc/fstab

#
# /etc/fstab
# Created by anaconda on Wed Sep 23 16:17:24 2020
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/VolGroup-lv_root /                       ext4    defaults        1 1
UUID=afc30963-55e3-49ef-8ca1-326d582ad306 /boot                   ext4    defaults        1 2
/dev/mapper/VolGroup-lv_home /home                   ext4    defaults        1 2
/dev/mapper/VolGroup-lv_swap swap                    swap    defaults        0 0
LABEL=disk1 /data1 ext4  defaults,noatime,nodiratime,delalloc 0 0
[root@centos-base ~]# mount -a
[root@centos-base ~]# df -Th
Filesystem                   Type      Size  Used Avail Use% Mounted on
/dev/mapper/VolGroup-lv_root ext4       50G  1.6G   46G   4% /
devtmpfs                     devtmpfs  906M     0  906M   0% /dev
tmpfs                        tmpfs     917M     0  917M   0% /dev/shm
tmpfs                        tmpfs     917M  9.0M  908M   1% /run
tmpfs                        tmpfs     917M     0  917M   0% /sys/fs/cgroup
/dev/sda1                    ext4      477M  114M  334M  26% /boot
/dev/mapper/VolGroup-lv_home ext4       12G   41M   11G   1% /home
tmpfs                        tmpfs     184M     0  184M   0% /run/user/0
/dev/sdb1                    xfs       4.0G   33M  4.0G   1% /data
/dev/sdb2                    ext4      976M  2.6M  907M   1% /data1
```

### 5.3、交换分区（待补充）



### 5.4、smartctl（待补充）

```shell
[root@centos-base ~]# yum -y install smartmontools

[root@centos-base ~]# smartctl -i /dev/sdb
smartctl 7.0 2018-12-30 r4883 [x86_64-linux-3.10.0-957.el7.x86_64] (local build)
Copyright (C) 2002-18, Bruce Allen, Christian Franke, www.smartmontools.org

=== START OF INFORMATION SECTION ===
Device Model:     CentOS Linux-1 SSD
Serial Number:    E2DC030BV6H4XB4XK15M
Firmware Version: F.AFEZM4
User Capacity:    10,737,418,240 bytes [10.7 GB]
Sector Sizes:     512 bytes logical, 4096 bytes physical
Rotation Rate:    Solid State Device
Device is:        Not in smartctl database [for details use: -P showall]
ATA Version is:   ATA8-ACS, ATA/ATAPI-5 T13/1321D revision 1
SATA Version is:  SATA 2.6, 3.0 Gb/s
Local Time is:    Mon Apr 25 04:19:10 2022 EDT
SMART support is: Unavailable - device lacks SMART capability.
```



### 5.5、磁盘读写速度

#### 5.5.1、iostat

iostat是I/O statistics（输入/输出统计）的缩写，可以用来统计CPU、网卡、tty设备、磁盘、CD-ROM 等等设备的活动情况, 负载信息。

缺点是无法对某个进程深入分析。

```shell
iostat [参数] [时间] [次数]
```

```shell
-C 显示CPU使用情况
-d 显示磁盘使用情况
-k 以 KB 为单位显示
-m 以 M 为单位显示
-N 显示磁盘阵列(LVM) 信息
-n 显示NFS 使用情况
-p [磁盘] 显示磁盘和分区的情况
-t 显示终端和CPU的信息
-x 显示详细信息
-V 显示版本信息
```

![iostat-1](https://raw.githubusercontent.com/chengxinmu/Figure-bed/main/linux_base/iostat-1.png)

**cpu属性值说明：**

%user：CPU处在用户模式下的时间百分比。

%nice：CPU处在带NICE值的用户模式下的时间百分比。

%system：CPU处在系统模式下的时间百分比。

%iowait：CPU等待输入输出完成时间的百分比。

%steal：管理程序维护另一个虚拟处理器时，虚拟CPU的无意识等待时间百分比。

%idle：CPU空闲时间百分比。

备注：如果%iowait的值过高，表示硬盘存在I/O瓶颈，%idle值高，表示CPU较空闲，如果%idle值高但系统响应慢时，有可能是CPU等待分配内存，此时应加大内存容量。%idle值如果持续低于10，那么系统的CPU处理能力相对较低，表明系统中最需要解决的资源是CPU。

**disk属性值说明：**

rrqm/s: 每秒进行 merge 的读操作数目。即 rmerge/s

wrqm/s: 每秒进行 merge 的写操作数目。即 wmerge/s

r/s: 每秒完成的读 I/O 设备次数。即 rio/s

w/s: 每秒完成的写 I/O 设备次数。即 wio/s

rsec/s: 每秒读扇区数。即 rsect/s

wsec/s: 每秒写扇区数。即 wsect/s

rkB/s: 每秒读K字节数。是 rsect/s 的一半，因为每扇区大小为512字节。

wkB/s: 每秒写K字节数。是 wsect/s 的一半。

avgrq-sz: 平均每次设备I/O操作的数据大小 (扇区)。

avgqu-sz: 平均I/O队列长度。

await: 平均每次设备I/O操作的等待时间 (毫秒)。

svctm: 平均每次设备I/O操作的服务时间 (毫秒)。

%util: 一秒中有百分之多少的时间用于 I/O 操作，即被io消耗的cpu百分比

备注：如果 %util 接近 100%，说明产生的I/O请求太多，I/O系统已经满负荷，该磁盘可能存在瓶颈。如果 svctm 比较接近 await，说明 I/O 几乎没有等待时间；如果 await 远大于 svctm，说明I/O 队列太长，io响应太慢，则需要进行必要优化。如果avgqu-sz比较大，也表示有当量io在等待。

[参考链接](https://www.cnblogs.com/wade-luffy/p/6756881.html)

#### 5.5.2、dd

```shell
# 写入测试
[root@centos-base ~]# time dd if=/dev/sdb2 of=/test.disk bs=8k count=2000         
2000+0 records in
2000+0 records out
16384000 bytes (16 MB) copied, 0.0156005 s, 1.1 GB/s

real    0m0.017s
user    0m0.001s
sys     0m0.015s

# 读取测试
[root@centos-base ~]# time dd if=/test.disk of=/dev/null bs=8k
2000+0 records in
2000+0 records out
16384000 bytes (16 MB) copied, 0.00417164 s, 3.9 GB/s

real    0m0.005s
user    0m0.000s
sys     0m0.005s

# 读写测试
[root@centos-base ~]# time dd if=/test.disk of=/tmp/test.disk2 bs=8k
2000+0 records in
2000+0 records out
16384000 bytes (16 MB) copied, 0.0108234 s, 1.5 GB/s

real    0m0.012s
user    0m0.001s
sys     0m0.010s
```



#### 5.5.3、iotop

![iotop-1](https://raw.githubusercontent.com/chengxinmu/Figure-bed/main/linux_base/iotop-1.png)

#### 5.5.4、hdparm

```shell
-t 普通读取
-T 快读
```

```shell
[root@centos-base ~]# hdparm /dev/sdb              

/dev/sdb:
 multcount     = 128 (on)
 IO_support    =  1 (32-bit)
 readonly      =  0 (off)
 readahead     = 8192 (on)
 geometry      = 1305/255/63, sectors = 20971520, start = 0
 
 [root@centos-base ~]# hdparm -t /dev/sdb

/dev/sdb:
 Timing buffered disk reads: 956 MB in  3.00 seconds = 318.39 MB/sec
[root@centos-base ~]# hdparm -T /dev/sdb

/dev/sdb:
 Timing cached reads:   27704 MB in  1.99 seconds = 13897.71 MB/sec


```

### 5.6、lvm

PE（物理块）     PV（物理卷）    VG（卷组）    LV（逻辑卷）

缺点：windows不支持

[参考文章](https://blog.csdn.net/lilygg/article/details/84035315)

#### 5.6.1、常规使用

```shell
# 创建物理卷
[root@centos-base ~]# pvcreate /dev/sdb1 /dev/sdb2
WARNING: xfs signature detected on /dev/sdb1 at offset 0. Wipe it? [y/n]: y
  Wiping xfs signature on /dev/sdb1.
WARNING: ext4 signature detected on /dev/sdb2 at offset 1080. Wipe it? [y/n]: y
  Wiping ext4 signature on /dev/sdb2.
  Physical volume "/dev/sdb1" successfully created.
  Physical volume "/dev/sdb2" successfully created.
[root@centos-base ~]# pvs
  PV         VG       Fmt  Attr PSize   PFree 
  /dev/sda2  VolGroup lvm2 a--  <63.51g     0 
  /dev/sdb1           lvm2 ---   <4.00g <4.00g
  /dev/sdb2           lvm2 ---    1.00g  1.00g

# 创建卷组
[root@centos-base ~]# vgcreate vg0  /dev/sdb1  /dev/sdb2
  Volume group "vg0" successfully created
[root@centos-base ~]# vgs
  VG       #PV #LV #SN Attr   VSize   VFree
  VolGroup   1   3   0 wz--n- <63.51g    0 
  vg0        2   0   0 wz--n-   4.99g 4.99g

# 修改PE大小
[root@centos-base ~]# vgchange -s 1 vg0
 Volume group "vg0" successfully changed

# 创建逻辑卷
[root@centos-base ~]# lvcreate -l 100 -n lvm0 vg0 
  Logical volume "lvm0" created.
[root@centos-base ~]# lvcreate -L 100M -n lvm1 vg0
  Logical volume "lvm1" created.
[root@centos-base ~]# lvs
  LV      VG       Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  lv_home VolGroup -wi-ao---- <11.54g                                                    
  lv_root VolGroup -wi-ao----  50.00g                                                    
  lv_swap VolGroup -wi-ao----  <1.97g                                                    
  lvm0    vg0      -wi-a----- 100.00m                                                    
  lvm1    vg0      -wi-a----- 100.00m

# 格式化文件系统
[root@centos-base ~]# mkfs.xfs /dev/vg0/lvm0
meta-data=/dev/vg0/lvm0          isize=512    agcount=4, agsize=6400 blks
         =                       sectsz=4096  attr=2, projid32bit=1
         =                       crc=1        finobt=0, sparse=0
data     =                       bsize=4096   blocks=25600, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal log           bsize=4096   blocks=1605, version=2
         =                       sectsz=4096  sunit=1 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
[root@centos-base ~]# mount /dev/vg0/lvm0 /data
[root@centos-base ~]# df -Th
Filesystem                   Type      Size  Used Avail Use% Mounted on
/dev/mapper/VolGroup-lv_root ext4       50G  1.6G   46G   4% /
devtmpfs                     devtmpfs  906M     0  906M   0% /dev
tmpfs                        tmpfs     917M     0  917M   0% /dev/shm
tmpfs                        tmpfs     917M  9.0M  908M   1% /run
tmpfs                        tmpfs     917M     0  917M   0% /sys/fs/cgroup
/dev/sda1                    ext4      477M  114M  334M  26% /boot
/dev/mapper/VolGroup-lv_home ext4       12G   41M   11G   1% /home
tmpfs                        tmpfs     184M     0  184M   0% /run/user/0
/dev/mapper/vg0-lvm0         xfs        94M  5.3M   89M   6% /data

# 扩大逻辑卷 
[root@centos-base ~]# lvextend -L 200M /dev/vg0/lvm0
  Size of logical volume vg0/lvm0 changed from 100.00 MiB (100 extents) to 200.00 MiB (200 extents).
  Logical volume vg0/lvm0 successfully resized.
[root@centos-base ~]# xfs_growfs /dev/vg0/lvm0
meta-data=/dev/mapper/vg0-lvm0   isize=512    agcount=4, agsize=6400 blks
         =                       sectsz=4096  attr=2, projid32bit=1
         =                       crc=1        finobt=0 spinodes=0
data     =                       bsize=4096   blocks=25600, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal               bsize=4096   blocks=1605, version=2
         =                       sectsz=4096  sunit=1 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
data blocks changed from 25600 to 51200
[root@centos-base ~]# df -Th
Filesystem                   Type      Size  Used Avail Use% Mounted on
/dev/mapper/VolGroup-lv_root ext4       50G  1.6G   46G   4% /
devtmpfs                     devtmpfs  906M     0  906M   0% /dev
tmpfs                        tmpfs     917M     0  917M   0% /dev/shm
tmpfs                        tmpfs     917M  9.0M  908M   1% /run
tmpfs                        tmpfs     917M     0  917M   0% /sys/fs/cgroup
/dev/sda1                    ext4      477M  114M  334M  26% /boot
/dev/mapper/VolGroup-lv_home ext4       12G   41M   11G   1% /home
tmpfs                        tmpfs     184M     0  184M   0% /run/user/0
/dev/mapper/vg0-lvm0         xfs       194M  5.5M  189M   3% /data
```

#### 5.6.2、xfs文件系统缩容逻辑卷

```shell
[root@centos-base ~]# yum install -y xfsdump

[root@centos-base ~]# df -Th
Filesystem                   Type      Size  Used Avail Use% Mounted on
/dev/mapper/VolGroup-lv_root ext4       50G  1.6G   46G   4% /
devtmpfs                     devtmpfs  906M     0  906M   0% /dev
tmpfs                        tmpfs     917M     0  917M   0% /dev/shm
tmpfs                        tmpfs     917M  9.0M  908M   1% /run
tmpfs                        tmpfs     917M     0  917M   0% /sys/fs/cgroup
/dev/sda1                    ext4      477M  114M  334M  26% /boot
/dev/mapper/VolGroup-lv_home ext4       12G   41M   11G   1% /home
tmpfs                        tmpfs     184M     0  184M   0% /run/user/0
/dev/mapper/vg0-lvm0         xfs       194M  5.5M  189M   3% /data

# 备份挂载路径下的文件
[root@centos-base ~]# xfsdump -f /opt/home.dump /data
xfsdump: using file dump (drive_simple) strategy
xfsdump: version 3.1.7 (dump format 3.0) - type ^C for status and control

 ============================= dump label dialog ==============================

please enter label for this dump session (timeout in 300 sec)
 -> test1
session label entered: "test1"

 --------------------------------- end dialog ---------------------------------

xfsdump: level 0 dump of centos-base:/data
xfsdump: dump date: Mon Apr 25 07:36:28 2022
xfsdump: session id: 5ed9fa82-d7df-49cc-a1ca-d159109f97a2
xfsdump: session label: "test1"
xfsdump: ino map phase 1: constructing initial dump list
xfsdump: ino map phase 2: skipping (no pruning necessary)
xfsdump: ino map phase 3: skipping (only one dump stream)
xfsdump: ino map construction complete
xfsdump: estimated dump size: 32512 bytes
xfsdump: /var/lib/xfsdump/inventory created

 ============================= media label dialog =============================

please enter label for media in drive 0 (timeout in 300 sec)
 -> test2
media label entered: "test2"

 --------------------------------- end dialog ---------------------------------

xfsdump: creating dump session media file 0 (media 0, file 0)
xfsdump: dumping ino map
xfsdump: dumping directories
xfsdump: dumping non-directory files
xfsdump: ending media file
xfsdump: media file size 25464 bytes
xfsdump: dump size (non-dir files) : 1088 bytes
xfsdump: dump complete: 28 seconds elapsed
xfsdump: Dump Summary:
xfsdump:   stream 0 /opt/home.dump OK (success)
xfsdump: Dump Status: SUCCESS
[root@centos-base ~]# ls /opt/home.dump 
/opt/home.dump
[root@centos-base ~]# umount /data

# 缩容操作
[root@centos-base ~]# lvresize -L 100M /dev/vg0/lvm0
  WARNING: Reducing active logical volume to 100.00 MiB.
  THIS MAY DESTROY YOUR DATA (filesystem etc.)
Do you really want to reduce vg0/lvm0? [y/n]: y
  Size of logical volume vg0/lvm0 changed from 200.00 MiB (200 extents) to 100.00 MiB (100 extents).
  Logical volume vg0/lvm0 successfully resized.

# 重写文件系统
[root@centos-base ~]# mkfs.xfs -f /dev/vg0/lvm0
meta-data=/dev/vg0/lvm0          isize=512    agcount=4, agsize=6400 blks
         =                       sectsz=4096  attr=2, projid32bit=1
         =                       crc=1        finobt=0, sparse=0
data     =                       bsize=4096   blocks=25600, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal log           bsize=4096   blocks=1605, version=2
         =                       sectsz=4096  sunit=1 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
[root@centos-base ~]# mount -a

# 重新导入数据
[root@centos-base ~]# xfsrestore -f /opt/home.dump /data
xfsrestore: using file dump (drive_simple) strategy
xfsrestore: version 3.1.7 (dump format 3.0) - type ^C for status and control
xfsrestore: searching media for dump
xfsrestore: examining media file 0
xfsrestore: dump description: 
xfsrestore: hostname: centos-base
xfsrestore: mount point: /data
xfsrestore: volume: /dev/mapper/vg0-lvm0
xfsrestore: session time: Mon Apr 25 07:36:28 2022
xfsrestore: level: 0
xfsrestore: session label: "test1"
xfsrestore: media label: "test2"
xfsrestore: file system id: e5f633a9-809e-4caa-b23f-5b1d83bb21ca
xfsrestore: session id: 5ed9fa82-d7df-49cc-a1ca-d159109f97a2
xfsrestore: media id: f55cb92a-9b99-4293-9492-e199a336f097
xfsrestore: using online session inventory
xfsrestore: searching media for directory dump
xfsrestore: reading directories
xfsrestore: 10 directories and 11 entries processed
xfsrestore: directory post-processing
xfsrestore: restoring non-directory files
xfsrestore: restore complete: 0 seconds elapsed
xfsrestore: Restore Summary:
xfsrestore:   stream 0 /opt/home.dump OK (success)
xfsrestore: Restore Status: SUCCESS
[root@centos-base ~]# ls /data
1  2  3  4  5  6  7  8  9
```

#### 5.6.3、ext4文件系统缩容逻辑卷

```shell
[root@centos-base ~]# df -Th
Filesystem                   Type      Size  Used Avail Use% Mounted on
/dev/mapper/VolGroup-lv_root ext4       50G  1.6G   46G   4% /
devtmpfs                     devtmpfs  906M     0  906M   0% /dev
tmpfs                        tmpfs     917M     0  917M   0% /dev/shm
tmpfs                        tmpfs     917M  9.0M  908M   1% /run
tmpfs                        tmpfs     917M     0  917M   0% /sys/fs/cgroup
/dev/sda1                    ext4      477M  114M  334M  26% /boot
/dev/mapper/VolGroup-lv_home ext4       12G   41M   11G   1% /home
tmpfs                        tmpfs     184M     0  184M   0% /run/user/0
/dev/mapper/vg0-lvm0         xfs        94M  5.4M   89M   6% /data
/dev/mapper/vg0-lvm1         ext4       93M  1.6M   85M   2% /data1

[root@centos-base data1]# ls /data1
1  2  3  4  5  6  7  8  9  lost+found

[root@centos-base ~]# umount /data1

# 先缩小文件系统
[root@centos-base ~]# e2fsck -f /dev/vg0/lvm1
e2fsck 1.42.9 (28-Dec-2013)
Pass 1: Checking inodes, blocks, and sizes
Pass 2: Checking directory structure
Pass 3: Checking directory connectivity
Pass 4: Checking reference counts
Pass 5: Checking group summary information
/dev/vg0/lvm1: 22/25688 files (4.5% non-contiguous), 8907/102400 blocks

[root@centos-base ~]# resize2fs /dev/vg0/lvm1 50M
resize2fs 1.42.9 (28-Dec-2013)
Resizing the filesystem on /dev/vg0/lvm1 to 51200 (1k) blocks.
The filesystem on /dev/vg0/lvm1 is now 51200 blocks long.

[root@centos-base ~]# mount /dev/vg0/lvm1 /data1
[root@centos-base ~]# ls /data1
1  2  3  4  5  6  7  8  9  lost+found
[root@centos-base ~]# ls /data1/1
1.txt
[root@centos-base ~]# cat /data1/1/1.txt 
1
```

#### 5.6.4、创建镜像备份

```shell
[root@centos-base ~]# lvcreate -L 10M -n data1_backup -s /dev/mapper/vg0-lvm1
  Logical volume "data1_backup" created.
[root@centos-base ~]# lvs
  LV           VG       Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  lv_home      VolGroup -wi-ao---- <11.54g                                                    
  lv_root      VolGroup -wi-ao----  50.00g                                                    
  lv_swap      VolGroup -wi-ao----  <1.97g                                                    
  data1_backup vg0      swi-a-s---  10.00m      lvm1   0.12                                   
  lvm0         vg0      -wi-ao---- 100.00m                                                    
  lvm1         vg0      owi-aos--- 100.00m                                                    
[root@centos-base ~]# cd /data1
[root@centos-base data1]# ls
1  2  3  4  5  6  7  8  9  lost+found
[root@centos-base data1]# rm -rf 1 2 3 4 5
[root@centos-base data1]# ls
6  7  8  9  lost+found
[root@centos-base ~]# umount /data1
[root@centos-base ~]# lvs
  LV           VG       Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  lv_home      VolGroup -wi-ao---- <11.54g                                                    
  lv_root      VolGroup -wi-ao----  50.00g                                                    
  lv_swap      VolGroup -wi-ao----  <1.97g                                                    
  data1_backup vg0      swi-a-s---  10.00m      lvm1   0.62                                   
  lvm0         vg0      -wi-ao---- 100.00m                                                    
  lvm1         vg0      owi-a-s--- 100.00m                                                    
[root@centos-base ~]# mount /dev/vg0/data1_backup /data1
[root@centos-base ~]# ls /data1
1  2  3  4  5  6  7  8  9  lost+found
```

#### 5.7、文件系统卷标设置

```shell
[root@centos-base ~]# xfs_admin -L disk1 /dev/vg0/lvm0
writing all SBs
new label = "disk1"

[root@centos-base ~]# xfs_admin -l /dev/vg0/lvm0
label = "disk1"

# 格式化时设置卷标
[root@centos-base ~]# mkfs.xfs -L disk1 -f /dev/vg0/lvm0
```

```shell
# 查看ext4文件系统卷标
[root@centos-base ~]# e2label /dev/vg0/lvm1 disk2

[root@centos-base ~]# e2label /dev/vg0/lvm1
disk2

# 格式化时设置卷标
[root@centos-base ~]# mkfs.ext4 -L disk2 -f /dev/vg0/lvm1
```





























