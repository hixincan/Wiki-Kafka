



# 认识Kafka

kafka.apache.org



官方定位：A distributed streaming platform （分布式地流处理的平台）

Kafka是基于zookeeper的分布式消息系统

Kafka具有高吞吐率、高性能、实时及高可靠等特点（这些特点是基于流平台的特点，流又是与大数据相关的应用）



## 安装

手动安装，建议统一在 /opt （解压目录）

环境变量 /etc/profile

jdk（略）

### zookeeper

Kafka是基于zookeeper的

**1、解压程序到 /opt**

`[root@centos7 tmp]# tar -zxvf apache-zookeeper-3.7.0-bin.tar.gz -C ./install/`

注意，是 bin 版本



**2、新建配置**

```shell
[root@centos7 opt]# cd apache-zookeeper-3.7.0/
[root@centos7 apache-zookeeper-3.7.0]# ls
bin                         conf                       Jenkinsfile-owasp      owaspSuppressions.xml  tools               zookeeper-compatibility-tests  zookeeper-jute
checkstyle-simple.xml       dev                        Jenkinsfile-PreCommit  pom.xml                zk-merge-pr.py      zookeeper-contrib              zookeeper-metrics-providers
checkstyle-strict.xml       excludeFindBugsFilter.xml  LICENSE.txt            README.md              zookeeper-assembly  zookeeper-docs                 zookeeper-recipes
checkstyleSuppressions.xml  Jenkinsfile                NOTICE.txt             README_packaging.md    zookeeper-client    zookeeper-it                   zookeeper-server
[root@centos7 apache-zookeeper-3.7.0]# cd conf
[root@centos7 conf]# ls
configuration.xsl  log4j.properties  zoo_sample.cfg
[root@centos7 conf]# cp zoo_sample.cfg  zoo.cfg
[root@centos7 conf]# ls
configuration.xsl  log4j.properties  zoo.cfg  zoo_sample.cfg
```

> zoo.cfg  固定命名，默认配置如下

dataDir=/tmp/zookeeper  #建议放在空间比较大的磁盘

clientPort=2181   #默认客户端端口



**3、启动 zookeeper**

```shell
[root@centos7 apache-zookeeper-3.7.0-bin]# cd bin/
[root@centos7 bin]# ls
README.txt    zkCli.cmd  zkEnv.cmd  zkServer.cmd            zkServer.sh             zkSnapshotComparer.sh  zkSnapShotToolkit.sh  zkTxnLogToolkit.sh
zkCleanup.sh  zkCli.sh   zkEnv.sh   zkServer-initialize.sh  zkSnapshotComparer.cmd  zkSnapShotToolkit.cmd  zkTxnLogToolkit.cmd
[root@centos7 bin]# ./zkServer.sh start
ZooKeeper JMX enabled by default
Using config: /opt/apache-zookeeper-3.7.0-bin/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED
[root@centos7 bin]# ./zkCli.sh
Connecting to localhost:2181
...
```



4、检查启动状态

`ps -ef|grep zookeeper`





### kafka

**1、解压程序到 /opt**

`root@centos7 tmp]# tar -zxvf kafka_2.13-2.8.0.tgz -C ./install/`



**2、修改配置**

```shell
[root@centos7 kafka_2.13-2.8.0]# cd config/
[root@centos7 config]# ls
connect-console-sink.properties    connect-file-sink.properties    connect-mirror-maker.properties  kraft                server.properties       zookeeper.properties
connect-console-source.properties  connect-file-source.properties  connect-standalone.properties    log4j.properties     tools-log4j.properties
connect-distributed.properties     connect-log4j.properties        consumer.properties              producer.properties  trogdor.conf
[root@centos7 config]# vi server.properties
```

> server.properties

```shell
listeners=PLAINTEXT://1.117.139.73:9092 # 没有域名，先用ip
advertised.listeners=PLAINTEXT://1.117.139.73:9092 # 没有域名，先用ip
zookeeper.connect=1.117.139.73:2181	# 没有域名，先用ip
log.dirs=/tmp/kafka-logs # 暂时放在 /tmp 下
```



**3、常用命令**

==注意==：& 符号，表示后台启动！

```
1、启动Kafka
bin/kafka-server-start.sh config/server.properties &
2、停止Kafka
bin/kafka-server-stop.sh
```

> 启动

