# MHA环境搭建

[报错参照](https://blog.csdn.net/qq_42935487/article/details/93378131)

| 主机名     | ip        | 角色   |
| ---------- | --------- | ------ |
| kafka-1    | 10.4.7.17 | Master |
| Kafka-2    | 10.4.7.19 | Slave  |
| Kafka-3    | 10.4.7.18 | Slave  |
| Mha-manage | 10.4.7.20 | Manage |

## 1、安装mysql

为简化过程，使用yum源方式安装

```shell
[root@kafka-1 ~]# yum install epel*  -y && yum clean all && yum makecache
[root@kafka-1 ~]# rpm -Uvh http://repo.mysql.com/mysql57-community-release-el7.rpm

# 删除gpgcheck
[root@kafka-2 ~]# sed "s/gpgcheck=1/gpgcheck=0/g" /etc/yum.repos.d/mysql-community.repo
[root@kafka-2 ~]# sed -i "s/gpgcheck=1/gpgcheck=0/g" /etc/yum.repos.d/mysql-community.repo

[root@kafka-1 ~]# yum install gcc gcc-c++ openssl-devel mysql mysql-server mysql-devel -y
[root@kafka-1 ~]# systemctl disable firewalld
systemctl stop firewalld
Removed symlink /etc/systemd/system/dbus-org.fedoraproject.FirewallD1.service.
Removed symlink /etc/systemd/system/multi-user.target.wants/firewalld.service.
[root@kafka-1 ~]# systemctl stop firewalld

[root@kafka-1 ~]# cat /var/log/mysqld.log | grep 'password is generated'
```

## 2、主从同步配置

```shell
[root@kafka-1 ~]# mysql -uroot -pTarena123..

# 所有节点执行
mysql> grant replication slave on *.* to 'repl_user'@'10.4.7.%' identified by 'Tarena123..';
Query OK, 0 rows affected, 1 warning (5.01 sec)

mysql> grant all on *.* to 'root'@'10.4.7.%' identified by 'Tarena123..'; 
Query OK, 0 rows affected, 1 warning (0.01 sec)

# slave 节点执行
mysql> set global read_only=1;
Query OK, 0 rows affected (0.00 sec)

change master to master_host='10.4.7.17',master_user='repl_user',master_password='Tarena123..',master_log_file='mysql-bin.000001',master_log_pos=736;

mysql> show slave status\G
*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
                  Master_Host: 10.4.7.17
                  Master_User: repl_user
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000001
          Read_Master_Log_Pos: 736
               Relay_Log_File: mysql-relay-bin.000002
                Relay_Log_Pos: 320
        Relay_Master_Log_File: mysql-bin.000001
             Slave_IO_Running: Yes
            Slave_SQL_Running: Yes
              Replicate_Do_DB: 
          Replicate_Ignore_DB: 
           Replicate_Do_Table: 
       Replicate_Ignore_Table: 
      Replicate_Wild_Do_Table: 
  Replicate_Wild_Ignore_Table: mysql.%,test.%,information_schema.%
                   Last_Errno: 0
                   Last_Error: 
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 736
              Relay_Log_Space: 527
              Until_Condition: None
               Until_Log_File: 
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File: 
           Master_SSL_CA_Path: 
              Master_SSL_Cert: 
            Master_SSL_Cipher: 
               Master_SSL_Key: 
        Seconds_Behind_Master: 0
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error: 
               Last_SQL_Errno: 0
               Last_SQL_Error: 
  Replicate_Ignore_Server_Ids: 
             Master_Server_Id: 1
                  Master_UUID: 667c64dd-c5c6-11ec-8ad1-001c42fe3274
             Master_Info_File: /var/lib/mysql/master.info
                    SQL_Delay: 0
          SQL_Remaining_Delay: NULL
      Slave_SQL_Running_State: Slave has read all relay log; waiting for more updates
           Master_Retry_Count: 86400
                  Master_Bind: 
      Last_IO_Error_Timestamp: 
     Last_SQL_Error_Timestamp: 
               Master_SSL_Crl: 
           Master_SSL_Crlpath: 
           Retrieved_Gtid_Set: 
            Executed_Gtid_Set: 66989614-c5c6-11ec-a568-001c42093853:1-2
                Auto_Position: 0
         Replicate_Rewrite_DB: 
                 Channel_Name: 
           Master_TLS_Version: 
1 row in set (0.01 sec)
```

## 3、相互免密配置

```shell
[root@kafka-1 ~]# ssh-keygen -t rsa -b 2048 -N '' -f /root/.ssh/id_rsa
Generating public/private rsa key pair.
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:/FakPVtueuzLBzOKOsPpK4DRygfSQdStK8vIpkoIQvI root@kafka-1
The key's randomart image is:
+---[RSA 2048]----+
| oo. .           |
|  . . .          |
|.o o .      .    |
|+.+ o  .   +     |
|ooE= .  S . + .  |
|+ = +    . . =+  |
|o+ + . . .o..oo+ |
|ooo   . =.. .+o .|
|*      o+=  .o+o |
+----[SHA256]-----+
[root@kafka-1 ~]# ssh-copy-id localhost
[root@kafka-1 ~]# scp .ssh/* kafka-2:/root/.ssh/
[root@kafka-1 ~]# scp .ssh/* kafka-3:/root/.ssh/
```

## 4、安装mhanode

### 4.1、四个节点都安装

```shell
[root@kafka-1 ~]# yum install perl-DBD-MySQL perl-Config-Tiny perl-Log-Dispatch perl-Parallel-ForkManager　-y

[root@kafka-3 ~]# wget https://github.com/yoshinorim/mha4mysql-node/releases/download/v0.58/mha4mysql-node-0.58-0.el7.centos.noarch.rpm

[root@kafka-3 ~]# rpm -ivh mha4mysql-node-0.58-0.el7.centos.noarch.rpm
```

### 4.2、管理节点安装

```shell
[root@mha-manage ~]# wget https://github.com/yoshinorim/mha4mysql-manager/releases/download/v0.58/mha4mysql-manager-0.58-0.el7.centos.noarch.rpm

[root@mha-manage ~]# yum -y localinstall mha4mysql-manager-0.58-0.el7.centos.noarch.rpm

[root@mha-manage ~]# cat /etc/masterha_default.cnf
[server default]
user=root   # mha监控用户
password=Tarena123..
ssh_user=root     # ssh远程用户
repl_user=repl_user    # 主从复制用户
repl_password=Tarena123..    # 主从复制用户密码
ping_interval=3
#master_binlog_dir= /var/lib/mysql,/var/log/mysql
secondary_check_script=masterha_secondary_check -s 10.4.7.17 -s 10.4.7.18 -s 10.4.7.19 
master_ip_failover_script="/etc/mha/scripts/master_ip_failover"
master_ip_online_change_script="/etc/mha/scripts/master_ip_online_change"
report_script="/etc/mha/scripts/send_report"


[root@mha-manage ~]# cat /etc/mha/app1.cnf
[server default]
manager_workdir=/var/log/mha/app1
manager_log=/var/log/mha/app1/manager.log

[server1]
hostname=kafka-1
candidate_master=1
master_binlog_dir="/var/lib/mysql"
#查看方式　find / -name mysql-bin*

[server2]
hostname=kafka-2
candidate_master=1
master_binlog_dir="/var/lib/mysql"

[server3]
hostname=kafka-3
master_binlog_dir="/var/lib/mysql"
#表示没有机会成为master
no_master=1

```

### 4.3、监控脚本定义

```shell
[root@mha-manage ~]# cat /etc/mha/scripts/master_ip_failover

#!/usr/bin/env perl
use strict;
use warnings FATAL => 'all';
use Getopt::Long;

my (
	$command,	$ssh_user,	$orig_master_host,
	$orig_master_ip,$orig_master_port, $new_master_host, $new_master_ip,$new_master_port
);

#定义VIP变量
my $vip = '10.4.7.20/24';
my $key = '1';
my $ssh_start_vip = "/sbin/ifconfig eth0:$key $vip";
my $ssh_stop_vip = "/sbin/ifconfig eth0:$key down";

GetOptions(
	'command=s'		=> \$command,
	'ssh_user=s'		=> \$ssh_user,
	'orig_master_host=s'	=> \$orig_master_host,
	'orig_master_ip=s'	=> \$orig_master_ip,
	'orig_master_port=i'	=> \$orig_master_port,
	'new_master_host=s'	=> \$new_master_host,
	'new_master_ip=s'	=> \$new_master_ip,
	'new_master_port=i'	=> \$new_master_port,
);

exit &main();

sub main {
	print "\n\nIN SCRIPT TEST====$ssh_stop_vip==$ssh_start_vip===\n\n";
	if ( $command eq "stop" || $command eq "stopssh" ) {
		my $exit_code = 1;
		eval {
			print "Disabling the VIP on old master: $orig_master_host \n";
			&stop_vip();
			$exit_code = 0;
		};
		if ($@) {
			warn "Got Error: $@\n";
			exit $exit_code;
		}
		exit $exit_code;
	}

	elsif ( $command eq "start" ) {
	my $exit_code = 10;
	eval {
		print "Enabling the VIP - $vip on the new master - $new_master_host \n";
		&start_vip();
		$exit_code = 0;
	};

	if ($@) {
		warn $@;
		exit $exit_code;
		}
	exit $exit_code;
	}

	elsif ( $command eq "status" ) {
		print "Checking the Status of the script.. OK \n";
		exit 0;
	}
	else {
		&usage();
		exit 1;
	}
}

sub start_vip() {
	`ssh $ssh_user\@$new_master_host \" $ssh_start_vip \"`;
}
sub stop_vip() {
	return 0 unless ($ssh_user);
	`ssh $ssh_user\@$orig_master_host \" $ssh_stop_vip \"`;
}
sub usage {
	print
	"Usage: master_ip_failover --command=start|stop|stopssh|status --orig_master_host=host --orig_master_ip=ip --orig_master_port=port --new_master_host=host --new_master_ip=ip --new_master_port=port\n";
}
```

```shell
[root@mha-manage ~]# cat /etc/mha/scripts/master_ip_online_change
#!/bin/bash
source /root/.bash_profile
vip=`echo '10.4.7.20/24'`  #设置VIP
key=`echo '1'`

