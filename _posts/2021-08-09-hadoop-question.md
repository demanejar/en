---
title: Summary of questions about Apache Hadoop
author: trannguyenhan
date: 2021-08-09 20:52:00 +0700
categories: [Hadoop & Spark, Hadoop]
tags: [Hadoop, Apache Hadoop, Bigdata, HDFS, Hadoop Yarn]
math: true
mermaid: true
image:
  src: https://i.pinimg.com/originals/4a/3c/14/4a3c144fa89a85fd6dbccc07bdb8509a.jpg
---

#### The main goal of Apache Hadoop

Open data storage and powerful data processing. Save costs when storing and processing large amounts of data.

You can see more details about Hadoop's goals [HERE](https://demanejar.github.io/en/posts/hdfs-introduction/#objective-of-hdfs)

#### Hadoop solves the problem of fault tolerance through what technique?

- Hadoop is fault tolerant through redundancy engineering

- Files are fragmented, fragments are replicated to other nodes on the cluster

- The jobs that need to be calculated are segmented into independent tasks 

#### Describe how a client gets data on HDFS

The client queries the namenode to know the location of the chunks. Namenode returns the location of the chunks. Client connects to datanodes in parallel to read chunks.

You can see details of this data reading process [HERE](https://demanejar.github.io/en/posts/hdfs-introduction/#read-data)

#### Describe how a client writes data on HDFS

The client connects to the namenode to specify the amount of data to write. Namnode specifies the location of chunks for the client. The client when the chunk reaches the first datanode, then the datanodes automatically perform replication. The process ends when all chunks and clones have been executed successfully.

You can see details of this data recording process [HERE](https://demanejar.github.io/posts/hdfs-introduction/#write-data)

#### Main components in Hadoop Ecosystem

Hadoop Ecosytem is a platform that provides solutions for storing and processing large amounts of data. The main components in Hadoop Ecosytem are:

- HDFS 
- Mapreduce framework 
- YARN
- Zookeeper

#### Fault tolerance mechanism of datanode in HDFS

*Using the heartbeat mechanism*, the datanode will periodically send status notifications to the namenode. The default time period for datanode to send heartbeat to namenode is 3s. After 3s, if datanode does not send information to namenode, by default namenode will consider that node dead and the unfinished task of that node will be given back. for new node.

To reconfigure the heartbeat time, you can add the following XML to the `file hdfs-site.xml` as follows:

```xml
<property>
<name>dfs.heartbeat.interval</name>
<value>3</value>
</property>

<property>
<name>dfs.namenode.heartbeat.recheck-interval</name>
<value>300000</value>
</property>
```

You can see the default configurations `hdfs-site.xml` [HERE](https://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-hdfs/hdfs-default.xml)

#### Data organization mechanism of datanode in HDFS

Files in HDFS are divided into block-sized chunks called data blocks. Blocks are stored as independent units (default block size is 128MB).
 
Chunks are system files in the datanode server's local file.
 
*See more:* [*What happens if you store small files on HDFS?*](https://demanejar.github.io/en/posts/hdfs-introduction/#blocks)
 
#### Data replication mechanism in HDFS

File blocks are duplicated to increase fault tolerance. The Namenode is the node that makes all the decisions regarding the replication of blocks.

#### How does HDFS solve the single-point-of-failure problem for namenode

Use Secondary Namenode according to active-passive mechanism. Secondary Namenode only works when there is a problem with the Namenode

You can see details about Secondary Namenode [HERE](https://demanejar.github.io/en/posts/hdfs-introduction/#secondary-namenode)

#### What are the 3 modes in which Hadoop can run?

- *Standalone mode*: this is the default mode, Hadoop uses local FileSystem and a single Java process to run Hadoop services.

- *Pseudo-distributed mode*: deploy Hadoop on 1 node to execute all services.

- *Fully-distributed mode*: deploy Hadoop on a cluster of machines with namenode and datanode.

#### Explain Bigdata and Bigdata's 5V criteria

Bigdata is a term for large and complex data sets that are difficult to process with relational data tools and traditional data processing applications.

5V in Bigdata is:

- *Volume*: Volume represents the amount of data that is growing at an exponential rate, i.e. in Petabytes and Exabytes.
- *Velocity*: Velocity refers to the rate at which data is growing, very fast. Today, yesterday's data is considered old data. Today, social networks are a major contributing factor to the growth of data.
- *Variety*: Variety refers to the heterogeneity of data types. In other words, the data collected comes in many formats like video, audio, csv, etc. So, these different formats represent many types of data.
- *Veracity*: Veracity refers to doubtful or uncertain data of available data due to data inconsistency and incompleteness. The available data can sometimes be messy and difficult to trust. With many forms of big data, quality and accuracy are difficult to control. Volume is often the reason behind the lack of data quality and accuracy.
- *Value*: It's all well and good to have access to big data but unless we can turn it into a value.