```shell
[root@centos7 kafka_2.13-2.8.0]# ls
bin  config  libs  LICENSE  licenses  NOTICE  site-docs
[root@centos7 kafka_2.13-2.8.0]# ./bin/kafka-server-start.sh config/server.properties &
[1] 14792
[root@centos7 kafka_2.13-2.8.0]# [2021-07-15 23:44:06,699] INFO Registered kafka:type=kafka.Log4jController MBean (kafka.utils.Log4jControllerRegistration$)
[2021-07-15 23:44:07,345] INFO Setting -D jdk.tls.rejectClientInitiatedRenegotiation=true to disable client-initiated TLS renegotiation (org.apache.zookeeper.common.X509Util)
[2021-07-15 23:44:07,526] INFO Registered signal handlers for TERM, INT, HUP (org.apache.kafka.common.utils.LoggingSignalHandler)
[2021-07-15 23:44:07,561] INFO starting (kafka.server.KafkaServer)
[2021-07-15 23:44:07,562] INFO Connecting to zookeeper on 1.117.139.73:2181 (kafka.server.KafkaServer)
[2021-07-15 23:44:07,597] INFO [ZooKeeperClient Kafka server] Initializing a new session to 1.117.139.73:2181. (kafka.zookeeper.ZooKeeperClient)
[2021-07-15 23:44:07,607] INFO Client environment:zookeeper.version=3.5.9-83df9301aa5c2a5d284a9940177808c01bc35cef, built on 01/06/2021 20:03 GMT (org.apache.zookeeper.ZooKeeper)
[2021-07-15 23:44:07,607] INFO Client environment:host.name=centos7 (org.apache.zookeeper.ZooKeeper)
[2021-07-15 23:44:07,607] INFO Client environment:java.version=1.8.0_291 (org.apache.zookeeper.ZooKeeper)
[2021-07-15 23:44:07,607] INFO Client environment:java.vendor=Oracle Corporation (org.apache.zookeeper.ZooKeeper)
[2021-07-15 23:44:07,607] INFO Client environment:java.home=/usr/java/jdk1.8.0_291-amd64/jre (org.apache.zookeeper.ZooKeeper)
...
```

==报错==：`org.apache.kafka.common.KafkaException: Socket server failed to bind to 1.117.139.73:9092: Cannot assign requested address.`

解决方法（基于OpenStack虚拟机部署时的坑），`listeners=PLAINTEXT://1.117.139.73:9092`中的ip改为真实ip[ifconfig中显示的ip，eth网卡 - 172.17.0.8]即可，其他使用时正常使用对外ip即可，跟真实ip就没有关系了。





**4、检查启动状态**

`ps -ef | grep kafka`

```shell
[root@centos7 ~]# ps -ef | grep kafka
root     18332 17192  6 23:58 pts/0    00:00:04 /usr/java/jdk1.8.0_291-amd64/bin/java -Xmx1G -Xms1G -server -XX:+UseG1GC -XX:MaxGCPauseMillis=20 -XX:InitiatingHeapOccupancyPercent=35 -XX:+ExplicitGCInvokesConcurrent -XX:MaxInlineLevel=15 -Djava.awt.headless=true -Xloggc:/opt/kafka_2.13-2.8.0/bin/../logs/kafkaServer-gc.log -verbose:gc -XX:+PrintGCDetails -XX:+PrintGCDateStamps -XX:+PrintGCTimeStamps -XX:+UseGCLogFileRotation -XX:NumberOfGCLogFiles=10 -XX:GCLogFileSize=100M -Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.authenticate=false -Dcom.sun.management.jmxremote.ssl=false -Dkafka.logs.dir=/optkafka_2.13-2.8.0/bin/../logs -Dlog4j.configuration=file:bin/../config/log4j.properties -cp .:/usr/java/jdk1.8.0_291-amd64/lib/dt.jar:/usr/java/jdk1.8.0_291-amd64/lib/tools.jar:/opt/kafka_2.13-2.8.0/bin/../libs/activation-1.1.1.jar:/opt/kafka_2.13-2.8.0/bin/../libs/aopalliance-repackaged-2.6.1.jar:/opt/kafka_2.13-2.8.0/bin/../libs/argparse4j-0.7.0.jar:/opt/kafka_2.13-2.8.0/bin/../libs/audience-annotations-0.5.0.jar:/opt/kafka_2.13-2.8.0/bin/../libs/commons-cli-1.4.jar:/opt/kafka_2.13-2.8.0/bin/../libs/commons-lang3-3.8.1.jar:/opt/kafka_2.13-2.8.0/bin/../libs/connect-api-2.8.0.jar:/opt/kafka_2.13-2.8.0/bin/../libs/connect-basic-auth-extension-2.8.0.jar:/opt/kafka_2.13-2.8.0/bin/../libs/connect-file-2.8.0.jar:/opt/kafka_2.13-2.8.0/bin/../libs/connect-json-2.8.0.jar:/opt/kafka_2.13-2.8.0/bin/../libs/connect-mirror-2.8.0.
.....
```



> 查看启动日志