command=`echo "$1" | awk -F = '{print $2}'`
orig_master_host=`echo "$2" | awk -F = '{print $2}'`
new_master_host=`echo "$7" | awk -F = '{print $2}'`
orig_master_ssh_user=`echo "${12}" | awk -F = '{print $2}'`
new_master_ssh_user=`echo "${13}" | awk -F = '{print $2}'`

#要求服务的网卡识别名一样，都为ens192(这里是)
stop_vip=`echo "ssh root@$orig_master_host /usr/sbin/ifconfig eth0:$key down"`
start_vip=`echo "ssh root@$new_master_host /usr/sbin/ifconfig eth0:$key $vip"`

if [ $command = 'stop' ]
         then
          echo -e "\n\n\n****************************\n"
           echo -e "Disabled thi VIP - $vip on old master: $orig_master_host \n"
           $stop_vip
           if [ $? -eq 0 ]
             then
                echo "Disabled the VIP successfully"
              else
                echo "Disabled the VIP failed"
            fi
            echo -e "***************************\n\n\n"
          fi

if [ $command = 'start' -o $command = 'status' ]
          then
            echo -e "\n\n\n*************************\n"
            echo -e "Enabling the VIP - $vip on new master: $new_master_host \n"
            $start_vip
            if [ $? -eq 0 ]
              then
                echo "Enabled the VIP successfully"
              else
                echo "Enabled the VIP failed"
           fi
           echo -e "***************************\n\n\n"
