---
title: Hadoop Install
date: 2018-04-20 16:13:59
tags: [Hadoop]
category: [CS]
---

# Hadoop Install

我采用的是 linux 下的 hadoop 安装，本安装文档说明前提是已经全局安装了`open-jdk1.8`(也可以使用 hotspot jdk)。本文档主要参考官方文档。部分英文由自己补充和改写，主要内容翻译为中文。

Required software for Linux include:

Java™ must be installed. Recommended Java versions are described at HadoopJavaVersions.

ssh must be installed and sshd must be running to use the Hadoop scripts that manage remote Hadoop daemons.

<!-- more -->

## Installing Software

以下是必需的软件。

```
$ sudo apt-get install ssh
$ sudo apt-get install rsync
```

## Download

To get a Hadoop distribution, download a recent stable release from one of the Apache Download [Mirrors](http://www.apache.org/dyn/closer.cgi/hadoop/common/hadoop-2.7.6/hadoop-2.7.6.tar.gz).

For global using, move the hadoop software to `~/software`.
Before start hadoop, we should check the `etc/profile` and `~/.bashrc` coded with the following code.
These are my personal config about Java and basic hadoop config.
For hadoop, `HADOOP_HOME` must to be set firstly.

我使用的是 hadoop2.7.5 版本。到官网下载后，解压，并把主目录移动到了`~/software`下，并且改名为了hadoop。
然后需要配置一些最基本的环境变量，`etc/profile`，`~/.bashrc`配置一些全局变量，我这里包括java的基本配置。如果与读者不一致，后续操作需要做相应的修改
(官方后续许多跟hadoop相关的操作是在 HADOOP_HOME 下的，我把命令全部放入到PATH中，后续操作更方便)。

etc/profile:

```
export JAVA_HOME=/usr/lib/jdk1.8
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib  
export PATH=$PATH:${JAVA_HOME}/bin
```

~/.bashrc:

```
export HADOOP_HOME=~/software/hadoop
export PATH=$PATH:$HADOOP_HOME/bin
export PATH=$PATH:$HADOOP_HOME/sbin
```


想让环境配置立即刷新需要使用下面的命令。

```
source /etc/profile
source ~/.bashrc
```

## Standalone Operation

By default, Hadoop is configured to run in a non-distributed mode, as a single Java process. This is useful for debugging.

The following example copies the unpacked conf directory to use as input and then finds and displays every match of the given regular expression. Output is written to the given output directory.

hadoop 默认为单击模式，这对 debugging 比较友好。这里复制一些文件到input，然后跑一个Wordcount程序读取input，输出到output。

```
$ mkdir input
$ cp $HADOOP_HOME/etc/hadoop/*.xml input
$ hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.5.jar grep input output 'dfs[a-z.]+'
$ cat output/*
```

## Pseudo-Distributed Operation

Hadoop can also be run on a single-node in a pseudo-distributed mode where each Hadoop daemon runs in a separate Java process.

通过利用多个java进程，hadoop 也可以在单个节点上运行伪分布式模式。

Use the following:

伪分布式需要先配置。

$HADOOP_HOME/etc/hadoop/core-site.xml:

```xml
<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://localhost:9000</value>
    </property>
</configuration>
```

$HADOOP_HOME/etc/hadoop/hdfs-site.xml:

```xml
<configuration>
    <property>
        <name>dfs.replication</name>
        <value>1</value>
    </property>
</configuration>
```

## Setup passphraseless ssh

Now check that you can ssh to the localhost without a passphrase:

先检测下是否能用密钥访问 localhost。

```
$ ssh localhost
```

If you cannot ssh to localhost without a passphrase, execute the following commands:

如果你无法通过密钥来访问 localhost，执行下面的命令。

```
$ ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
$ cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
$ chmod 0600 ~/.ssh/authorized_keys
```

## Execution

The following instructions are to run a MapReduce job locally.

以下的说明针对的是本地任务。

Format the filesystem:

第一次使用该文件系统需要初始化。

![format](format.png)

```
$ hdfs namenode -format
```

Start NameNode daemon and DataNode daemon:

用该命令启动节点。

![start-dfs](start-dfs.png)

```
$ start-dfs.sh
```

The hadoop daemon log output is written to the $HADOOP_LOG_DIR directory (defaults to $HADOOP_HOME/logs).

我们这里没有设置`$HADOOP_LOG_DIR`，使用默认的就好。

Browse the web interface for the NameNode; by default it is available at:

成功启动后，可以访问 Web 界面 http://localhost:50070 查看 NameNode 和 Datanode 信息，还可以在线查看 HDFS 中的文件。
通过 50070 这个默认端口用浏览器浏览名字节点。

![overview](overview.png)

```
NameNode - http://localhost:50070/
```

Make the HDFS directories required to execute MapReduce jobs:

创建 DFS 文件来提供执行 maoreduce 任务。

![mkdir](dfs-mkdir.png)

```
$ hdfs dfs -mkdir /user
$ hdfs dfs -mkdir /user/<username>
```

Copy the input files into the distributed filesystem:

把本地需要测试的文件复制到 dfs 里的文件系统中。

![dfs-put](dfs-put.png)

```
$ hdfs dfs -put $HADOOP_HOME/etc/hadoop input
```

Run some of the examples provided:

运行一个官方实例。可以看到已经运行了WordCount。

![example](example.png)

```
$ hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.5.jar grep input output 'dfs[a-z.]+'
```

Examine the output files: Copy the output files from the distributed filesystem to the local filesystem and examine them:

从 hadoop 中的 dfs 的复制 output 文件到本地一份。

```
$ hdfs dfs -get output output
$ cat output/*
```

When you’re done, stop the daemons with:

当你工作结束后，使用下面命令关闭。

![stop-dfs](stop-dfs.png)

```
$ stop-dfs.sh
```

## Reference

- [Hadoop: Setting up a Single Node Cluster.](http://hadoop.apache.org/docs/r2.7.6/hadoop-project-dist/hadoop-common/SingleCluster.html#Standalone_Operation)
- [ssh：connect to host localhost port 22: Connection refused](https://blog.csdn.net/u013963380/article/details/72599793)
- [Hadoop安装教程_单机/伪分布式配置](http://www.powerxing.com/install-hadoop/)