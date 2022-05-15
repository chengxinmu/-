# ceph集群部署

## 1、准备工作

### 1.1、时间同步（三台服务器执行）

```shell
[root@kafka-1 ~]# yum -y install ntp
[root@kafka-1 ~]# timedatectl set-timezone Asia/Shanghai
[root@kafka-1 ~]# timedatectl 
[root@kafka-1 ~]# ntpdate ntp.aliyun.com
[root@kafka-1 ~]# date

```

### 1.2、配置免密登录

需配置ceph1节点对所有主/客户机节点的免密（包括ceph1本身），如果有客户端，也需要配置客户端client1节点对所有主/客户机节点的免密（包括client1本身）

```shell
# 略
```

### 1.3、关闭防火墙、selinux

```shell
[root@mha-manage ~]# sed -i "s/SELINUX=enforcing/SELINUX=disabled/g" /etc/selinux/config
[root@mha-manage ~]# systemctl stop firewalld
[root@mha-manage ~]# systemctl disable firewalld
```

## 2、安装ansible（主节点）

### 2.1、安装python3

```shell
# 略
```



### 2.2、安装ceph-ansible

```shell
[root@kafka-1 ~]# yum -y install ansible     # 注意版本，建议使用epel源2.9版本

```

[ceph-ansible](https://github.com/ceph/ceph-ansible/releases)

> Stable 4.0 supports CentOS7.
> Stable 5.0 supports only CentOS8.
>
> 
>
> - `stable-3.0` Supports Ceph versions `jewel` and `luminous`. This branch requires Ansible version `2.4`.
> - `stable-3.1` Supports Ceph versions `luminous` and `mimic`. This branch requires Ansible version `2.4`.
> - `stable-3.2` Supports Ceph versions `luminous` and `mimic`. This branch requires Ansible version `2.6`.
> - `stable-4.0` Supports Ceph version `nautilus`. This branch requires Ansible version `2.8`.
> - `stable-5.0` Supports Ceph version `octopus`. This branch requires Ansible version `2.9`.
> - `master` Supports the master branch of Ceph. This branch requires Ansible version `2.9`.

建议使用4.0版本

```shell
[root@kafka-1 ~]# tar -xf ceph-ansible-4.0.70.tar.gz

# 安装依赖
[root@kafka-1 ceph-ansible-4.0.70]# pip3 install -r requirements.txt
```

### 2.3、配置文件处理

```shell
[root@kafka-1 ~]# cat /etc/ansible/hosts  | grep -Pv "^#|^$"
[mons]
kafka-1

[mgrs]
kafka-1

[osds]
kafka-1
kafka-2
kafka-3

[rgws]
kafka-1
kafka-2
kafka-3

[mdss]
kafka-1
kafka-3

[clients]
kafka-1
kafka-2

[monitoring]
kafka-1

[grafana-kafka]
kafka-1

[grafana-server]
kafka-1
```

```shell
[root@kafka-1 ~]# cat ceph-ansible-4.0.70/roles/ceph-defaults/defaults/main.yml
...

ceph_mirror: http://download.ceph.com/
ceph_stable_key: http://download.ceph.com/keys/release.asc
ceph_stable_release: nautilus
ceph_stable_repo: "{{ ceph_mirror }}/debian-{{ ceph_stable_release }}"

#取消注释（不需要选择）
#dashboard_admin_password: p@ssw0rd
dashboard_admin_password: p@ssw0rd

#取消注释（不需要选择）
#grafana_admin_password: admin
grafana_admin_password: admin

....


## 切换国内原（未测试，本次部署使用楼上源地址）
#修改为国内清华源（阿里源、清华源二选一）
ceph_mirror: http://mirrors.tuna.tsinghua.edu.cn/ceph
ceph_stable_key: https://mirrors.tuna.tsinghua.edu.cn/ceph/keys/release.asc

#修改为国内阿里源
ceph_mirror: http://mirrors.aliyun.com/ceph
ceph_stable_key: https://mirrors.aliyun.com/ceph/keys/release.asc
```

```shell
[root@kafka-1 ~]# cp ceph-ansible-4.0.70/site.yml.sample site.yml
```

```shell
# 注意：
monitor_interface: 实际网卡名称
journal_size: 1024 日志盘大小，根据需要修改
public_network:  实际网络地址
cluster_network: "{{ public_network }}" # 两个网段相同使用时可能会产生莫名bug。务必配置成不同网段。

[root@kafka-1 ceph-ansible-4.0.70]# cat group_vars/all.yml | grep -Pv "^#|^$"
---
dummy:
ceph_origin: repository
ceph_repository: community
ceph_mirror: http://download.ceph.com
ceph_stable_repo: "{{ ceph_mirror }}/rpm-{{ ceph_stable_release }}"
ceph_stable_redhat_distro: el7
ceph_stable_release: nautilus
monitor_interface: eth0
radosgw_interface: eth0
journal_size: 1024
public_network: 10.4.7.0/24
cluster_network: "{{ public_network }}"

[root@kafka-1 group_vars]# cat rgws.yml | grep -Pv "^$|^#"           
---
dummy:
copy_admin_key: true

[root@kafka-1 group_vars]# cat mdss.yml | grep -Pv "^$|^#"        
---
dummy:
copy_admin_key: true

[root@kafka-1 group_vars]# cat osds.yml | grep -Pv "^$|^#"       
---
dummy:
osd_scenario: "non-collocated"
devices:
  - /dev/sdb
dedicated_devices:  
  - /dev/sdc
```

```shell
[root@kafka-1 group_vars]#  cp mons.yml.sample mons.yml
[root@kafka-1 group_vars]#  cp mgrs.yml.sample mgrs.yml
[root@kafka-1 group_vars]#  cp mdss.yml.sample mdss.yml
[root@kafka-1 group_vars]#  cp rgws.yml.sample rgws.yml
[root@kafka-1 group_vars]#  cp osds.yml.sample osds.yml
[root@kafka-1 group_vars]#  cp clients.yml.sample clients.yml
[root@kafka-1 group_vars]#  cp all.yml.sample all.yml
```

## 3、ceph部署

### 3.1、部署和状态检查

```shell
[root@kafka-1 ceph-ansible-4.0.70]# ansible-playbook site.yml

# 执行结束，在执行页面会有相关的提示，如图所示，所有节点显示failed=0，则部署成功。

[root@kafka-2 ~]# ceph -s
  cluster:
    id:     8a18f7c2-f1bd-44ac-8f18-8afbac8715c8
    health: HEALTH_WARN
            1 pools have too few placement groups
            mon is allowing insecure global_id reclaim
 
  services:
    mon: 1 daemons, quorum kafka-1 (age 71m)
    mgr: kafka-1(active, since 59m)
    mds: cephfs:1 {0=kafka-1=up:active} 1 up:standby
    osd: 3 osds: 3 up (since 64m), 3 in (since 64m)
    rgw: 3 daemons active (kafka-1.rgw0, kafka-2.rgw0, kafka-3.rgw0)
 
  task status:
 
  data:
    pools:   6 pools, 144 pgs
    objects: 245 objects, 5.9 KiB
    usage:   63 GiB used, 57 GiB / 120 GiB avail
    pgs:     144 active+clean
    
[root@kafka-1 ceph-ansible-4.0.70]# ceph mgr services
{
    "dashboard": "https://kafka-1:8443/",
    "prometheus": "http://kafka-1:9283/"
}

# 查看CRUSH的层次，包括节点权重。
[root@client ~]# ceph osd crush tree
ID  CLASS WEIGHT  TYPE NAME         
 -1       0.28815 root default      
-11       0.04880     host docker-1 
  3   ssd 0.04880         osd.3     
 -9       0.08788     host docker-2 
  4   ssd 0.04639         osd.4     
  5   ssd 0.04149         osd.5     
 -7       0.03909     host kafka-1  
  2   ssd 0.03909         osd.2     
 -3       0.03909     host kafka-2  
  1   ssd 0.03909         osd.1     
 -5       0.03909     host kafka-3  
  0   ssd 0.03909         osd.0     
-13       0.03419     host osd      
  6   ssd 0.03419         osd.6
  
```

![ceph-1](https://raw.githubusercontent.com/chengxinmu/Figure-bed/main/linux_base/ceph-1.jpg)

![ceph-garafna](https://raw.githubusercontent.com/chengxinmu/Figure-bed/main/linux_base/ceph-garafna.jpg)

### 3.2、报错记录

```shell
[root@kafka-2 ~]# ceph -s
  cluster:
    id:     8a18f7c2-f1bd-44ac-8f18-8afbac8715c8
    health: HEALTH_WARN
            1 pools have too few placement groups
            mon is allowing insecure global_id reclaim
 
  services:
    mon: 1 daemons, quorum kafka-1 (age 71m)
    mgr: kafka-1(active, since 59m)
    mds: cephfs:1 {0=kafka-1=up:active} 1 up:standby
    osd: 3 osds: 3 up (since 64m), 3 in (since 64m)
    rgw: 3 daemons active (kafka-1.rgw0, kafka-2.rgw0, kafka-3.rgw0)
 
  task status:
 
  data:
    pools:   6 pools, 144 pgs
    objects: 245 objects, 5.9 KiB
    usage:   63 GiB used, 57 GiB / 120 GiB avail
    pgs:     144 active+clean

[root@kafka-1 ~]# ceph osd pool autoscale-status
POOL                  SIZE TARGET SIZE RATE RAW CAPACITY  RATIO TARGET RATIO EFFECTIVE RATIO BIAS PG_NUM NEW PG_NUM AUTOSCALE 
cephfs_metadata      3278               3.0       119.9G 0.0000                               4.0      8            warn      
default.rgw.meta      393               3.0       119.9G 0.0000                               1.0     32            warn      
cephfs_data             0               3.0       119.9G 0.0000                               1.0      8         32 warn      
default.rgw.control     0               3.0       119.9G 0.0000                               1.0     32            warn      
.rgw.root            2398               3.0       119.9G 0.0000                               1.0     32            warn      
default.rgw.log         0               3.0       119.9G 0.0000                               1.0     32            warn    

# 安全模式关闭可处理第二个告警
[root@kafka-1 ~]# ceph config set mon auth_allow_insecure_global_id_reclaim false

# 第一个告警需要开启对应池的自动伸缩
[root@kafka-1 ~]# ceph osd pool set cephfs_data pg_autoscale_mode on

[root@kafka-1 ~]# ceph health detail            
HEALTH_OK
[root@kafka-1 ~]# ceph -s
  cluster:
    id:     8a18f7c2-f1bd-44ac-8f18-8afbac8715c8
    health: HEALTH_OK
 
  services:
    mon: 1 daemons, quorum kafka-1 (age 118m)
    mgr: kafka-1(active, since 106m)
    mds: cephfs:1 {0=kafka-1=up:active} 1 up:standby
    osd: 3 osds: 3 up (since 111m), 3 in (since 111m)
    rgw: 3 daemons active (kafka-1.rgw0, kafka-2.rgw0, kafka-3.rgw0)
 
  task status:
 
  data:
    pools:   6 pools, 168 pgs
    objects: 245 objects, 5.9 KiB
    usage:   63 GiB used, 57 GiB / 120 GiB avail
    pgs:     168 active+clean
 
  io:
    client:   2.2 KiB/s rd, 0 B/s wr, 2 op/s rd, 1 op/s wr

```

```shell
[root@kafka-1 ceph-ansible-4.0.70]# ceph health detail
HEALTH_WARN Long heartbeat ping times on back interface seen, longest is 12394.107 msec; Long heartbeat ping times on front interface seen, longest is 12394.180 msec
OSD_SLOW_PING_TIME_BACK Long heartbeat ping times on back interface seen, longest is 12394.107 msec
    Slow heartbeat ping on back interface from osd.1 to osd.2 12394.107 msec possibly improving
    Slow heartbeat ping on back interface from osd.1 to osd.0 12393.689 msec
OSD_SLOW_PING_TIME_FRONT Long heartbeat ping times on front interface seen, longest is 12394.180 msec
    Slow heartbeat ping on front interface from osd.1 to osd.2 12394.180 msec possibly improving
    Slow heartbeat ping on front interface from osd.1 to osd.0 12393.549 msec

[root@kafka-1 ceph-ansible-4.0.70]# ceph daemon /var/run/ceph/ceph-mgr.k093.asok dump_osd_network 0
```

```shell
fatal: [ceph_client -> {{ item }}]: FAILED! => 
  msg: |-
    The task includes an option with an undefined variable. The error was: 'dict object' has no attribute 'hostname'
  
    The error appears to be in '/root/ceph-ansible-4.0.70/roles/ceph-facts/tasks/facts.yml': line 45, column 3, but may
    be elsewhere in the file depending on the exact syntax problem.
  
    The offending line appears to be:
  
  
    - name: set_fact monitor_name ansible_facts['hostname']
      ^ here

# 可以运行以下命令处理
[root@kafka-1 ~]# ansible -i hostsceph clients -m setup -a 'filter=ansible_hostname'
```

```shell
# set_fact _monitor_address to monitor_address_block ipv4 此模块报错即由于public网段和cluster网段一样导致。

参考 https://github.com/ceph/ceph-ansible/issues/4333

```

### 3.3、节点扩容

#### 3.3.1、扩容mon节点

新节点配置免密     # 略

新节点配置时间同步    # 略

```shell
# 主节点配置hosts
[root@kafka-1 ~]# cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
10.4.7.17 kafka-1
10.4.7.19 kafka-2
10.4.7.18 kafka-3
10.4.7.20 mha-manage

# 添加节点到hosts文件
[root@kafka-1 ceph-ansible-4.0.70]# cat /etc/ansible/hosts | grep -A 4 mons
[mons]
kafka-1
mha-manage

....

# 添加节点
[root@kafka-1 ceph-ansible-4.0.70]# ansible-playbook -i  /etc/ansible/hosts site.yml --limit mha-manage

INSTALLER STATUS *********************************************************************************************************************
Install Ceph Monitor           : Complete (0:00:12)
Install Ceph Node Exporter     : Complete (0:00:52)
Install Ceph Crash             : Complete (0:00:03)
```

![ceph_add_mon](https://raw.githubusercontent.com/chengxinmu/Figure-bed/main/linux_base/ceph_add_mon.jpg)

#### 3.3.2、扩容其他类型节点

```shell
# 将新增节点添加到ansible inventory
# 初始化节点。关闭防火墙，配置主节点到新节点免密、时间同步等。

[root@kafka-1 ceph-ansible-4.0.70]# cat /etc/ansible/hosts | grep -A 50 mons 
[mons]
kafka-1
mha-manage

[mgrs]
kafka-1
docker-2 devices="['/dev/sdb','/dev/sdc']" dedicated_devices="['/dev/sdd']"

[osds]
kafka-1
kafka-2
kafka-3
docker-1
docker-2 devices="['/dev/sdb','/dev/sdc']" dedicated_devices="['/dev/sdd']"

[rgws]
kafka-1
kafka-2
kafka-3

[mdss]
kafka-1
kafka-3
docker-2 devices="['/dev/sdb','/dev/sdc']" dedicated_devices="['/dev/sdd']"

[clients]
kafka-1
kafka-2
docker-1

[monitoring]
kafka-1

[grafana-kafka]
kafka-1

[grafana-server]
kafka-1
```

```shell
# 测试节点连通性

[root@kafka-1 ceph-ansible-4.0.70]# ansible -m ping all
docker-1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
mha-manage | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
docker-2 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
kafka-1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
kafka-3 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
kafka-2 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}

```

```shell
# 新增节点

[root@kafka-1 ceph-ansible-4.0.70]# ansible-playbook -i  /etc/ansible/hosts site.yml --limit docker-1,docker-2

```

![ceph_add_other](https://raw.githubusercontent.com/chengxinmu/Figure-bed/main/linux_base/ceph_add_other.jpg)

## 4、ceph使用

### 4.1、客户端安装

```shell
# 将客户端ntp、防火墙、秘钥等初始化完成。添加主机名至hosts文件后执行添加角色即可。

[root@kafka-1 ceph-ansible-4.0.70]# cat /etc/ansible/hosts | grep -A 5 clients
[clients]
...

client

[root@kafka-1 ceph-ansible-4.0.70]# ansible-playbook -vvv site.yml --limit client

# 最后出现compelete字样即完成。

# 配置文件传递给客户端

[root@kafka-1 ~]# scp /etc/ceph/ceph.conf client:/etc/ceph/
ceph.conf                                                                      100%  889     2.5MB/s   00:00    
[root@kafka-1 ~]# scp /etc/ceph/ceph.client.admin.keyring client:/etc/ceph
ceph.client.admin.keyring     
```

### 4.2、授权

| 权限                  | 说明                                                         |
| :-------------------- | :----------------------------------------------------------- |
| r                     | 向用户授予读取权限。访问监视器(mon)以检索 CRUSH 运行图时需具有此能力。 |
| w                     | 向用户授予针对对象的写入权限。                               |
| x                     | 授予用户调用类方法(包括读取和写入)的能力，以及在监视器中执行 auth 操作的能力。 |
| *                     | 授予用户对特定守护进程/存储池的读取、写入和执行权限，以及执行管理命令的能力 |
| class-read            | 授予用户调用类读取方法的能力，属于是 x 能力的子集。          |
| class-write           | 授予用户调用类写入方法的能力，属于是 x 能力的子集。          |
| profile osd           | 授予用户以某个 OSD 身份连接到其他 OSD 或监视器的权限。授予 OSD 权限，使 OSD 能够处理复制检测信号流量和状态报告。 |
| profile mds           | 授予用户以某个 MDS 身份连接到其他 MDS 或监视器的权限。       |
| profile bootstrap-osd | 授予用户引导 OSD 的权限(初始化 OSD 并将 OSD 加入 ceph 集群)，授 权给部署工具，使其在引导 OSD 时有权添加密钥。 |
| profile bootstrap-mds | 授予用户引导元数据服务器的权限，授权部署工具权限，使其在引导 元数据服务器时有权添加密钥。 |

[文档参照](https://www.icode9.com/content-4-1117034.html)



### 4.3、ceph用户管理

```shell
# 查看当前存储池

[root@client ~]# ceph osd lspools
1 cephfs_data
2 cephfs_metadata
3 .rgw.root
4 default.rgw.control
5 default.rgw.meta
6 default.rgw.log
7 pool-test1
8 pool-test2
9 cephtest-matadata
10 cephtest-data

# 创建用户
[root@client ~]# ceph auth add client.tony mon 'allow r' osd 'allow rwx pool=pool-test2'
added key for client.tony

# 查看用户秘钥
[root@client ~]# ceph auth get client.tony
[client.tony]
        key = AQAPMH9iqX8tAxAAPHI5vAnzWHGR1ZPZJURYwA==
        caps mon = "allow r"
        caps osd = "allow rwx pool=pool-test2"
exported keyring for client.tony

[root@kafka-1 ~]# ceph auth list | grep -A 3 tony
installed auth entries:

client.tony
        key: AQAPMH9iqX8tAxAAPHI5vAnzWHGR1ZPZJURYwA==
        caps: [mon] allow r
        caps: [osd] allow rwx pool=pool-test2

# 修改用户权限
[root@client ~]# ceph auth caps client.tony mon 'allow r' osd 'allow rw pool=pool-test2' 
updated caps for client.tony
[root@client ~]# ceph auth get client.tony
[client.tony]
        key = AQAPMH9iqX8tAxAAPHI5vAnzWHGR1ZPZJURYwA==
        caps mon = "allow r"
        caps osd = "allow rw pool=pool-test2"
exported keyring for client.tony        

# 导入keyring到文件
[root@client ~]# ceph auth get client.tony -o /etc/ceph/ceph.client.tony.keyring
exported keyring for client.tony
[root@client ~]# cat /etc/ceph/ceph.client.tony.keyring
[client.tony]
        key = AQAPMH9iqX8tAxAAPHI5vAnzWHGR1ZPZJURYwA==
        caps mon = "allow r"
        caps osd = "allow rw pool=pool-test2"

# 用户恢复
[root@client ~]# ceph auth del client.tony
updated
[root@client ~]# ceph auth get client.tony
Error ENOENT: failed to find client.tony in keyring

[root@client ~]# ceph auth import -i /etc/ceph/ceph.client.tony.keyring
imported keyring
[root@client ~]# ceph auth get client.tony
[client.tony]
        key = AQAPMH9iqX8tAxAAPHI5vAnzWHGR1ZPZJURYwA==
        caps mon = "allow r"
        caps osd = "allow rw pool=pool-test2"
exported keyring for client.tony

# 将一个用户的秘钥导入另一个用户秘钥中
[root@client ~]# ceph-authtool --import-keyring /etc/ceph/ceph.client.tony.keyring /etc/ceph/ceph.client.client.keyring
importing contents of /etc/ceph/ceph.client.tony.keyring into /etc/ceph/ceph.client.client.keyring
[root@client ~]# ceph-authtool -l  /etc/ceph/ceph.client.client.keyring
[client.client]
        key = AQCckn5iGE1oORAAO6RhGbkE0hQMUDQJ3FGQLw==
        caps mds = "allow rw"
        caps mon = "allow r"
        caps osd = "allow rw pool=cephtest-data"
[client.tony]
        key = AQAPMH9iqX8tAxAAPHI5vAnzWHGR1ZPZJURYwA==
        caps mon = "allow r"
        caps osd = "allow rw pool=pool-test2"
```



### 4.4、osd使用

```shell
# ceph 查看授权用户
[root@client ~]# ceph auth list
mds.docker-2
        key: AQABfHpiVbNPKRAAnJCmkXwS+X8bYQI3rRC7XQ==
        caps: [mds] allow
        caps: [mon] allow profile mds
        caps: [osd] allow rwx
mds.kafka-1
        key: AQCeNHlivSipBhAAZXpPRWxF/Sh0agpnoIvkxw==
        caps: [mds] allow
        caps: [mon] allow profile mds
        caps: [osd] allow rwx
mds.kafka-3
        key: AQCeNHliJka8CBAAHhUk6WO8EO10nx1qzh0pSQ==
        caps: [mds] allow
        caps: [mon] allow profile mds
        caps: [osd] allow rwx
osd.0
        key: AQBONHlil9G2BBAAXmhrhhlGpI/t0mxuvs9AMg==
        caps: [mgr] allow profile osd
        caps: [mon] allow profile osd
        caps: [osd] allow *
osd.1
        key: AQBONHliDHBLBRAAcmuNM+zyUxOk5PyVgHdeeg==
        caps: [mgr] allow profile osd
        caps: [mon] allow profile osd
        caps: [osd] allow *
osd.2
        key: AQBONHlipjkECBAAgBBa8I25l6NrQKIqBOhivg==
        caps: [mgr] allow profile osd
        caps: [mon] allow profile osd
        caps: [osd] allow *
osd.3
        key: AQDOe3piU1bbDBAA9MkGtX/706JsYxcVFOxHMg==
        caps: [mgr] allow profile osd
        caps: [mon] allow profile osd
        caps: [osd] allow *
osd.4
        key: AQDOe3pimEcwGRAAjsB9l+QhPQT/BPBKgXS4aA==
        caps: [mgr] allow profile osd
        caps: [mon] allow profile osd
        caps: [osd] allow *
osd.5
        key: AQDSe3piDuVjDBAA0zso9nwfYGzvvkGalUnXgw==
        caps: [mgr] allow profile osd
        caps: [mon] allow profile osd
        caps: [osd] allow *
osd.6
        key: AQCWDn1i8/66DRAARAeYyzY2jR0qNf3ZuctDFw==
        caps: [mgr] allow profile osd
        caps: [mon] allow profile osd
        caps: [osd] allow *
client.admin
        key: AQC5MnliRRkmNxAAVaw9gSqdPMemyIjSrJJXOg==
        caps: [mds] allow *
        caps: [mgr] allow *
        caps: [mon] allow *
        caps: [osd] allow *
client.bootstrap-mds
        key: AQC5MnliHCUmNxAAu1kf3pMef4CKO/ZkcyzSnQ==
        caps: [mon] allow profile bootstrap-mds
client.bootstrap-mgr
        key: AQC5MnliIS4mNxAAIUZ+8cEHXZ0/SEVizOQNYw==
        caps: [mon] allow profile bootstrap-mgr
client.bootstrap-osd
        key: AQC5MnliJTYmNxAAMiA8/XFlLORICx5xVdU+RQ==
        caps: [mon] allow profile bootstrap-osd
client.bootstrap-rbd
        key: AQC5MnlizT8mNxAAtIgDKITMezbO/f6uIj38ew==
        caps: [mon] allow profile bootstrap-rbd
client.bootstrap-rbd-mirror
        key: AQC5MnliT0cmNxAAyxY0aXH+D6XDZcEtGoWwdg==
        caps: [mon] allow profile bootstrap-rbd-mirror
client.bootstrap-rgw
        key: AQC5MnliaE4mNxAAJmeOGYaptTSOU5d8JTWvvg==
        caps: [mon] allow profile bootstrap-rgw
client.crash
        key: AQCVNXliAAAAABAA1E/WQfOldeFGxiUluR2JyQ==
        caps: [mgr] allow profile crash
        caps: [mon] allow profile crash
client.rgw.kafka-1.rgw0
        key: AQC0NHliCAeSDRAAQ0VR/GQSI36ws8WrCOod0Q==
        caps: [mon] allow rw
        caps: [osd] allow rwx
client.rgw.kafka-2.rgw0
        key: AQC0NHli9Gd3DxAAVYFQ0yFWdK0z7JOsyHd9jQ==
        caps: [mon] allow rw
        caps: [osd] allow rwx
client.rgw.kafka-3.rgw0
        key: AQC0NHliSgvBDBAAh5UfetW00ZFWy5/sjx9Nxw==
        caps: [mon] allow rw
        caps: [osd] allow rwx
mgr.docker-2
        key: AQCBe3piAAAAABAAJmLMejBjE6TQrEXKziETDg==
        caps: [mds] allow *
        caps: [mon] allow profile mgr
        caps: [osd] allow *
mgr.kafka-1
        key: AQD0M3liAAAAABAA/WSBterlFSySohbKC558VQ==
        caps: [mds] allow *
        caps: [mon] allow profile mgr
        caps: [osd] allow *
installed auth entries:

# 查看存储池
[root@client ~]# ceph osd lspools
1 cephfs_data
2 cephfs_metadata
3 .rgw.root
4 default.rgw.control
5 default.rgw.meta
6 default.rgw.log
7 pool-test1
8 pool-test2

# 查看存储空间
[root@kafka-1 infrastructure-playbooks]# ceph df
RAW STORAGE:
    CLASS     SIZE        AVAIL       USED        RAW USED     %RAW USED 
    ssd       260 GiB     129 GiB     125 GiB      131 GiB         50.41 
    TOTAL     260 GiB     129 GiB     125 GiB      131 GiB         50.41 
 
POOLS:
    POOL                    ID     PGS     STORED      OBJECTS     USED       %USED     MAX AVAIL 
    cephfs_data              1      32         0 B           0        0 B         0        29 GiB 
    cephfs_metadata          2       8     4.2 KiB          22     96 KiB         0        29 GiB 
    .rgw.root                3      32     2.3 KiB           6     72 KiB         0        29 GiB 
    default.rgw.control      4      32         0 B           8        0 B         0        29 GiB 
    default.rgw.meta         5      32       393 B           2     24 KiB         0        29 GiB 
    default.rgw.log          6      32         0 B         207        0 B         0        29 GiB 
    pool-test1               7      32         0 B           0        0 B         0        29 GiB 
    pool-test2               8      32         0 B           0        0 B         0        57 GiB 

# 查看存储池保存副本
[root@client ~]# ceph osd pool get pool-test1 size
size: 3

# 对存储池启用rbd
[root@client ~]# ceph osd pool application enable pool-test1 rbd
enabled application 'rbd' on pool 'pool-test1'

# 初始化pool
[root@client ~]# rbd pool init -p pool-test1  

# 在对应的池创建镜像
[root@client ~]# rbd create rbd-img-1 --size 5G --pool pool-test1 --image-format 2 --image-feature layering

[root@client ~]# rbd create rbd-img-2 --size 3G --pool pool-test1 --image-format 2 --image-feature layering

[root@client ~]# rbd ls --pool pool-test1
rbd-img-1
rbd-img-2

# 查看镜像信息

[root@client ~]# rbd --image rbd-img-1 --pool pool-test1 info
rbd image 'rbd-img-1':
        size 5 GiB in 1280 objects
        order 22 (4 MiB objects)
        snapshot_count: 0
        id: 873e781f6737
        block_name_prefix: rbd_data.873e781f6737
        format: 2
        features: layering
        op_features: 
        flags: 
        create_timestamp: Fri May 13 20:43:24 2022
        access_timestamp: Fri May 13 20:43:24 2022
        modify_timestamp: Fri May 13 20:43:24 2022
                
```

**镜像的其他特性**

- **layering:** 支持镜像分层快照特性，用于快照及写时复制，可以对 image 创建快照并保护，然 后从快照克隆出新的 image 出来，父子 image 之间采用 COW 技术，共享对象数据。默认添加的特性
- **striping:** 支持条带化 v2，类似 raid 0，只不过在 ceph 环境中的数据被分散到不同的对象中， 可改善顺序读写场景较多情况下的性能。
- **exclusive-lock:** 支持独占锁，限制一个镜像只能被一个客户端使用。
- **object-map:** 支持对象映射(依赖 exclusive-lock),加速数据导入导出及已用空间统计等，此特 性开启的时候，会记录 image 所有对象的一个位图，用以标记对象是否真的存在，在一些场 景下可以加速 io。
- **fast-diff:** 快速计算镜像与快照数据差异对比(依赖 object-map)。 **deep-flatten:** 支持快照扁平化操作，用于快照管理时解决快照依赖关系等。
- **journaling:** 修改数据是否记录日志，该特性可以通过记录日志并通过日志恢复数据(依赖独 占锁),开启此特性会增加系统磁盘 IO 使用。

**jewel** 默认开启的特性包括**: layering/exlcusive lock/object map/fast diff/deep flatten**

```shell
# 新特性开启、关闭

[root@client ~]# rbd feature enable exclusive-lock --pool pool-test1 --image rbd-img-1          
[root@client ~]# rbd --image rbd-img-1 --pool pool-test1 info
rbd image 'rbd-img-1':
        size 5 GiB in 1280 objects
        order 22 (4 MiB objects)
        snapshot_count: 0
        id: 873e781f6737
        block_name_prefix: rbd_data.873e781f6737
        format: 2
        features: layering, exclusive-lock
        op_features: 
        flags: 
        create_timestamp: Fri May 13 20:43:24 2022
        access_timestamp: Fri May 13 20:43:24 2022
        modify_timestamp: Fri May 13 20:43:24 2022
[root@client ~]# rbd feature disable exclusive-lock --pool pool-test1 --image rbd-img-1    
[root@client ~]# rbd --image rbd-img-1 --pool pool-test1 info                           
rbd image 'rbd-img-1':
        size 5 GiB in 1280 objects
        order 22 (4 MiB objects)
        snapshot_count: 0
        id: 873e781f6737
        block_name_prefix: rbd_data.873e781f6737
        format: 2
        features: layering
        op_features: 
        flags: 
        create_timestamp: Fri May 13 20:43:24 2022
        access_timestamp: Fri May 13 20:43:24 2022
        modify_timestamp: Fri May 13 20:43:24 2022

# 映射镜像到本地  rbd map mypool/test.img
[root@client ~]# rbd map --pool pool-test1 --image rbd-img-1
/dev/rbd0

[root@client ~]# lsblk
NAME                 MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda                    8:0    0    20G  0 disk 
├─sda1                 8:1    0   500M  0 part /boot
└─sda2                 8:2    0  19.5G  0 part 
  ├─VolGroup-lv_root 253:0    0    16G  0 lvm  /
  ├─VolGroup-lv_swap 253:1    0     2G  0 lvm  [SWAP]
  └─VolGroup-lv_home 253:2    0   1.6G  0 lvm  /home
sr0                   11:0    1 128.6M  0 rom  
sr1                   11:1    1   918M  0 rom  
rbd0                 252:0    0     5G  0 disk 

# 格式化、挂载
[root@client ~]# mkfs.xfs /dev/rbd0 
meta-data=/dev/rbd0              isize=512    agcount=8, agsize=163840 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=0, sparse=0
data     =                       bsize=4096   blocks=1310720, imaxpct=25
         =                       sunit=1024   swidth=1024 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal log           bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=8 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0

[root@client ~]# mkdir /data

[root@client ~]# mount /dev/rbd0 /data

[root@client ~]# echo test1 > /data/test.txt

[root@client ~]# cat /data/test.txt
test1

[root@client ~]# lsmod | grep ceph
libceph               306625  1 rbd
dns_resolver           13140  1 libceph
libcrc32c              12644  4 xfs,libceph,nf_nat,nf_conntrack

# 挂载rbd后内核自动加载ceph模块
```

### 4.5、cephFS使用

```shell
# 现有文件系统查看
[root@client ~]# ceph fs ls
name: cephfs, metadata pool: cephfs_metadata, data pools: [cephfs_data ]

[root@client ~]# ceph fs status cephfs
cephfs - 0 clients
======
+------+--------+---------+---------------+-------+-------+
| Rank | State  |   MDS   |    Activity   |  dns  |  inos |
+------+--------+---------+---------------+-------+-------+
|  0   | active | kafka-3 | Reqs:    0 /s |   10  |   13  |
+------+--------+---------+---------------+-------+-------+
+-----------------+----------+-------+-------+
|       Pool      |   type   |  used | avail |
+-----------------+----------+-------+-------+
| cephfs_metadata | metadata |  112k | 32.4G |
|   cephfs_data   |   data   |    0  | 32.4G |
+-----------------+----------+-------+-------+
+-------------+
| Standby MDS |
+-------------+
|   docker-2  |
+-------------+
MDS version: ceph version 14.2.22 (ca74598065096e6fcbd8433c8779a2be0c889351) nautilus (stable)

[root@client ~]# ceph mds stat
cephfs:1 {0=kafka-3=up:active} 1 up:standby


```

```shell
# 创建文件系统
[root@client ~]# ceph fs new cephfs-test cephtest-matadata cephtest-data
Error EINVAL: Creation of multiple filesystems is disabled.  To enable this experimental feature, use 'ceph fs flag set enable_multiple true'
[root@client ~]# ceph fs flag set enable_multiple true
Warning! This feature is experimental.It may cause problems up to and including data loss.Consult the documentation at ceph.com, and if unsure, do not proceed.Add --yes-i-really-mean-it if you are certain.

```

**ceph不建议在一个集群中使用多个文件系统。可能导致MDS或客户端节点意外终止。**

[官方文档](https://access.redhat.com/documentation/en-us/red_hat_ceph_storage/2/html/ceph_file_system_guide_technology_preview/what_is_the_ceph_file_system_cephfs)

```shell
# 创建账户
[root@client ~]# ceph auth get-or-create client.client mon 'allow r' mds 'allow rw' osd 'allow rw pool=cephtest-data'
[client.client]
        key = AQBCh35ia5pvOBAAPmccuxGPgp3qSkDmknvUQQ==

# 创建秘钥
[root@client ~]# ceph-authtool --create-keyring ceph.client.client.keyring       
creating ceph.client.client.keyring

# 验证秘钥
[root@client ~]# ceph auth get client.client
[client.client]
        key = AQBCh35ia5pvOBAAPmccuxGPgp3qSkDmknvUQQ==
        caps mds = "allow rw"
        caps mon = "allow r"
        caps osd = "allow rw pool=cephtest-data"
exported keyring for client.client

# 秘钥导出
[root@client ~]# ceph auth get client.client -o ceph.client.client.keyring
exported keyring for client.client

[root@client ~]# mv ceph.client.client.keyring /etc/ceph/

# 验证账户
[root@client ~]# ceph -s --user client
  cluster:
    id:     8a18f7c2-f1bd-44ac-8f18-8afbac8715c8
    health: HEALTH_OK
 
  services:
    mon: 2 daemons, quorum kafka-1,mha-manage (age 4h)
    mgr: kafka-1(active, since 28h), standbys: docker-2
    mds: cephfs:1 {0=kafka-3=up:active} 1 up:standby
    osd: 7 osds: 7 up (since 4h), 7 in (since 26h)
    rgw: 3 daemons active (kafka-1.rgw0, kafka-2.rgw0, kafka-3.rgw0)
 
  task status:
 
  data:
    pools:   10 pools, 328 pgs
    objects: 263 objects, 14 MiB
    usage:   147 GiB used, 148 GiB / 295 GiB avail
    pgs:     328 active+clean
 
  io:
    client:   8.0 KiB/s rd, 0 B/s wr, 7 op/s rd, 5 op/s wr
    
[root@client ~]# ceph auth print-key client.client > client.client.key    

[root@client ~]# mount -t ceph -o name=client,secretfile=client.client.key 10.4.7.17:6789,10.4.7.20:6789:/ /data2/ 
[root@client ~]# df -h
Filesystem                       Size  Used Avail Use% Mounted on
/dev/mapper/VolGroup-lv_root      16G  1.6G   14G  11% /
devtmpfs                         906M     0  906M   0% /dev
tmpfs                            917M     0  917M   0% /dev/shm
tmpfs                            917M  9.0M  908M   1% /run
tmpfs                            917M     0  917M   0% /sys/fs/cgroup
/dev/sda1                        477M  114M  334M  26% /boot
/dev/mapper/VolGroup-lv_home     1.6G  4.8M  1.5G   1% /home
tmpfs                            184M     0  184M   0% /run/user/0
/dev/rbd0                        5.0G   33M  5.0G   1% /data
10.4.7.17:6789,10.4.7.20:6789:/   33G     0   33G   0% /data2

[root@client ~]# cd /data2

[root@client data2]# ls

[root@client data2]# mkdir test

[root@client data2]# 
```

![ceph-client](https://raw.githubusercontent.com/chengxinmu/Figure-bed/main/linux_base/ceph-client.jpg)

### 4.6、ceph对象网关

#### 4.6.1、确认服务使用端口

```shell
# 查看服务启用端口

[root@kafka-1 ~]# cat /etc/ceph/ceph.conf | grep rgw
[client.rgw.kafka-1.rgw0]
keyring = /var/lib/ceph/radosgw/ceph-rgw.kafka-1.rgw0/keyring
log file = /var/log/ceph/ceph-rgw-kafka-1.rgw0.log
rgw frontends = beast endpoint=10.4.7.17:8080
rgw thread pool size = 512

[root@kafka-1 ~]# ps aux | grep rgw
ceph       58894  0.1  1.1 5155620 20736 ?       Ssl  May13   2:22 /usr/bin/radosgw -f --cluster ceph --name client.rgw.kafka-1.rgw0 --setuser ceph --setgroup ceph
root      141215  0.0  0.0 112808   972 pts/0    R+   13:56   0:00 grep --color=auto rgw
[root@kafka-1 ~]# ss -ltuanp | grep 58894
tcp    LISTEN     0      128    10.4.7.17:8080                  *:*                   users:(("radosgw",pid=58894,fd=45))
tcp    ESTAB      0      0      10.4.7.17:35536              10.4.7.22:6808                users:(("radosgw",pid=58894,fd=46))
```

![ceph-rgw1](https://raw.githubusercontent.com/chengxinmu/Figure-bed/main/linux_base/ceph-rgw1.jpg)

#### 4.6.2、s3接口测试

```shell
# 创建radosgw用户

[root@client ~]# radosgw-admin user create --uid="rgwuser" --display-name="This is first rgw test user"
{
    "user_id": "rgwuser",
    "display_name": "This is first rgw test user",
    "email": "",
    "suspended": 0,
    "max_buckets": 1000,
    "subusers": [],
    "keys": [
        {
            "user": "rgwuser",
            "access_key": "EJIE1SDRWU8BE7LBVNNP",
            "secret_key": "1UvxuDmjs8JAfCFc67D2X7gUEnE0OoDZmAnF8LMz"
        }
    ],
    "swift_keys": [],
    "caps": [],
    "op_mask": "read, write, delete",
    "default_placement": "",
    "default_storage_class": "",
    "placement_tags": [],
    "bucket_quota": {
        "enabled": false,
        "check_on_raw": false,
        "max_size": -1,
        "max_size_kb": 0,
        "max_objects": -1
    },
    "user_quota": {
        "enabled": false,
        "check_on_raw": false,
        "max_size": -1,
        "max_size_kb": 0,
        "max_objects": -1
    },
    "temp_url_keys": [],
    "type": "rgw",
    "mfa_ids": []
}

```

![ceph-rgwuser-1](https://raw.githubusercontent.com/chengxinmu/Figure-bed/main/linux_base/ceph-rgwuser-1.jpg)

```shell
[root@client ~]# pip3 install boto
WARNING: Running pip install with root privileges is generally not a good idea. Try `pip3 install --user` instead.
Collecting boto
  Downloading https://files.pythonhosted.org/packages/23/10/c0b78c27298029e4454a472a1919bde20cb182dab1662cec7f2ca1dcc523/boto-2.49.0-py2.py3-none-any.whl (1.4MB)
    100% |████████████████████████████████| 1.4MB 776kB/s 
Installing collected packages: boto
Successfully installed boto-2.49.0

[root@client ~]# cat s3.py 
import boto
import boto.s3.connection
access_key = 'EJIE1SDRWU8BE7LBVNNP'
secret_key = '1UvxuDmjs8JAfCFc67D2X7gUEnE0OoDZmAnF8LMz'
conn = boto.connect_s3(
    aws_access_key_id = access_key,
    aws_secret_access_key = secret_key,
    host = '10.4.7.17', port=8080,
    is_secure=False,
    calling_format = boto.s3.connection.OrdinaryCallingFormat(),
)
bucket = conn.create_bucket('my-first-s3-bucket')
for bucket in conn.get_all_buckets():
        print("{name}\t{created}".format(
                name = bucket.name,
                created = bucket.creation_date,
))

[root@client ~]# python3 s3.py    
my-first-s3-bucket      2022-05-14T07:03:00.942Z
```

![ceph-s3-1](https://raw.githubusercontent.com/chengxinmu/Figure-bed/main/linux_base/ceph-s3-1.jpg)

[官方文档](https://docs.ceph.com/en/latest/radosgw/s3/python/#)

[参考](https://www.51cto.com/article/684376.html)

#### 4.6.3、s3命令行使用方式

```shell
# s3cmd 配置

[root@client ~]# s3cmd --configure

Enter new values or accept defaults in brackets with Enter.
Refer to user manual for detailed description of all options.

Access key and Secret key are your identifiers for Amazon S3. Leave them empty for using the env variables.
Access Key: EJIE1SDRWU8BE7LBVNNP
Secret Key: 1UvxuDmjs8JAfCFc67D2X7gUEnE0OoDZmAnF8LMz
Default Region [US]: 

Use "s3.amazonaws.com" for S3 Endpoint and not modify it to the target Amazon S3.
S3 Endpoint [s3.amazonaws.com]: 10.4.7.17:8080

Use "%(bucket)s.s3.amazonaws.com" to the target Amazon S3. "%(bucket)s" and "%(location)s" vars can be used
if the target S3 system supports dns based buckets.
DNS-style bucket+hostname:port template for accessing a bucket [%(bucket)s.s3.amazonaws.com]: 10.4.7.17:8080/%(bucket)s

Encryption password is used to protect your files from reading
by unauthorized persons while in transfer to S3
Encryption password: 
Path to GPG program [/usr/bin/gpg]: 

When using secure HTTPS protocol all communication with Amazon S3
servers is protected from 3rd party eavesdropping. This method is
slower than plain HTTP, and can only be proxied with Python 2.7 or newer
Use HTTPS protocol [Yes]: no

On some networks all internet access must go through a HTTP proxy.
Try setting it here if you can't connect to S3 directly
HTTP Proxy server name: 

New settings:
  Access Key: EJIE1SDRWU8BE7LBVNNP
  Secret Key: 1UvxuDmjs8JAfCFc67D2X7gUEnE0OoDZmAnF8LMz
  Default Region: US
  S3 Endpoint: 10.4.7.17:8080
  DNS-style bucket+hostname:port template for accessing a bucket: 10.4.7.17:8080/%(bucket)s
  Encryption password: 
  Path to GPG program: /usr/bin/gpg
  Use HTTPS protocol: False
  HTTP Proxy server name: 
  HTTP Proxy server port: 0

Test access with supplied credentials? [Y/n] 
Please wait, attempting to list all buckets...
Success. Your access key and secret key worked fine :-)

Now verifying that encryption works...
Not configured. Never mind.

Save settings? [y/N] y
Configuration saved to '/root/.s3cfg'

[root@client ~]# s3cmd ls
2022-05-14 07:03  s3://my-first-s3-bucket

[root@client ~]# sed -i '/signature_v2/s/False/True/g' /root/.s3cfg

# 创建bucket
[root@client ~]# s3cmd mb s3://s3cmd-demo                          
Bucket 's3://s3cmd-demo/' created

[root@client ~]# s3cmd ls
2022-05-14 07:03  s3://my-first-s3-bucket
2022-05-14 07:27  s3://s3cmd-demo
```

![ceph-s3-2](https://raw.githubusercontent.com/chengxinmu/Figure-bed/main/linux_base/ceph-s3-2.jpg)

```shell
# 上传文件
[root@client ~]# s3cmd put /etc/fstab s3://s3cmd-demo/fstab-demo
upload: '/etc/fstab' -> 's3://s3cmd-demo/fstab-demo'  [1 of 1]
 556 of 556   100% in    1s   390.96 B/s  done

# 上传1文件夹
[root@client ~]# s3cmd put /etc/ceph s3://s3cmd-demo/ --recursive
upload: '/etc/ceph/ceph.client.admin.keyring' -> 's3://s3cmd-demo/ceph/ceph.client.admin.keyring'  [1 of 5]
 151 of 151   100% in    0s     9.68 KB/s  done
upload: '/etc/ceph/ceph.client.client.keyring' -> 's3://s3cmd-demo/ceph/ceph.client.client.keyring'  [2 of 5]
 274 of 274   100% in    0s     5.62 KB/s  done
upload: '/etc/ceph/ceph.client.tony.keyring' -> 's3://s3cmd-demo/ceph/ceph.client.tony.keyring'  [3 of 5]
 123 of 123   100% in    0s     2.54 KB/s  done
upload: '/etc/ceph/ceph.conf' -> 's3://s3cmd-demo/ceph/ceph.conf'  [4 of 5]
 889 of 889   100% in    0s    17.99 KB/s  done
upload: '/etc/ceph/rbdmap' -> 's3://s3cmd-demo/ceph/rbdmap'  [5 of 5]
 92 of 92   100% in    0s  1887.22 B/s  done
 
# 查看bucket内容
[root@client ~]# s3cmd ls s3://s3cmd-demo/
                          DIR  s3://s3cmd-demo/ceph/
2022-05-14 07:30          556  s3://s3cmd-demo/fstab-demo
[root@client ~]# s3cmd ls s3://s3cmd-demo/ceph
                          DIR  s3://s3cmd-demo/ceph/
[root@client ~]# s3cmd ls s3://s3cmd-demo/ceph/
2022-05-14 07:41          151  s3://s3cmd-demo/ceph/ceph.client.admin.keyring
2022-05-14 07:41          274  s3://s3cmd-demo/ceph/ceph.client.client.keyring
2022-05-14 07:41          123  s3://s3cmd-demo/ceph/ceph.client.tony.keyring
2022-05-14 07:41          889  s3://s3cmd-demo/ceph/ceph.conf
2022-05-14 07:41           92  s3://s3cmd-demo/ceph/rbdmap

# 下载bucket上的文件
[root@client ~]# s3cmd get s3://s3cmd-demo/ceph/ceph.conf
download: 's3://s3cmd-demo/ceph/ceph.conf' -> './ceph.conf'  [1 of 1]
 889 of 889   100% in    0s    73.58 KB/s  done
[root@client ~]# ls
anaconda-ks.cfg             ceph.conf         client.client.key  original-ks.cfg
ceph.client.client.keyring  client.admin.key  hosts              s3.py

# 删除bucket上的文件
[root@client ~]# s3cmd del s3://s3cmd-demo/ceph/ceph.conf
delete: 's3://s3cmd-demo/ceph/ceph.conf'

[root@client ~]# s3cmd get s3://s3cmd-demo/ceph/ceph.conf
download: 's3://s3cmd-demo/ceph/ceph.conf' -> './ceph.conf'  [1 of 1]
ERROR: Download of './ceph.conf' failed (Reason: 404 (NoSuchKey))
ERROR: S3 error: 404 (NoSuchKey)

# 查看bucket上文件大小
[root@client ~]# s3cmd du -H s3://s3cmd-demo/ceph/
  640       4 objects s3://s3cmd-demo/ceph/
  
# 查看文件信息
[root@client ~]# s3cmd info s3://s3cmd-demo/ceph/ceph.client.admin.keyring
s3://s3cmd-demo/ceph/ceph.client.admin.keyring (object):
   File size: 151
   Last mod:  Sat, 14 May 2022 07:41:33 GMT
   MIME type: text/plain
   Storage:   STANDARD
   MD5 sum:   b75c4170be3d47d01feb9bb4ef4a6bdb
   SSE:       none
   Policy:    none
   CORS:      none
   ACL:       This is first rgw test user: FULL_CONTROL
   x-amz-meta-s3cmd-attrs: atime:1652461461/ctime:1652461304/gid:0/gname:root/md5:b75c4170be3d47d01feb9bb4ef4a6bdb/mode:33188/mtime:1652444867/uid:0/uname:root  
   
# 两个bucket之间拷贝文件
[root@client ~]# s3cmd cp --recursive s3://s3cmd-demo/ceph/ s3://my-first-s3-bucket/
remote copy: 's3://s3cmd-demo/ceph/ceph.client.admin.keyring' -> 's3://my-first-s3-bucket/ceph.client.admin.keyring'  [1 of 4]
remote copy: 's3://s3cmd-demo/ceph/ceph.client.client.keyring' -> 's3://my-first-s3-bucket/ceph.client.client.keyring'  [2 of 4]
remote copy: 's3://s3cmd-demo/ceph/ceph.client.tony.keyring' -> 's3://my-first-s3-bucket/ceph.client.tony.keyring'  [3 of 4]
remote copy: 's3://s3cmd-demo/ceph/rbdmap' -> 's3://my-first-s3-bucket/rbdmap'  [4 of 4]

# 列出需要同步的文件。并不同步。
[root@client ~]# s3cmd sync --dry-run /var/log/ s3://s3cmd-demo/
upload: '/var/log/anaconda/anaconda.log' -> 's3://s3cmd-demo/anaconda/anaconda.log'
upload: '/var/log/anaconda/ifcfg.log' -> 's3://s3cmd-demo/anaconda/ifcfg.log'
upload: '/var/log/anaconda/journal.log' -> 's3://s3cmd-demo/anaconda/journal.log'
upload: '/var/log/anaconda/ks-script-XadrjW.log' -> 's3://s3cmd-demo/anaconda/ks-script-XadrjW.log'
upload: '/var/log/anaconda/ks-script-zDw1Wo.log' -> 's3://s3cmd-demo/anaconda/ks-script-zDw1Wo.log'
WARNING: Exiting now because of --dry-run

s3cmd sync --delete-removed 源文件 s3://目的文件
同步时删除目的文件中  源文件没有的内容
```

#### 4.6.4、swift方式连接

```shell
# 安装对应的python模块  python-swiftclient setuptools 
[root@client ~]# pip3 list --format=columns
Package            Version  
------------------ ---------
boto               2.49.0   
certifi            2021.10.8
charset-normalizer 2.0.12   
idna               3.3      
pip                9.0.3    
python-swiftclient 3.13.1   
requests           2.27.1   
setuptools         39.2.0   
six                1.16.0   
urllib3            1.26.9   

# 创建rgwuser的子用户用于访问swift服务

[root@client ~]# sudo radosgw-admin subuser create --uid=rgwuser --subuser=rgwuser:swift --access=full
{
    "user_id": "rgwuser",
    "display_name": "This is first rgw test user",
    "email": "",
    "suspended": 0,
    "max_buckets": 1000,
    "subusers": [
        {
            "id": "rgwuser:swift",
            "permissions": "full-control"
        }
    ],
    "keys": [
        {
            "user": "rgwuser",
            "access_key": "EJIE1SDRWU8BE7LBVNNP",
            "secret_key": "1UvxuDmjs8JAfCFc67D2X7gUEnE0OoDZmAnF8LMz"
        }
    ],
    "swift_keys": [
        {
            "user": "rgwuser:swift",
            "secret_key": "GQGlRfk1LdGE0A1yavKkydLfR8jb8ORvPDHkKNgB"
        }
    ],
    "caps": [],
    "op_mask": "read, write, delete",
    "default_placement": "",
    "default_storage_class": "",
    "placement_tags": [],
    "bucket_quota": {
        "enabled": false,
        "check_on_raw": false,
        "max_size": -1,
        "max_size_kb": 0,
        "max_objects": -1
    },
    "user_quota": {
        "enabled": false,
        "check_on_raw": false,
        "max_size": -1,
        "max_size_kb": 0,
        "max_objects": -1
    },
    "temp_url_keys": [],
    "type": "rgw",
    "mfa_ids": []
}

# 环境变量配置
[root@client ~]# tail /etc/profile
...
export ST_AUTH=http://10.4.7.17:8080/auth 
export ST_USER=rgwuser:swift 
export ST_KEY=GQGlRfk1LdGE0A1yavKkydLfR8jb8ORvPDHkKNgB

[root@client ~]# source /etc/profile

[root@client ~]# swift list
my-first-s3-bucket
s3cmd-demo
[root@client ~]# swift list --lh
           4  640 2022-05-14 07:03:00 default-placement my-first-s3-bucket
           5 1.2K 2022-05-14 07:27:03 default-placement s3cmd-demo
          34 3.7M 2022-05-14 10:08:53 default-placement swift-demo
          43 3.7M

# 创建kucket
[root@client ~]# swift post swift-demo
[root@client ~]# swift list           
my-first-s3-bucket
s3cmd-demo
swift-demo

# 上传文件、文件夹
[root@client ~]# swift upload swift-demo /etc/fstab
etc/fstab
[root@client ~]# swift upload swift-demo /var/log
var/log/parallels-tools-install.log
var/log/boot.log-20220513
var/log/secure
var/log/tallylog
var/log/cron
var/log/firewalld
var/log/dmesg
var/log/grubby_prune_debug
var/log/btmp
var/log/dmesg.old
var/log/messages
var/log/boot.log

# 查看swift状态
[root@client ~]# swift stat
                                    Account: v1
                                 Containers: 3
                                    Objects: 43
                                      Bytes: 3876890
   Containers in policy "default-placement": 3
      Objects in policy "default-placement": 43
        Bytes in policy "default-placement": 3876890
Objects in policy "default-placement-bytes": 0
  Bytes in policy "default-placement-bytes": 0
                                X-Timestamp: 1652523211.51877
                X-Account-Bytes-Used-Actual: 3956736
                                 X-Trans-Id: tx000000000000000000188-00627f80cb-376b-default
                     X-Openstack-Request-Id: tx000000000000000000188-00627f80cb-376b-default
                              Accept-Ranges: bytes
                               Content-Type: text/plain; charset=utf-8
                                 Connection: Keep-Alive
                                 
# 切分文件上传
[root@client log]# swift upload swift-demo test.log -S 10240000
test.log segment 0
test.log segment 3
test.log segment 1
test.log segment 4
test.log segment 6
test.log segment 8
test.log segment 7
test.log segment 5
test.log segment 9
test.log segment 10
test.log segment 2
test.log

# 下载文件
[root@client ~]# swift download swift-demo test.log
test.log [auth 0.003s, headers 0.034s, total 0.377s, 279.900 MB/s]
[root@client ~]# ll
total 102424
-rw-------. 1 root root      3154 Apr 25 23:30 anaconda-ks.cfg
-rw-r--r--  1 root root        40 May 14 01:21 client.admin.key
-rw-r--r--  1 root root        40 May 14 01:18 client.client.key
-rw-r--r--  1 root root        59 May 14 00:36 hosts
-rw-------. 1 root root      2438 Apr 25 23:30 original-ks.cfg
-rw-r--r--  1 root root       570 May 14 15:02 s3.py
-rw-r--r--  1 root root 104857600 May 14 18:16 test.log

# swift 客户端日志
[root@client ~]# swift list --lh swift-demo_segments
9.8M 2022-05-14 10:28:29 application/swiftclient-segment test.log/1652523392.537454/104857600/10240000/00000000
9.8M 2022-05-14 10:28:30 application/swiftclient-segment test.log/1652523392.537454/104857600/10240000/00000001
9.8M 2022-05-14 10:28:30 application/swiftclient-segment test.log/1652523392.537454/104857600/10240000/00000002
9.8M 2022-05-14 10:28:30 application/swiftclient-segment test.log/1652523392.537454/104857600/10240000/00000003
9.8M 2022-05-14 10:28:30 application/swiftclient-segment test.log/1652523392.537454/104857600/10240000/00000004
9.8M 2022-05-14 10:28:30 application/swiftclient-segment test.log/1652523392.537454/104857600/10240000/00000005
9.8M 2022-05-14 10:28:30 application/swiftclient-segment test.log/1652523392.537454/104857600/10240000/00000006
9.8M 2022-05-14 10:28:30 application/swiftclient-segment test.log/1652523392.537454/104857600/10240000/00000007
9.8M 2022-05-14 10:28:30 application/swiftclient-segment test.log/1652523392.537454/104857600/10240000/00000008
9.8M 2022-05-14 10:28:30 application/swiftclient-segment test.log/1652523392.537454/104857600/10240000/00000009
2.3M 2022-05-14 10:28:30 application/swiftclient-segment test.log/1652523392.537454/104857600/10240000/00000010
100M

# 删除bucket
[root@client ~]# swift delete swift-demo
var/log/tuned/tuned.log
var/log/wtmp
var/log/yum.log
var/log/ceph/ceph-client.client.log
var/log/chrony
var/log/cron
var/log/dmesg
var/log/rhsm
var/log/secure
var/log/spooler
var/log/tallylog
etc/fstab
test.log
var/log/anaconda/anaconda.log
var/log/anaconda/ifcfg.log
var/log/anaconda/program.log
var/log/anaconda/storage.log
var/log/anaconda/syslog
var/log/audit/audit.log
var/log/boot.log
```

## 5、节点维护

### 5.1、osd节点维修

```shell
# osd状态查看
[root@kafka-1 ~]# ceph osd stat
7 osds: 7 up (since 97m), 7 in (since 45h); epoch: e1211

[root@kafka-1 ~]# ceph osd dump
epoch 1211
fsid 8a18f7c2-f1bd-44ac-8f18-8afbac8715c8
created 2022-05-09 23:26:49.925048
modified 2022-05-14 17:42:09.372242
flags sortbitwise,recovery_deletes,purged_snapdirs,pglog_hardlimit
crush_version 10
full_ratio 0.95
backfillfull_ratio 0.9
nearfull_ratio 0.85
require_min_compat_client jewel
min_compat_client jewel
require_osd_release nautilus
pool 1 'cephfs_data' replicated size 3 min_size 2 crush_rule 0 object_hash rjenkins pg_num 32 pgp_num 32 autoscale_mode on last_change 41 lfor 0/0/32 flags hashpspool stripe_width 0 application cephfs
pool 2 'cephfs_metadata' replicated size 3 min_size 2 crush_rule 0 object_hash rjenkins pg_num 8 pgp_num 8 autoscale_mode on last_change 42 flags hashpspool stripe_width 0 pg_autoscale_bias 4 pg_num_min 16 recovery_priority 5 application cephfs
pool 3 '.rgw.root' replicated size 3 min_size 2 crush_rule 0 object_hash rjenkins pg_num 32 pgp_num 32 autoscale_mode warn last_change 20 flags hashpspool stripe_width 0 application rgw
pool 4 'default.rgw.control' replicated size 3 min_size 2 crush_rule 0 object_hash rjenkins pg_num 32 pgp_num 32 autoscale_mode warn last_change 22 flags hashpspool stripe_width 0 application rgw
pool 5 'default.rgw.meta' replicated size 3 min_size 2 crush_rule 0 object_hash rjenkins pg_num 32 pgp_num 32 autoscale_mode warn last_change 24 flags hashpspool stripe_width 0 application rgw
pool 6 'default.rgw.log' replicated size 3 min_size 2 crush_rule 0 object_hash rjenkins pg_num 32 pgp_num 32 autoscale_mode warn last_change 26 flags hashpspool stripe_width 0 application rgw
pool 7 'pool-test1' replicated size 3 min_size 2 crush_rule 0 object_hash rjenkins pg_num 32 pgp_num 32 autoscale_mode on last_change 1186 lfor 0/980/978 flags hashpspool,selfmanaged_snaps stripe_width 0 application rbd
        removed_snaps [1~3]
pool 8 'pool-test2' erasure size 3 min_size 2 crush_rule 1 object_hash rjenkins pg_num 32 pgp_num 32 autoscale_mode warn last_change 72 flags hashpspool stripe_width 8192 compression_algorithm snappy compression_mode aggressive
pool 9 'cephtest-matadata' replicated size 3 min_size 2 crush_rule 0 object_hash rjenkins pg_num 32 pgp_num 32 autoscale_mode warn last_change 1190 flags hashpspool stripe_width 0
pool 10 'cephtest-data' replicated size 3 min_size 2 crush_rule 0 object_hash rjenkins pg_num 64 pgp_num 64 autoscale_mode warn last_change 1193 flags hashpspool stripe_width 0
pool 11 'default.rgw.buckets.index' replicated size 3 min_size 2 crush_rule 0 object_hash rjenkins pg_num 32 pgp_num 32 autoscale_mode warn last_change 1200 flags hashpspool stripe_width 0 application rgw
pool 12 'default.rgw.buckets.data' replicated size 3 min_size 2 crush_rule 0 object_hash rjenkins pg_num 32 pgp_num 32 autoscale_mode warn last_change 1203 flags hashpspool stripe_width 0 application rgw
max_osd 7
osd.0 up   in  weight 1 up_from 1181 up_thru 1210 down_at 1180 last_clean_interval [8,1180) [v2:10.4.7.18:6800/10896,v1:10.4.7.18:6801/10896] [v2:10.4.7.18:6808/9010896,v1:10.4.7.18:6809/9010896] exists,up e310c357-5815-4a8e-9bc1-87afc24ff476
osd.1 up   in  weight 1 up_from 1181 up_thru 1210 down_at 1180 last_clean_interval [8,1180) [v2:10.4.7.19:6800/10583,v1:10.4.7.19:6801/10583] [v2:10.4.7.19:6808/9010583,v1:10.4.7.19:6809/9010583] exists,up af281907-ea6c-4595-b739-53b42559febf
osd.2 up   in  weight 1 up_from 1206 up_thru 1210 down_at 1204 last_clean_interval [8,1205) [v2:10.4.7.17:6802/18864,v1:10.4.7.17:6803/18864] [v2:10.4.7.17:6804/16018864,v1:10.4.7.17:6805/16018864] exists,up 53a1fcc6-951d-4e35-bfd6-e9b214d09923
osd.3 up   in  weight 1 up_from 1196 up_thru 1210 down_at 1194 last_clean_interval [54,1195) [v2:10.4.7.21:6800/6772,v1:10.4.7.21:6801/6772] [v2:10.4.7.21:6808/9006772,v1:10.4.7.21:6809/9006772] exists,up fe0457cb-4e31-497d-b842-9be5cb631e17
osd.4 up   in  weight 1 up_from 1196 up_thru 1206 down_at 1194 last_clean_interval [54,1195) [v2:10.4.7.22:6800/7618,v1:10.4.7.22:6801/7618] [v2:10.4.7.22:6805/14007618,v1:10.4.7.22:6806/14007618] exists,up 43c240e5-57cb-482e-b67c-4441c3bd2024
osd.5 up   in  weight 1 up_from 1210 up_thru 1210 down_at 1208 last_clean_interval [54,1209) [v2:10.4.7.22:6808/8056,v1:10.4.7.22:6809/8056] [v2:10.4.7.22:6807/13008056,v1:10.4.7.22:6813/13008056] exists,up 3b692e5c-f82d-4292-b875-066730b118cf
osd.6 up   in  weight 1 up_from 1196 up_thru 1210 down_at 1194 last_clean_interval [1175,1195) [v2:10.4.7.25:6800/8210,v1:10.4.7.25:6801/8210] [v2:10.4.7.25:6808/1008210,v1:10.4.7.25:6809/1008210] exists,up 133a727f-f8f0-440f-bc4a-5365d89bf925

[root@kafka-1 ~]# ceph osd tree
ID  CLASS WEIGHT  TYPE NAME         STATUS REWEIGHT PRI-AFF 
 -1       0.28815 root default                              
-11       0.04880     host docker-1                         
  3   ssd 0.04880         osd.3         up  1.00000 1.00000 
 -9       0.08788     host docker-2                         
  4   ssd 0.04639         osd.4         up  1.00000 1.00000 
  5   ssd 0.04149         osd.5         up  1.00000 1.00000 
 -7       0.03909     host kafka-1                          
  2   ssd 0.03909         osd.2         up  1.00000 1.00000 
 -3       0.03909     host kafka-2                          
  1   ssd 0.03909         osd.1         up  1.00000 1.00000 
 -5       0.03909     host kafka-3                          
  0   ssd 0.03909         osd.0         up  1.00000 1.00000 
-13       0.03419     host osd                              
  6   ssd 0.03419         osd.6         up  1.00000 1.00000 
  
# 检测out状态的osd（本环境暂无）
[root@kafka-1 ~]# ceph osd tree out
ID CLASS WEIGHT TYPE NAME STATUS REWEIGHT PRI-AFF 

# 检查pg状态
[root@kafka-1 ~]# ceph pg dump

# 导出为json文件
[root@kafka-1 ~]# ceph pg dump -o PG.json --format=json
dumped all

[root@kafka-1 ~]# ls PG.json 
PG.json

# 查看id为6的节点的信息
[root@kafka-1 ~]# ceph osd find 6
{
    "osd": 6,
    "addrs": {
        "addrvec": [
            {
                "type": "v2",
                "addr": "10.4.7.25:6800",
                "nonce": 8210
            },
            {
                "type": "v1",
                "addr": "10.4.7.25:6801",
                "nonce": 8210
            }
        ]
    },
    "osd_fsid": "133a727f-f8f0-440f-bc4a-5365d89bf925",
    "host": "osd",
    "crush_location": {
        "host": "osd",
        "root": "default"
    }
}
```

```shell
# 删除节点
[root@kafka-1 ~]# ceph  osd out osd.6
marked out osd.6. 

[root@kafka-1 ~]# ceph osd crush remove osd.6
removed item id 6 name 'osd.6' from crush map

[root@kafka-1 ~]# ceph auth del osd.6
updated

# 停止节点ceph服务
[root@osd ~]# ps aux | grep ceph
root        6189  0.0  0.4 189152  9176 ?        Ss   06:19   0:00 /usr/bin/python2.7 /usr/bin/ceph-crash
ceph        8210  0.4  8.1 1113488 152340 ?      Ssl  06:41   3:11 /usr/bin/ceph-osd -f --cluster ceph --id 6 --setuser ceph --setgroup ceph
root       21612  0.0  0.0 112808   976 pts/1    R+   19:58   0:00 grep --color=auto ceph
[root@osd ~]# kill -9 8210
[root@osd ~]# ps aux | grep ceph
root        6189  0.0  0.4 189152  9176 ?        Ss   06:19   0:00 /usr/bin/python2.7 /usr/bin/ceph-crash
root       21673  0.0  0.0 112808   976 pts/1    R+   19:59   0:00 grep --color=auto ceph

[root@kafka-1 ~]# ceph  osd rm osd.6           
removed osd.6
```

![ceph-delete-osd](https://raw.githubusercontent.com/chengxinmu/Figure-bed/main/linux_base/ceph-delete-osd.jpg)

**节点重新加入集群**

```shell
# 清理节点上的数据
ps aux|grep ceph |awk '{print $2}'|xargs kill -9
ps -ef|grep ceph
umount /var/lib/ceph/osd/*
rm -rf /var/lib/ceph/osd/*
rm -rf /var/lib/ceph/mon/*
rm -rf /var/lib/ceph/mds/*
rm -rf /var/lib/ceph/bootstrap-mds/*
rm -rf /var/lib/ceph/bootstrap-osd/*
rm -rf /var/lib/ceph/bootstrap-rgw/*
rm -rf /var/lib/ceph/bootstrap-mgr/*
rm -rf /var/lib/ceph/tmp/*
rm -rf /etc/ceph/*
rm -rf /var/run/ceph/*

# 删除lvm、vg、pv


# 重新加入集群
[root@kafka-1 ceph-ansible-4.0.70]# ansible-playbook -vvv site.yml --limit osd

[root@kafka-1 ceph-ansible-4.0.70]# ceph -s
  cluster:
    id:     8a18f7c2-f1bd-44ac-8f18-8afbac8715c8
    health: HEALTH_OK
 
  services:
    mon: 2 daemons, quorum kafka-1,mha-manage (age 63m)
    mgr: kafka-1(active, since 2d), standbys: docker-2
    mds: cephfs:1 {0=kafka-3=up:active} 1 up:standby
    osd: 7 osds: 7 up (since 2m), 7 in (since 2m)
    rgw: 3 daemons active (kafka-1.rgw0, kafka-2.rgw0, kafka-3.rgw0)
 
  task status:
 
  data:
    pools:   12 pools, 392 pgs
    objects: 316 objects, 114 MiB
    usage:   147 GiB used, 148 GiB / 295 GiB avail
    pgs:     392 active+clean
    
[root@docker-2 ceph-5]# ceph osd find 6
{
    "osd": 6,
    "addrs": {
        "addrvec": [
            {
                "type": "v2",
                "addr": "10.4.7.25:6800",
                "nonce": 30544
            },
            {
                "type": "v1",
                "addr": "10.4.7.25:6801",
                "nonce": 30544
            }
        ]
    },
    "osd_fsid": "3dccef4a-5e49-4735-9dc7-cf8e1e53abdb",
    "host": "osd",
    "crush_location": {
        "host": "osd",
        "root": "default"
    }
}

[root@osd ceph-6]# ceph-volume lvm list


====== osd.6 =======

  [block]       /dev/ceph-2d5e1eef-2c7a-4f1c-aafb-d715f6e87aad/osd-block-3dccef4a-5e49-4735-9dc7-cf8e1e53abdb

      block device              /dev/ceph-2d5e1eef-2c7a-4f1c-aafb-d715f6e87aad/osd-block-3dccef4a-5e49-4735-9dc7-cf8e1e53abdb
      block uuid                8efxVH-05bk-KaE7-67Ze-uwc1-gA2S-YHPL1X
      cephx lockbox secret      
      cluster fsid              8a18f7c2-f1bd-44ac-8f18-8afbac8715c8
      cluster name              ceph
      crush device class        None
      db device                 /dev/ceph-cfcee7ce-37bf-4c88-92f4-997a0ac438a6/osd-db-66bd57f3-d964-42a5-9b0e-4662f02a528e
      db uuid                   wBezVq-5wOE-hOcp-Ak3n-3Enq-P26y-takTPG
      encrypted                 0
      osd fsid                  3dccef4a-5e49-4735-9dc7-cf8e1e53abdb
      osd id                    6
      osdspec affinity          
      type                      block
      vdo                       0
      devices                   /dev/sdb

  [db]          /dev/ceph-cfcee7ce-37bf-4c88-92f4-997a0ac438a6/osd-db-66bd57f3-d964-42a5-9b0e-4662f02a528e

      block device              /dev/ceph-2d5e1eef-2c7a-4f1c-aafb-d715f6e87aad/osd-block-3dccef4a-5e49-4735-9dc7-cf8e1e53abdb
      block uuid                8efxVH-05bk-KaE7-67Ze-uwc1-gA2S-YHPL1X
      cephx lockbox secret      
      cluster fsid              8a18f7c2-f1bd-44ac-8f18-8afbac8715c8
      cluster name              ceph
      crush device class        None
      db device                 /dev/ceph-cfcee7ce-37bf-4c88-92f4-997a0ac438a6/osd-db-66bd57f3-d964-42a5-9b0e-4662f02a528e
      db uuid                   wBezVq-5wOE-hOcp-Ak3n-3Enq-P26y-takTPG
      encrypted                 0
      osd fsid                  3dccef4a-5e49-4735-9dc7-cf8e1e53abdb
      osd id                    6
      osdspec affinity          
      type                      db
      vdo                       0
      devices                   /dev/sdc
      
[root@osd ceph-6]# ll
total 52
-rw-r--r-- 1 ceph ceph 320 May 14 22:14 activate.monmap
lrwxrwxrwx 1 ceph ceph  93 May 14 22:14 block -> /dev/ceph-2d5e1eef-2c7a-4f1c-aafb-d715f6e87aad/osd-block-3dccef4a-5e49-4735-9dc7-cf8e1e53abdb
lrwxrwxrwx 1 ceph ceph  90 May 14 22:14 block.db -> /dev/ceph-cfcee7ce-37bf-4c88-92f4-997a0ac438a6/osd-db-66bd57f3-d964-42a5-9b0e-4662f02a528e
-rw------- 1 ceph ceph   2 May 14 22:14 bluefs
-rw------- 1 ceph ceph  37 May 14 22:14 ceph_fsid
-rw-r--r-- 1 ceph ceph  37 May 14 22:14 fsid
-rw------- 1 ceph ceph  55 May 14 22:14 keyring
-rw------- 1 ceph ceph   8 May 14 22:14 kv_backend
-rw------- 1 ceph ceph  21 May 14 22:14 magic
-rw------- 1 ceph ceph   4 May 14 22:14 mkfs_done
-rw------- 1 ceph ceph  41 May 14 22:14 osd_key
-rw------- 1 ceph ceph   6 May 14 22:14 ready
-rw-r--r-- 1 root root   3 May 14 23:28 require_osd_release
-rw------- 1 ceph ceph  10 May 14 22:14 type
-rw------- 1 ceph ceph   2 May 14 22:14 whoami

[root@osd ceph-6]# systemctl status ceph-osd@6
● ceph-osd@6.service - Ceph object storage daemon osd.6
   Loaded: loaded (/usr/lib/systemd/system/ceph-osd@.service; enabled-runtime; vendor preset: disabled)
   Active: active (running) since Sat 2022-05-14 23:42:30 CST; 33min ago
 Main PID: 30544 (ceph-osd)
   CGroup: /system.slice/system-ceph\x2dosd.slice/ceph-osd@6.service
           └─30544 /usr/bin/ceph-osd -f --cluster ceph --id 6 --setuser ceph --setgroup ceph

May 14 23:42:53 osd systemd[1]: [/usr/lib/systemd/system/ceph-osd@.service:16] Unknown lvalue 'LockPersonality' in section 'Service'
May 14 23:42:53 osd systemd[1]: [/usr/lib/systemd/system/ceph-osd@.service:17] Unknown lvalue 'MemoryDenyWriteExecute' in section 'Service'
May 14 23:42:53 osd systemd[1]: [/usr/lib/systemd/system/ceph-osd@.service:20] Unknown lvalue 'ProtectControlGroups' in section 'Service'
May 14 23:42:53 osd systemd[1]: [/usr/lib/systemd/system/ceph-osd@.service:22] Unknown lvalue 'ProtectKernelModules' in section 'Service'
May 14 23:42:53 osd systemd[1]: [/usr/lib/systemd/system/ceph-osd@.service:24] Unknown lvalue 'ProtectKernelTunables' in section 'Service'
May 14 23:42:57 osd systemd[1]: [/usr/lib/systemd/system/ceph-osd@.service:16] Unknown lvalue 'LockPersonality' in section 'Service'
May 14 23:42:57 osd systemd[1]: [/usr/lib/systemd/system/ceph-osd@.service:17] Unknown lvalue 'MemoryDenyWriteExecute' in section 'Service'
May 14 23:42:57 osd systemd[1]: [/usr/lib/systemd/system/ceph-osd@.service:20] Unknown lvalue 'ProtectControlGroups' in section 'Service'
May 14 23:42:57 osd systemd[1]: [/usr/lib/systemd/system/ceph-osd@.service:22] Unknown lvalue 'ProtectKernelModules' in section 'Service'
May 14 23:42:57 osd systemd[1]: [/usr/lib/systemd/system/ceph-osd@.service:24] Unknown lvalue 'ProtectKernelTunables' in section 'Service'
Hint: Some lines were ellipsized, use -l to show in full.
[root@osd ceph-6]# ps aux | grep ceph
root       22013  0.0  0.4 189152  9176 ?        Ss   May14   0:00 /usr/bin/python2.7 /usr/bin/ceph-crash
ceph       30544  0.6 11.4 1336896 214884 ?      Ssl  May14   0:12 /usr/bin/ceph-osd -f --cluster ceph --id 6 --setuser ceph --setgroup cep
root       31033  0.0  0.0 112808   976 pts/1    R+   00:16   0:00 grep --color=auto ceph
```