fi
```

```shell
[root@mha-manage ~]# cat /etc/mha/scripts/send_report
source /root/.bash_profile

# 解析变量
orig_master_host=`echo "$1" | awk -F = '{print $2}'`
new_master_host=`echo "$2" | awk -F = '{print $2}'`
new_slave_hosts=`echo "$3" | awk -F = '{print $2}'`
subject=`echo "$4" | awk -F = '{print $2}'`
body=`echo "$5" | awk -F = '{print $2}'`

#定义收件人地址
email="7**qq.com.com"
tac /var/log/mha/app1/manager.log | sed -n 2p | grep 'successfully' > /dev/null
if [ $? -eq 0 ]
        then
        messages=`echo -e "MHA $subject 主从切换成功\n master:$orig_master_host --> $new_master_host \n $body \n 当前从库:$new_slave_hosts"` 
        echo "$messages" | mail -s "Mysql 实例宕掉，MHA $subject 切换成功" $email >>/tmp/mailx.log 2>&1 
        else
        messages=`echo -e "MHA $subject 主从切换失败\n master:$orig_master_host --> $new_master_host \n $body" `
        echo "$messages" | mail -s ""Mysql 实例宕掉，MHA $subject 切换失败"" $email >>/tmp/mailx.log 2>&1  
fi
```

```shell
[root@kafka-3 ~]# chmod +x /etc/mha/scripts/*
```

### 4.4、前置状态检查

```shell
[root@kafka-3 ~]# masterha_check_ssh --conf=/etc/mha/app1.cnf
Wed Apr 27 04:39:15 2022 - [info] Reading default configuration from /etc/masterha_default.cnf..
Wed Apr 27 04:39:15 2022 - [info] Reading application default configuration from /etc/mha/app1.cnf..
Wed Apr 27 04:39:15 2022 - [info] Reading server configuration from /etc/mha/app1.cnf..
Wed Apr 27 04:39:15 2022 - [info] Starting SSH connection tests..
Wed Apr 27 04:39:17 2022 - [debug] 
Wed Apr 27 04:39:15 2022 - [debug]  Connecting via SSH from root@kafka-2(10.4.7.19:22) to root@kafka-1(10.4.7.17:22)..
Wed Apr 27 04:39:16 2022 - [debug]   ok.
Wed Apr 27 04:39:16 2022 - [debug]  Connecting via SSH from root@kafka-2(10.4.7.19:22) to root@kafka-3(10.4.7.18:22)..
Wed Apr 27 04:39:17 2022 - [debug]   ok.
Wed Apr 27 04:39:17 2022 - [debug] 
Wed Apr 27 04:39:15 2022 - [debug]  Connecting via SSH from root@kafka-1(10.4.7.17:22) to root@kafka-2(10.4.7.19:22)..
Wed Apr 27 04:39:15 2022 - [debug]   ok.
Wed Apr 27 04:39:15 2022 - [debug]  Connecting via SSH from root@kafka-1(10.4.7.17:22) to root@kafka-3(10.4.7.18:22)..
Wed Apr 27 04:39:16 2022 - [debug]   ok.
Wed Apr 27 04:39:17 2022 - [debug] 
Wed Apr 27 04:39:16 2022 - [debug]  Connecting via SSH from root@kafka-3(10.4.7.18:22) to root@kafka-1(10.4.7.17:22)..
Wed Apr 27 04:39:16 2022 - [debug]   ok.
Wed Apr 27 04:39:16 2022 - [debug]  Connecting via SSH from root@kafka-3(10.4.7.18:22) to root@kafka-2(10.4.7.19:22)..
Wed Apr 27 04:39:17 2022 - [debug]   ok.
Wed Apr 27 04:39:17 2022 - [info] All SSH connection tests passed successfully.

```

```shell
[root@kafka-3 ~]# masterha_check_repl --conf=/etc/mha/app1.cnf
Wed Apr 27 04:22:57 2022 - [info] Reading default configuration from /etc/masterha_default.cnf..
Wed Apr 27 04:22:57 2022 - [info] Reading application default configuration from /etc/mha/app1.cnf..
Wed Apr 27 04:22:57 2022 - [info] Reading server configuration from /etc/mha/app1.cnf..
Wed Apr 27 04:22:57 2022 - [info] MHA::MasterMonitor version 0.58.
Wed Apr 27 04:22:59 2022 - [info] GTID failover mode = 1
Wed Apr 27 04:22:59 2022 - [info] Dead Servers:
Wed Apr 27 04:22:59 2022 - [info] Alive Servers:
Wed Apr 27 04:22:59 2022 - [info]   kafka-1(10.4.7.17:3306)
Wed Apr 27 04:22:59 2022 - [info]   kafka-2(10.4.7.19:3306)
Wed Apr 27 04:22:59 2022 - [info]   kafka-3(10.4.7.18:3306)
Wed Apr 27 04:22:59 2022 - [info] Alive Slaves:
Wed Apr 27 04:22:59 2022 - [info]   kafka-2(10.4.7.19:3306)  Version=5.7.38-log (oldest major version between slaves) log-bin:enabled
Wed Apr 27 04:22:59 2022 - [info]     GTID ON
Wed Apr 27 04:22:59 2022 - [info]     Replicating from 10.4.7.17(10.4.7.17:3306)
Wed Apr 27 04:22:59 2022 - [info]     Primary candidate for the new Master (candidate_master is set)
Wed Apr 27 04:22:59 2022 - [info]   kafka-3(10.4.7.18:3306)  Version=5.7.38-log (oldest major version between slaves) log-bin:enabled
Wed Apr 27 04:22:59 2022 - [info]     GTID ON
Wed Apr 27 04:22:59 2022 - [info]     Replicating from 10.4.7.17(10.4.7.17:3306)
Wed Apr 27 04:22:59 2022 - [info]     Not candidate for the new Master (no_master is set)
Wed Apr 27 04:22:59 2022 - [info] Current Alive Master: kafka-1(10.4.7.17:3306)
Wed Apr 27 04:22:59 2022 - [info] Checking slave configurations..
Wed Apr 27 04:22:59 2022 - [info] Checking replication filtering settings..
Wed Apr 27 04:22:59 2022 - [info]  binlog_do_db= , binlog_ignore_db= 
Wed Apr 27 04:22:59 2022 - [info]  Replication filtering check ok.
Wed Apr 27 04:22:59 2022 - [info] GTID (with auto-pos) is supported. Skipping all SSH and Node package checking.
Wed Apr 27 04:22:59 2022 - [info] Checking SSH publickey authentication settings on the current master..
Wed Apr 27 04:22:59 2022 - [info] HealthCheck: SSH to kafka-1 is reachable.
Wed Apr 27 04:22:59 2022 - [info] 
kafka-1(10.4.7.17:3306) (current master)
 +--kafka-2(10.4.7.19:3306)
 +--kafka-3(10.4.7.18:3306)

