



# 认识Kafka

kafka.apache.org



官方定位：A distributed streaming platform （分布式地流处理的平台）

Kafka是基于zookeeper的分布式消息系统

Kafka具有高吞吐率、高性能、实时及高可靠等特点（这些特点是基于流平台的特点，流又是与大数据相关的应用）



# 安装

手动安装，建议统一在 /opt （解压目录）

环境变量 /etc/profile

jdk（略）

## zookeeper

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





## kafka

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
zookeeper.connect=1.117.139.73:2181	# 没有域名，先用ip
log.dirs=/tmp/kafka-logs # 暂时放在 /tmp 下
```



**3、常用命令**

```
1、启动Kafka
bin/kafka-server-start.sh config/server.properties&
2、停止Kafka
bin/kafka-server-stop.sh
3、创建Topic
bin/kafka-topics.sh--create--zookeeper localhost:2181--replication-factor 1--partitions 1--topic jiangzh-topic
4、查看已经创建的Topic信息
bin/kafka-topics.sh--list--zookeeper localhost:2181
5、发送消息
bin/kafka-console-producer.sh--broker-list localhost:9092--topic jiangzh-topic
6、接收消息
bin/kafka-console-consumer.sh--zookeeper localhost:2181--topic jiangzh-topic--from-beginning
```











流处理



高吞吐量的消息队列



高性能、高可用



5大类 API









优化配置经验





# 底层实现

































