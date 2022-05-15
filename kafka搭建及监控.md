# 1、安装jdk rpm包或tar包

[jdk下载地址](https://www.oracle.com/java/technologies/downloads/#java8)

```shell
[root@kafka-1 ~]# mkdir /usr/local/java

[root@kafka-1 ~]# tar -xzvf jdk-8u321-linux-x64.tar.gz -C /usr/local/java/

[root@kafka-1 ~]# cat /etc/profile
.....

export JAVA_HOME=/usr/local/java/jdk1.8.0_321/
export JRE_HOME=/usr/local/java/jdk1.8.0_321/jre/
export CLASS_PATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib
export PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin

[root@kafka-1 ~]# source /etc/profile
[root@kafka-1 ~]# java -version
java version "1.8.0_321"
Java(TM) SE Runtime Environment (build 1.8.0_321-b07)
Java HotSpot(TM) 64-Bit Server VM (build 25.321-b07, mixed mode)
[root@kafka-1 ~]# jps
5223 Jps
```



# 2、配置免密（非必要）

```shell
ssh-keygen -t rsa -b 2048 -N ‘’ -f /root/.ssh/id_rsa

ssh-copy-id root@centos-2

ssh-copy-id root@centos-3
```



# 3、zk安装

## 3.1、下载zookeeper：

 [zk下载地址](https://zookeeper.apache.org/releases.html)

## 3.2、配置过程

```shell
[root@kafka-1 ~]# mkdir /usr/local/zookeeper
[root@kafka-1 ~]# tar -xzf apache-zookeeper-3.7.0-bin.tar.gz --strip-components 1 -C /usr/local/zookeeper/
[root@kafka-1 ~]# cd /usr/local/zookeeper/conf/
[root@kafka-1 conf]# cp zoo_sample.cfg zoo.cfg

[root@kafka-1 conf]# cat zoo.cfg | grep -v "^#"
tickTime=2000
initLimit=10
syncLimit=5
dataDir=/usr/local/zookeeper/data/
dataLogDir=/use/local/zookeeper/logs
server.1=kafka-1:2888:3888
server.2=kafka-2:2888:3888
server.3=kafka-3:2888:3888
clientPort=2181

[root@kafka-1 conf]# mkdir -p /usr/local/zookeeper/data/
[root@kafka-1 conf]# mkdir -p /usr/local/zookeeper/logs

[root@kafka-1 conf]# echo 1 > /usr/local/zookeeper/data/myid ---->另外两台主机分别为2和3

[root@kafka-2 ~]# echo 2 > /usr/local/zookeeper/data/myid
[root@kafka-3 ~]# echo 3 > /usr/local/zookeeper/data/myid 

  
[root@kafka-1 conf]# cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
10.4.7.17 kafka-1
10.4.7.19 kafka-2
10.4.7.18 kafka-3

[root@kafka-1 ~]# scp /etc/hosts kafka-2:/etc/hosts
[root@kafka-1 ~]# scp /etc/hosts kafka-3:/etc/hosts
```

端口号2888表示该服务器与集群中leader交换信息的端口，默认为2888， 3888表示选举时服务器相互通信的端口。

```shell
[root@kafka-1 conf]# scp -r /usr/local/zookeeper  root@kafka-2:/usr/local/
[root@kafka-1 conf]# scp -r /usr/local/zookeeper  root@kafka-3:/usr/local/

[root@kafka-1 ~]# cat /etc/profile

....
export  PATH=/usr/local/zookeeper/zookeeper-release-3.7.0/bin/:$PATH


[root@kafka-1 conf]# scp /etc/profile root@kafka-2:/etc
[root@kafka-1 conf]# scp /etc/profile root@kafka-3:/etc


[root@kafka-1 ~]# source /etc/profile (每台主机均需执行)
```

## 3.3、验证 

```shell
[root@kafka-1 bin]# zkServer.sh start-foreground     # 以debug模式启动

[root@kafka-1 ~]# setenforce 0
[root@kafka-1 ~]# getenforce
Permissive

[root@kafka-1 ~]# zkServer.sh start
ZooKeeper JMX enabled by default
Using config: /usr/local/zookeeper/bin/../conf/zoo.cfg
Starting zookeeper ... already running as process 5341.
[root@kafka-1 ~]# zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /usr/local/zookeeper/bin/../conf/zoo.cfg
Client port found: 2181. Client address: localhost. Client SSL: false.
Mode: follower


```



# 4、kafka安装

[kafka下载地址](https://kafka.apache.org/downloads) 

[kafka官方文档](https://kafka.apache.org/30/documentation.html#quickstart)

当前版本为kafka_2.12-3.1.0.tgz，2.12代表 Scala 版本，3.1.0表示Kafka的版本。

## 4.1、配置

```shell
[root@kafka-1 ~]# mkdir /usr/local/kafka
[root@kafka-1 ~]# tar -xzf kafka_2.12-3.1.0.tgz --strip-components 1 -C /usr/local/kafka

[root@kafka-1 ~]# cd /usr/local/kafka/config/
[root@kafka-1 config]# cat /usr/local/kafka/config/server.properties | grep -Pv "^#|^$"
...
broker.id=1
log.dirs=/usr/local/kafka/logs
zookeeper.connect=kafka-1:2181,kafka-2:2181,kafka-3:2181
...

# 开启JMX端口（配置kafka-manager时需要）
# 修改bin/kafka-server-start.sh，添加JMX_PORT参数，添加后样子如下

[root@kafka-1 bin]# cat kafka-server-start.sh
if [ "x$KAFKA_HEAP_OPTS" = "x" ]; then
                    export KAFKA_HEAP_OPTS="-Xmx1G -Xms1G"
                    export JMX_PORT="9999"
fi

scp  /usr/local/kafka root@centos-2:/usr/local/   修改broker.id=2
scp  /usr/local/kafka root@centos-3:/usr/local/   修改broker.id=3
```

## 4.2、配置环境变量

```shell
[root@kafka-1 bin]# cat /etc/profile
export PATH=/usr/local/kafka/bin:$PATH


scp /etc/profile root@centos-2:/etc
scp /etc/profile root@centos-3:/etc
source /etc/profile  #每台主机均需执行
```

## 4.3、启动

```shell
# 三个节点都需要执行

[root@kafka-1 ~]# cd /usr/local/kafka/bin/
[root@kafka-1 bin]# kafka-server-start.sh -daemon ../config/server.properties

# 执行 jps命令查看 Java进程，此时进程信息至少包括以下几项:
[root@kafka-1 bin]# jps
5341 QuorumPeerMain
6365 Kafka
6413 Jps
```



## 4.4、kafka实战

创建一个拥有3个副本的topic:

```shell
# kafka 2.2版本之后不支持  --zookeeper  此选项。改为--bootstrap-server
bin/kafka-topics.sh --create --zookeeper kafka-1:2181 --replication-factor 3 --partitions 1 --topic my-replicated-topic

kafka-topics.sh --list --zookeeper centos-1:2181
 my-replicated-topic
 
[root@kafka-1 kafka]# bin/kafka-topics.sh --create --bootstrap-server kafka-1:9092 --replication-factor 3 --partitions 1 --topic my-replicated-topic                      
Created topic my-replicated-topic.

[root@kafka-1 kafka]# kafka-topics.sh --list --bootstrap-server kafka-1:9092
my-replicated-topic 
 
```

现在我们搭建了一个集群，怎么知道每个节点的信息呢？运行“"describe topics”命令就可以了：

```shell
kafka-topics.sh --describe ---bootstrap-server kafka-1:9092 --topic my-replicated-topic

[root@kafka-1 kafka]# kafka-topics.sh --describe --topic my-replicated-topic --bootstrap-server kafka-1:9092
Topic: my-replicated-topic      TopicId: oh-Y3sFxRiask6wDVWDNNg PartitionCount: 1       ReplicationFactor: 3     Configs: segment.bytes=1073741824
        Topic: my-replicated-topic      Partition: 0    Leader: 2       Replicas: 2,3,1 Isr: 2,3,1
```

第一行是对所有分区的一个描述，然后每个分区都会对应一行，因为我们只有一个分区所以下面就只加了一行。
 leader：负责处理消息的读和写，leader是从所有节点中随机选择的.
 replicas：列出了所有的副本节点，不管节点是否在服务中.
 isr：是正在服务中的节点.

在我们的例子中，节点2是作为leader运行。

往topic发送消息：

```shell
[root@kafka-1 kafka]# kafka-console-producer.sh --broker-list kafka-1:9092 --topic my-replicated-topic
>This is my first event
>This is my second event
```

消费这些消息：

```shell
[root@kafka-2 ~]# kafka-console-consumer.sh --bootstrap-server kafka-1:9092 --from-beginning  --topic my-replicated-topic
This is my first event
This is my second event
```

测试一下容错能力：

```shell
# broker2作为leader运行，现在kill掉：

[root@kafka-2 bin]# jps
6553 Jps
6314 Kafka
5311 QuorumPeerMain
[root@kafka-2 bin]# kill -9 6314

[root@kafka-1 kafka]# kafka-topics.sh --describe --topic my-replicated-topic --bootstrap-server kafka-1:9092
Topic: my-replicated-topic      TopicId: oh-Y3sFxRiask6wDVWDNNg PartitionCount: 1       ReplicationFactor: 3     Configs: segment.bytes=1073741824
        Topic: my-replicated-topic      Partition: 0    Leader: 3       Replicas: 2,3,1 Isr: 3,1
```

现在leader为3，

虽然最初负责续写消息的leader down掉了，但之前的消息还是可以消费的：

```shell
[root@kafka-1 kafka]# kafka-console-consumer.sh --bootstrap-server kafka-1:9092 --from-beginning --topic my-replicated-topic
This is my first event
This is my second event
```



# 5、kafka-manage配置

现在改名为cmak

[下载地址](https://github.com/yahoo/CMAK/releases/)

[参考文档](https://blog.csdn.net/weixin_40612128/article/details/123455813?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1.pc_relevant_antiscanv2&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1.pc_relevant_antiscanv2&utm_relevant_index=2)

```shell
# 需要使用jdk11+   ，暂时不支持jdk18
[root@kafka-1 cmak-3.0.0.5]# ls /usr/local/jdk-11.0.15/
bin  conf  include  jmods  legal  lib  man  README.html  release

[root@kafka-1 ~]# mkdir /usr/local/cmak
[root@kafka-1 ~]# unzip cmak-3.0.0.5.zip -d /usr/local/cmak/
[root@kafka-1 cmak-3.0.0.5]# cd /usr/local/cmak/cmak-3.0.0.5/conf/
[root@kafka-1 cmak-3.0.0.5]# cat conf/application.conf | grep -Pv "^#|^$"
...
kafka-manager.zkhosts="kafka-1:2181,kafka-2:2181,kafka-3:2181"
kafka-manager.zkhosts=${?ZK_HOSTS}
cmak.zkhosts="kafka-1:2181,kafka-2:2181,kafka-3:2181"
cmak.zkhosts=${?ZK_HOSTS}
...

# 指定jdk版本
[root@kafka-1 cmak-3.0.0.5]# head bin/cmak
#!/usr/bin/env bash

###  ------------------------------- ###
###  Helper methods for BASH scripts ###
###  ------------------------------- ###
JAVA_HOME=/usr/local/jdk-11.0.15

[root@kafka-1 cmak-3.0.0.5]# nohup bin/cmak -Dconfig.file=conf/application.conf -Dhttp.port=9001 &

# 进程停止后 需要删除RUNNING文件才可以重新启动
[root@kafka-1 cmak-3.0.0.5]# ls
application.home_IS_UNDEFINED  bin  conf  lib  logs  nohup.out  README.md  RUNNING_PID  share

[root@kafka-1 cmak-3.0.0.5]# rm -rf RUNNING_PID
```

监控界面参考

![CMAK-1](https://raw.githubusercontent.com/chengxinmu/Figure-bed/main/linux_base/CMAK-1.png)

![CMAK-2](https://raw.githubusercontent.com/chengxinmu/Figure-bed/main/linux_base/CMAK-2.png)

![CMAK-3](https://raw.githubusercontent.com/chengxinmu/Figure-bed/main/linux_base/CMAK-3.png)