Wed Apr 27 04:22:59 2022 - [info] Checking replication health on kafka-2..
Wed Apr 27 04:22:59 2022 - [info]  ok.
Wed Apr 27 04:22:59 2022 - [info] Checking replication health on kafka-3..
Wed Apr 27 04:22:59 2022 - [info]  ok.
Wed Apr 27 04:22:59 2022 - [info] Checking master_ip_failover_script status:
Wed Apr 27 04:22:59 2022 - [info]   /etc/mha/scripts/master_ip_failover --command=status --ssh_user=root --orig_master_host=kafka-1 --orig_master_ip=10.4.7.17 --orig_master_port=3306 


IN SCRIPT TEST====/sbin/ifconfig eth0:1 down==/sbin/ifconfig eth0:1 10.4.7.20/24===

Checking the Status of the script.. OK 
Wed Apr 27 04:22:59 2022 - [info]  OK.
Wed Apr 27 04:22:59 2022 - [warning] shutdown_script is not defined.
Wed Apr 27 04:22:59 2022 - [info] Got exit code 0 (Not master dead).

MySQL Replication Health is OK.
```

### 4.5、启动MHA

```shell
[root@kafka-1 ~]# /usr/sbin/ifconfig eth0:1 10.4.7.100/24

[root@mha-manage ~]# mkdir /var/log/mha/app1 -p
[root@mha-manage ~]# touch /var/log/mha/app1/manager.log
[root@mha-manage ~]# nohup masterha_manager --conf=/etc/mha/app1.cnf --remove_dead_master_conf --ignore_last_failover < /dev/null > /var/log/mha/app1/manager.log 2>&1 &
[1] 22643

[root@kafka-3 ~]# tailf /var/log/mha/app1/manager.log

IN SCRIPT TEST====/sbin/ifconfig eth0:1 down==/sbin/ifconfig eth0:1 10.4.7.20/24===

Checking the Status of the script.. OK 
Wed Apr 27 04:25:37 2022 - [info]  OK.
Wed Apr 27 04:25:37 2022 - [warning] shutdown_script is not defined.
Wed Apr 27 04:25:37 2022 - [info] Set master ping interval 3 seconds.
Wed Apr 27 04:25:37 2022 - [info] Set secondary check script: masterha_secondary_check -s 110.4.7.17 -s 10.4.7.18 -s 10.4.7.19
Wed Apr 27 04:25:37 2022 - [info] Starting ping health check on kafka-1(10.4.7.17:3306)..
Wed Apr 27 04:25:37 2022 - [info] Ping(SELECT) succeeded, waiting until MySQL doesn't respond..
^C
[root@mha-manage ~]# masterha_check_status --conf=/etc/mha/app1.cnf
app1 (pid:22643) is running(0:PING_OK), master:kafka-1