```shell
[root@centos7 kafka_2.13-2.8.0]# ls
bin  config  libs  LICENSE  licenses  logs  NOTICE  site-docs
[root@centos7 kafka_2.13-2.8.0]# tail -f logs/server.log
[2021-07-15 23:58:16,225] INFO [ExpirationReaper-0-AlterAcls]: Starting (kafka.server.DelayedOperationPurgatory$ExpiredOperationReaper)
[2021-07-15 23:58:16,373] INFO [/config/changes-event-process-thread]: Starting (kafka.common.ZkNodeChangeNotificationListener$ChangeEventProcessThread)
[2021-07-15 23:58:16,387] INFO [SocketServer listenerType=ZK_BROKER, nodeId=0] Starting socket server acceptors and processors (kafka.network.SocketServer)
[2021-07-15 23:58:16,440] INFO [SocketServer listenerType=ZK_BROKER, nodeId=0] Started data-plane acceptor and processor(s) for endpoint : ListenerName(PLAINTEXT) (kafka.network.SocketServer)
[2021-07-15 23:58:16,440] INFO [SocketServer listenerType=ZK_BROKER, nodeId=0] Started socket server acceptors and processors (kafka.network.SocketServer)
[2021-07-15 23:58:16,459] INFO Kafka version: 2.8.0 (org.apache.kafka.common.utils.AppInfoParser)
[2021-07-15 23:58:16,459] INFO Kafka commitId: ebb1d6e21cc92130 (org.apache.kafka.common.utils.AppInfoParser)
[2021-07-15 23:58:16,459] INFO Kafka startTimeMs: 1626364696440 (org.apache.kafka.common.utils.AppInfoParser)
[2021-07-15 23:58:16,460] INFO [KafkaServer id=0] started (kafka.server.KafkaServer)
...
```





## Kafka基本概念

Topic：

* 在消息队列的场景中，是用来存放两个系统之间通信消息的第三方



Producer：

* 消息生产者





Consumer：

* 消息消费者





## 基本操作

topic：独立于生产者和消费者，用于存放消息的地方



**创建topic**

`bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test-topic`

报错：Error while executing topic command : Replication factor: 1 larger than available brokers: 0.

解决：Kafka未正常启动



**查看已经创建的Topic信息**
`bin/kafka-topics.sh --list --zookeeper localhost:2181`



```shell
[root@centos7 kafka_2.13-2.8.0]# bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test-topic

Created topic test-topic.
[2021-07-16 08:16:59,975] INFO [ReplicaFetcherManager on broker 0] Removed fetcher for partitions Set(test-topic-0) (kafka.server.ReplicaFetcherManager)
[2021-07-16 08:17:00,137] INFO [Log partition=test-topic-0, dir=/tmp/kafka-logs] Loading producer state till offset 0 with message format version 2 (kafka.log.Log)
[2021-07-16 08:17:00,146] INFO Created log for partition test-topic-0 in /tmp/kafka-logs/test-topic-0 with properties {compression.type -> producer, message.downconversion.enable -> true, min.insync.replicas -> 1, segment.jitter.ms -> 0, cleanup.policy -> [delete], flush.ms -> 9223372036854775807, segment.bytes -> 1073741824, retention.ms -> 604800000, flush.messages -> 9223372036854775807, message.format.version -> 2.8-IV1, file.delete.delay.ms -> 60000, max.compaction.lag.ms -> 9223372036854775807, max.message.bytes -> 1048588, min.compaction.lag.ms -> 0, message.timestamp.type -> CreateTime, preallocate -> false, min.cleanable.dirty.ratio -> 0.5, index.interval.bytes -> 4096, unclean.leader.election.enable -> false, retention.bytes -> -1, delete.retention.ms -> 86400000, segment.ms -> 604800000, message.timestamp.difference.max.ms -> 9223372036854775807, segment.index.bytes -> 10485760}. (kafka.log.LogManager)
[2021-07-16 08:17:00,147] INFO [Partition test-topic-0 broker=0] No checkpointed highwatermark is found for partition test-topic-0 (kafka.cluster.Partition)
[2021-07-16 08:17:00,148] INFO [Partition test-topic-0 broker=0] Log loaded for partition test-topic-0 with initial high watermark 0 (kafka.cluster.Partition)
[root@centos7 kafka_2.13-2.8.0]#
[root@centos7 kafka_2.13-2.8.0]# bin/kafka-topics.sh --list --zookeeper localhost:2181

test-topic
```





**发送消息（生产者）**
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test-topic

localhost 需要换成真实ip  （ifconfig获取）

```
[root@centos7 kafka_2.13-2.8.0]# bin/kafka-console-producer.sh --broker-list 172.17.0.8:9092 --topic test-topic
>
```

看到 `>` 表示建立成功，可以输入消息；这是长连接，可以持续输入消息

*注：ctrl + delete 能删除特殊符号*





**接收消息（消费者）**

bin/kafka-console-consumer.sh --bootstrap-server 172.17.0.8:9092 --topic test-topic --from-beginning

```shell
[root@centos7 kafka_2.13-2.8.0]# bin/kafka-console-consumer.sh --bootstrap-server 172.17.0.8:9092 --topic test-topic --from-beginning
  dddd
kafka son{}d


asdf
dddd
asdf
hello world
```

























# 底层实现

