```

### 4.6、停止master节点测试

```shell
Fri Apr 29 04:18:44 2022 - [warning] Master is not reachable from health checker!
Fri Apr 29 04:18:44 2022 - [warning] Master kafka-1(10.4.7.17:3306) is not reachable!
Fri Apr 29 04:18:44 2022 - [warning] SSH is reachable.
Fri Apr 29 04:18:44 2022 - [info] Connecting to a master server failed. Reading configuration file /etc/masterha_default.cnf and /etc/mha/app1.cnf again, and trying to connect to all servers to check server status..
Fri Apr 29 04:18:44 2022 - [info] Reading default configuration from /etc/masterha_default.cnf..
Fri Apr 29 04:18:44 2022 - [info] Reading application default configuration from /etc/mha/app1.cnf..
Fri Apr 29 04:18:44 2022 - [info] Reading server configuration from /etc/mha/app1.cnf..
Fri Apr 29 04:18:45 2022 - [info] GTID failover mode = 1
Fri Apr 29 04:18:45 2022 - [info] Dead Servers:
Fri Apr 29 04:18:45 2022 - [info]   kafka-1(10.4.7.17:3306)
Fri Apr 29 04:18:45 2022 - [info] Alive Servers:
Fri Apr 29 04:18:45 2022 - [info]   kafka-2(10.4.7.19:3306)
Fri Apr 29 04:18:45 2022 - [info]   kafka-3(10.4.7.18:3306)
Fri Apr 29 04:18:45 2022 - [info] Alive Slaves:
Fri Apr 29 04:18:45 2022 - [info]   kafka-2(10.4.7.19:3306)  Version=5.7.38-log (oldest major version between slaves) log-bin:enabled
Fri Apr 29 04:18:45 2022 - [info]     GTID ON
Fri Apr 29 04:18:45 2022 - [info]     Replicating from 10.4.7.17(10.4.7.17:3306)
Fri Apr 29 04:18:45 2022 - [info]     Primary candidate for the new Master (candidate_master is set)
Fri Apr 29 04:18:45 2022 - [info]   kafka-3(10.4.7.18:3306)  Version=5.7.38-log (oldest major version between slaves) log-bin:enabled
Fri Apr 29 04:18:45 2022 - [info]     GTID ON
Fri Apr 29 04:18:45 2022 - [info]     Replicating from 10.4.7.17(10.4.7.17:3306)
Fri Apr 29 04:18:45 2022 - [info]     Primary candidate for the new Master (candidate_master is set)
Fri Apr 29 04:18:45 2022 - [info] Checking slave configurations..
Fri Apr 29 04:18:45 2022 - [info]  read_only=1 is not set on slave kafka-3(10.4.7.18:3306).
Fri Apr 29 04:18:45 2022 - [info] Checking replication filtering settings..
Fri Apr 29 04:18:45 2022 - [info]  Replication filtering check ok.
Fri Apr 29 04:18:45 2022 - [info] Master is down!
Fri Apr 29 04:18:45 2022 - [info] Terminating monitoring script.
Fri Apr 29 04:18:45 2022 - [info] Got exit code 20 (Master dead).
Fri Apr 29 04:18:45 2022 - [info] MHA::MasterFailover version 0.58.
Fri Apr 29 04:18:45 2022 - [info] Starting master failover.
Fri Apr 29 04:18:45 2022 - [info] 
Fri Apr 29 04:18:45 2022 - [info] * Phase 1: Configuration Check Phase..
Fri Apr 29 04:18:45 2022 - [info] 
Fri Apr 29 04:18:46 2022 - [info] GTID failover mode = 1
Fri Apr 29 04:18:46 2022 - [info] Dead Servers:
Fri Apr 29 04:18:46 2022 - [info]   kafka-1(10.4.7.17:3306)
Fri Apr 29 04:18:46 2022 - [info] Checking master reachability via MySQL(double check)...
Fri Apr 29 04:18:46 2022 - [info]  ok.
Fri Apr 29 04:18:46 2022 - [info] Alive Servers:
Fri Apr 29 04:18:46 2022 - [info]   kafka-2(10.4.7.19:3306)
Fri Apr 29 04:18:46 2022 - [info]   kafka-3(10.4.7.18:3306)
Fri Apr 29 04:18:46 2022 - [info] Alive Slaves:
Fri Apr 29 04:18:46 2022 - [info]   kafka-2(10.4.7.19:3306)  Version=5.7.38-log (oldest major version between slaves) log-bin:enabled
Fri Apr 29 04:18:46 2022 - [info]     GTID ON
Fri Apr 29 04:18:46 2022 - [info]     Replicating from 10.4.7.17(10.4.7.17:3306)
Fri Apr 29 04:18:46 2022 - [info]     Primary candidate for the new Master (candidate_master is set)
Fri Apr 29 04:18:46 2022 - [info]   kafka-3(10.4.7.18:3306)  Version=5.7.38-log (oldest major version between slaves) log-bin:enabled
Fri Apr 29 04:18:46 2022 - [info]     GTID ON
Fri Apr 29 04:18:46 2022 - [info]     Replicating from 10.4.7.17(10.4.7.17:3306)
Fri Apr 29 04:18:46 2022 - [info]     Primary candidate for the new Master (candidate_master is set)
Fri Apr 29 04:18:46 2022 - [info] Starting GTID based failover.
Fri Apr 29 04:18:46 2022 - [info] 
Fri Apr 29 04:18:46 2022 - [info] ** Phase 1: Configuration Check Phase completed.
Fri Apr 29 04:18:46 2022 - [info] 
Fri Apr 29 04:18:46 2022 - [info] * Phase 2: Dead Master Shutdown Phase..
Fri Apr 29 04:18:46 2022 - [info] 
Fri Apr 29 04:18:46 2022 - [info] Forcing shutdown so that applications never connect to the current master..
Fri Apr 29 04:18:46 2022 - [info] Executing master IP deactivation script:
Fri Apr 29 04:18:46 2022 - [info]   /etc/mha/scripts/master_ip_failover --orig_master_host=kafka-1 --orig_master_ip=10.4.7.17 --orig_master_port=3306 --command=stopssh --ssh_user=root  


IN SCRIPT TEST====/sbin/ifconfig eth0:1 down==/sbin/ifconfig eth0:1 10.4.7.100/24;/sbin/arping -I eth0 -c 3 -s 10.4.7.100/24 >/dev/null 2>&1===

Disabling the VIP on old master: kafka-1 
Fri Apr 29 04:18:46 2022 - [info]  done.
Fri Apr 29 04:18:46 2022 - [warning] shutdown_script is not set. Skipping explicit shutting down of the dead master.
Fri Apr 29 04:18:46 2022 - [info] * Phase 2: Dead Master Shutdown Phase completed.
Fri Apr 29 04:18:46 2022 - [info] 
Fri Apr 29 04:18:46 2022 - [info] * Phase 3: Master Recovery Phase..
Fri Apr 29 04:18:46 2022 - [info] 
Fri Apr 29 04:18:46 2022 - [info] * Phase 3.1: Getting Latest Slaves Phase..
Fri Apr 29 04:18:46 2022 - [info] 
Fri Apr 29 04:18:46 2022 - [info] The latest binary log file/position on all slaves is mysql-bin.000011:194
Fri Apr 29 04:18:46 2022 - [info] Retrieved Gtid Set: 667c64dd-c5c6-11ec-8ad1-001c42fe3274:3
Fri Apr 29 04:18:46 2022 - [info] Latest slaves (Slaves that received relay log files to the latest):
Fri Apr 29 04:18:46 2022 - [info]   kafka-2(10.4.7.19:3306)  Version=5.7.38-log (oldest major version between slaves) log-bin:enabled
Fri Apr 29 04:18:46 2022 - [info]     GTID ON
Fri Apr 29 04:18:46 2022 - [info]     Replicating from 10.4.7.17(10.4.7.17:3306)
Fri Apr 29 04:18:46 2022 - [info]     Primary candidate for the new Master (candidate_master is set)
Fri Apr 29 04:18:46 2022 - [info]   kafka-3(10.4.7.18:3306)  Version=5.7.38-log (oldest major version between slaves) log-bin:enabled
Fri Apr 29 04:18:46 2022 - [info]     GTID ON
Fri Apr 29 04:18:46 2022 - [info]     Replicating from 10.4.7.17(10.4.7.17:3306)
Fri Apr 29 04:18:46 2022 - [info]     Primary candidate for the new Master (candidate_master is set)
Fri Apr 29 04:18:46 2022 - [info] The oldest binary log file/position on all slaves is mysql-bin.000011:194
Fri Apr 29 04:18:46 2022 - [info] Retrieved Gtid Set: 667c64dd-c5c6-11ec-8ad1-001c42fe3274:3
Fri Apr 29 04:18:46 2022 - [info] Oldest slaves:
Fri Apr 29 04:18:46 2022 - [info]   kafka-2(10.4.7.19:3306)  Version=5.7.38-log (oldest major version between slaves) log-bin:enabled
Fri Apr 29 04:18:46 2022 - [info]     GTID ON
Fri Apr 29 04:18:46 2022 - [info]     Replicating from 10.4.7.17(10.4.7.17:3306)
Fri Apr 29 04:18:46 2022 - [info]     Primary candidate for the new Master (candidate_master is set)
Fri Apr 29 04:18:46 2022 - [info]   kafka-3(10.4.7.18:3306)  Version=5.7.38-log (oldest major version between slaves) log-bin:enabled
Fri Apr 29 04:18:46 2022 - [info]     GTID ON
Fri Apr 29 04:18:46 2022 - [info]     Replicating from 10.4.7.17(10.4.7.17:3306)
Fri Apr 29 04:18:46 2022 - [info]     Primary candidate for the new Master (candidate_master is set)
Fri Apr 29 04:18:46 2022 - [info] 
Fri Apr 29 04:18:46 2022 - [info] * Phase 3.3: Determining New Master Phase..
Fri Apr 29 04:18:46 2022 - [info] 
Fri Apr 29 04:18:46 2022 - [info] Searching new master from slaves..
Fri Apr 29 04:18:46 2022 - [info]  Candidate masters from the configuration file:
Fri Apr 29 04:18:46 2022 - [info]   kafka-2(10.4.7.19:3306)  Version=5.7.38-log (oldest major version between slaves) log-bin:enabled
Fri Apr 29 04:18:46 2022 - [info]     GTID ON
Fri Apr 29 04:18:46 2022 - [info]     Replicating from 10.4.7.17(10.4.7.17:3306)
Fri Apr 29 04:18:46 2022 - [info]     Primary candidate for the new Master (candidate_master is set)
Fri Apr 29 04:18:46 2022 - [info]   kafka-3(10.4.7.18:3306)  Version=5.7.38-log (oldest major version between slaves) log-bin:enabled
Fri Apr 29 04:18:46 2022 - [info]     GTID ON
Fri Apr 29 04:18:46 2022 - [info]     Replicating from 10.4.7.17(10.4.7.17:3306)
Fri Apr 29 04:18:46 2022 - [info]     Primary candidate for the new Master (candidate_master is set)
Fri Apr 29 04:18:46 2022 - [info]  Non-candidate masters:
Fri Apr 29 04:18:46 2022 - [info]  Searching from candidate_master slaves which have received the latest relay log events..
Fri Apr 29 04:18:46 2022 - [info] New master is kafka-2(10.4.7.19:3306)
Fri Apr 29 04:18:46 2022 - [info] Starting master failover..
Fri Apr 29 04:18:46 2022 - [info] 
From:
kafka-1(10.4.7.17:3306) (current master)
 +--kafka-2(10.4.7.19:3306)
 +--kafka-3(10.4.7.18:3306)

To:
kafka-2(10.4.7.19:3306) (new master)
 +--kafka-3(10.4.7.18:3306)
Fri Apr 29 04:18:46 2022 - [info] 
Fri Apr 29 04:18:46 2022 - [info] * Phase 3.3: New Master Recovery Phase..
Fri Apr 29 04:18:46 2022 - [info] 
Fri Apr 29 04:18:46 2022 - [info]  Waiting all logs to be applied.. 
Fri Apr 29 04:18:46 2022 - [info]   done.
Fri Apr 29 04:18:46 2022 - [info] Getting new master's binlog name and position..
Fri Apr 29 04:18:46 2022 - [info]  mysql-bin.000001:901
Fri Apr 29 04:18:46 2022 - [info]  All other slaves should start replication from here. Statement should be: CHANGE MASTER TO MASTER_HOST='kafka-2 or 10.4.7.19', MASTER_PORT=3306, MASTER_AUTO_POSITION=1, MASTER_USER='repl_user', MASTER_PASSWORD='xxx';
Fri Apr 29 04:18:46 2022 - [info] Master Recovery succeeded. File:Pos:Exec_Gtid_Set: mysql-bin.000001, 901, 667c64dd-c5c6-11ec-8ad1-001c42fe3274:3,
66989614-c5c6-11ec-a568-001c42093853:1-2
Fri Apr 29 04:18:46 2022 - [info] Executing master IP activate script:
Fri Apr 29 04:18:46 2022 - [info]   /etc/mha/scripts/master_ip_failover --command=start --ssh_user=root --orig_master_host=kafka-1 --orig_master_ip=10.4.7.17 --orig_master_port=3306 --new_master_host=kafka-2 --new_master_ip=10.4.7.19 --new_master_port=3306 --new_master_user='root'   --new_master_password=xxx
Unknown option: new_master_user
Unknown option: new_master_password


IN SCRIPT TEST====/sbin/ifconfig eth0:1 down==/sbin/ifconfig eth0:1 10.4.7.100/24;/sbin/arping -I eth0 -c 3 -s 10.4.7.100/24 >/dev/null 2>&1===

Enabling the VIP - 10.4.7.100/24 on the new master - kafka-2 
Fri Apr 29 04:18:46 2022 - [info]  OK.
Fri Apr 29 04:18:46 2022 - [info] Setting read_only=0 on kafka-2(10.4.7.19:3306)..
Fri Apr 29 04:18:46 2022 - [info]  ok.
Fri Apr 29 04:18:46 2022 - [info] ** Finished master recovery successfully.
Fri Apr 29 04:18:46 2022 - [info] * Phase 3: Master Recovery Phase completed.
Fri Apr 29 04:18:46 2022 - [info] 
Fri Apr 29 04:18:46 2022 - [info] * Phase 4: Slaves Recovery Phase..
Fri Apr 29 04:18:46 2022 - [info] 
Fri Apr 29 04:18:46 2022 - [info] 
Fri Apr 29 04:18:46 2022 - [info] * Phase 4.1: Starting Slaves in parallel..
Fri Apr 29 04:18:46 2022 - [info] 
Fri Apr 29 04:18:46 2022 - [info] -- Slave recovery on host kafka-3(10.4.7.18:3306) started, pid: 16432. Check tmp log /var/log/mha/app1/kafka-3_3306_20220429041845.log if it takes time..
Fri Apr 29 04:18:47 2022 - [info] 
Fri Apr 29 04:18:47 2022 - [info] Log messages from kafka-3 ...
Fri Apr 29 04:18:47 2022 - [info] 
Fri Apr 29 04:18:46 2022 - [info]  Resetting slave kafka-3(10.4.7.18:3306) and starting replication from the new master kafka-2(10.4.7.19:3306)..
Fri Apr 29 04:18:46 2022 - [info]  Executed CHANGE MASTER.
Fri Apr 29 04:18:46 2022 - [info]  Slave started.
Fri Apr 29 04:18:46 2022 - [info]  gtid_wait(667c64dd-c5c6-11ec-8ad1-001c42fe3274:3,
66989614-c5c6-11ec-a568-001c42093853:1-2) completed on kafka-3(10.4.7.18:3306). Executed 3 events.
Fri Apr 29 04:18:47 2022 - [info] End of log messages from kafka-3.
Fri Apr 29 04:18:47 2022 - [info] -- Slave on host kafka-3(10.4.7.18:3306) started.
Fri Apr 29 04:18:47 2022 - [info] All new slave servers recovered successfully.
Fri Apr 29 04:18:47 2022 - [info] 
Fri Apr 29 04:18:47 2022 - [info] * Phase 5: New master cleanup phase..
Fri Apr 29 04:18:47 2022 - [info] 
Fri Apr 29 04:18:47 2022 - [info] Resetting slave info on the new master..
Fri Apr 29 04:18:47 2022 - [info]  kafka-2: Resetting slave info succeeded.
Fri Apr 29 04:18:47 2022 - [info] Master failover to kafka-2(10.4.7.19:3306) completed successfully.
Fri Apr 29 04:18:47 2022 - [info] Deleted server1 entry from /etc/mha/app1.cnf .
Fri Apr 29 04:18:47 2022 - [info] 

----- Failover Report -----

app1: MySQL Master failover kafka-1(10.4.7.17:3306) to kafka-2(10.4.7.19:3306) succeeded

Master kafka-1(10.4.7.17:3306) is down!

Check MHA Manager logs at mha-manage:/var/log/mha/app1/manager.log for details.

Started automated(non-interactive) failover.
Invalidated master IP address on kafka-1(10.4.7.17:3306)
Selected kafka-2(10.4.7.19:3306) as a new master.
kafka-2(10.4.7.19:3306): OK: Applying all logs succeeded.
kafka-2(10.4.7.19:3306): OK: Activated master IP address.
kafka-3(10.4.7.18:3306): OK: Slave started, replicating from kafka-2(10.4.7.19:3306)
kafka-2(10.4.7.19:3306): Resetting slave info succeeded.
Master failover to kafka-2(10.4.7.19:3306) completed successfully.

[root@kafka-2 ~]# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 00:1c:42:09:38:53 brd ff:ff:ff:ff:ff:ff
    inet 10.4.7.19/24 brd 10.4.7.255 scope global noprefixroute dynamic eth0
       valid_lft 1262sec preferred_lft 1262sec
    inet 10.4.7.100/24 brd 10.4.7.255 scope global secondary eth0:1
       valid_lft forever preferred_lft forever
    inet6 fdb2:2c26:f4e4:0:21c:42ff:fe09:3853/64 scope global noprefixroute dynamic 
       valid_lft 2591739sec preferred_lft 604539sec
    inet6 fe80::21c:42ff:fe09:3853/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
```

### 4.7、重新加入master节点

```shell
[root@mha-manage mha]# cat app1.cnf
[server default]
manager_log=/var/log/mha/app1/manager.log
manager_workdir=/var/log/mha/app1

[server2]
candidate_master=1
hostname=kafka-2
master_binlog_dir="/var/lib/mysql"
port=3306

[server3]
candidate_master=1
hostname=kafka-3
master_binlog_dir="/var/lib/mysql"
port=3306

[server1]
candidate_master=1
hostname=kafka-1
master_binlog_dir="/var/lib/mysql"
port=3306

# old master节点重新加入并指定新master为主。

# 切换后mha监控进程会退出

[root@mha-manage mha]# masterha_check_status --conf=/etc/mha/app1.cnf                            app1 is stopped(2:NOT_RUNNING).
[root@mha-manage mha]# nohup masterha_manager --conf=/etc/mha/app1.cnf --remove_dead_master_conf --ignore_last_failover < /dev/null > /var/log/mha/app1/manager.log 2>&1 &
[1] 16638

[root@mha-manage mha]# masterha_check_repl --conf=/etc/mha/app1.cnf
Fri Apr 29 04:57:39 2022 - [info] Reading default configuration from /etc/masterha_default.cnf..
Fri Apr 29 04:57:39 2022 - [info] Reading application default configuration from /etc/mha/app1.cnf..
Fri Apr 29 04:57:39 2022 - [info] Reading server configuration from /etc/mha/app1.cnf..
Fri Apr 29 04:57:39 2022 - [info] MHA::MasterMonitor version 0.58.
Fri Apr 29 04:57:40 2022 - [info] GTID failover mode = 1
Fri Apr 29 04:57:40 2022 - [info] Dead Servers:
Fri Apr 29 04:57:40 2022 - [info] Alive Servers:
Fri Apr 29 04:57:40 2022 - [info]   kafka-1(10.4.7.17:3306)
Fri Apr 29 04:57:40 2022 - [info]   kafka-2(10.4.7.19:3306)
Fri Apr 29 04:57:40 2022 - [info]   kafka-3(10.4.7.18:3306)
Fri Apr 29 04:57:40 2022 - [info] Alive Slaves:
Fri Apr 29 04:57:40 2022 - [info]   kafka-1(10.4.7.17:3306)  Version=5.7.38-log (oldest major version between slaves) log-bin:enabled
Fri Apr 29 04:57:40 2022 - [info]     GTID ON
Fri Apr 29 04:57:40 2022 - [info]     Replicating from 10.4.7.19(10.4.7.19:3306)
Fri Apr 29 04:57:40 2022 - [info]     Primary candidate for the new Master (candidate_master is set)
Fri Apr 29 04:57:40 2022 - [info]   kafka-3(10.4.7.18:3306)  Version=5.7.38-log (oldest major version between slaves) log-bin:enabled
Fri Apr 29 04:57:40 2022 - [info]     GTID ON
Fri Apr 29 04:57:40 2022 - [info]     Replicating from 10.4.7.19(10.4.7.19:3306)
Fri Apr 29 04:57:40 2022 - [info]     Primary candidate for the new Master (candidate_master is set)
Fri Apr 29 04:57:40 2022 - [info] Current Alive Master: kafka-2(10.4.7.19:3306)
Fri Apr 29 04:57:40 2022 - [info] Checking slave configurations..
Fri Apr 29 04:57:40 2022 - [info]  read_only=1 is not set on slave kafka-1(10.4.7.17:3306).
Fri Apr 29 04:57:40 2022 - [info]  read_only=1 is not set on slave kafka-3(10.4.7.18:3306).
Fri Apr 29 04:57:40 2022 - [info] Checking replication filtering settings..
Fri Apr 29 04:57:40 2022 - [info]  binlog_do_db= , binlog_ignore_db= 
Fri Apr 29 04:57:40 2022 - [info]  Replication filtering check ok.
Fri Apr 29 04:57:40 2022 - [info] GTID (with auto-pos) is supported. Skipping all SSH and Node package checking.
Fri Apr 29 04:57:40 2022 - [info] Checking SSH publickey authentication settings on the current master..
Fri Apr 29 04:57:40 2022 - [info] HealthCheck: SSH to kafka-2 is reachable.
Fri Apr 29 04:57:40 2022 - [info] 
kafka-2(10.4.7.19:3306) (current master)
 +--kafka-1(10.4.7.17:3306)
 +--kafka-3(10.4.7.18:3306)

Fri Apr 29 04:57:40 2022 - [info] Checking replication health on kafka-1..
Fri Apr 29 04:57:40 2022 - [info]  ok.
Fri Apr 29 04:57:40 2022 - [info] Checking replication health on kafka-3..
Fri Apr 29 04:57:40 2022 - [info]  ok.
Fri Apr 29 04:57:40 2022 - [info] Checking master_ip_failover_script status:
Fri Apr 29 04:57:40 2022 - [info]   /etc/mha/scripts/master_ip_failover --command=status --ssh_user=root --orig_master_host=kafka-2 --orig_master_ip=10.4.7.19 --orig_master_port=3306 


IN SCRIPT TEST====/sbin/ifconfig eth0:1 down==/sbin/ifconfig eth0:1 10.4.7.100/24;/sbin/arping -I eth0 -c 3 -s 10.4.7.100/24 >/dev/null 2>&1===

Checking the Status of the script.. OK 
Fri Apr 29 04:57:41 2022 - [info]  OK.
Fri Apr 29 04:57:41 2022 - [warning] shutdown_script is not defined.
Fri Apr 29 04:57:41 2022 - [info] Got exit code 0 (Not master dead).

MySQL Replication Health is OK.
```



