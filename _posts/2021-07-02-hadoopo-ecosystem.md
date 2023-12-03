---
title: Hadoop Ecosystem
author: trannguyenhan
date: 2121-07-02 08:00:00 +0700
categories: [Hadoop & Spark, Hadoop]
tags: [Ubuntu, Hadoop Ecosystem, Bigdata, Java, HDFS, Pig, Kafka, Zookeeper, Hive, HBase, Sqoop]
math: true
mermaid: true
image:
  src: https://i.pinimg.com/originals/83/7b/af/837bafd1df1b18b6cbe3404b697c2559.jpg
---

*The Apache Hadoop ecosystem refers to the various components of the Apache Hadoop software library; it includes open source projects as well as a complete range of additional tools. Some of the most famous tools of the Hadoop ecosystem include HDFS, Hive, Pig, YARN, MapReduce, Spark, HBase, Oozie, Sqoop, Zookeeper, etc.* 

## HDFS 

Hadoop Distributed File System (HDFS) is one of the largest systems in the Hadoop ecosystem and is Hadoop's primary storage system.

HDFS provides reliable and affordable storage for large data blocks, optimized for large file sizes (from several hundred MB to several TB). HDFS has a hierarchical directory tree space like Unix and Linux operating systems.

Due to the nature of large data and distributed file systems, editing is very difficult, so HDFS only supports writing extra data at the end of the file ( `append`), if you want to edit anywhere. Otherwise, the only way is to rewrite the entire file with the modifications and replace the old file. HDFS follows the “ write once, read many ” criteria.

![](https://i.pinimg.com/originals/c6/c3/1a/c6c31aa5f418ab6ac2f4122ba3f4db3b.jpg)

The architecture of HDFS is a Master/Slave architecture, the HDFS master (namenode) manages the namespace and metadata, and monitors the datanodes. HDFS slave (datanode) directly performs I/O operations with chunks.

The design principles of HDFS are:

- Append only => reduce concurrency control overhead
- Data dispersion
- Duplicate data
- Fault tolerance mechaism

## Hive

![](https://i.pinimg.com/originals/ee/83/49/ee8349d0a2166192988ecc3854924f18.jpg)

Apache Hive is a data warehouse infrastructure tool for processing structured data in Hadoop. Hive facilitates reading, writing, and managing large data sets residing in distributed storage using SQL (however remember Hive is not a relational database).

Hive provides a SQL-style language for querying called HiveQL or HQL.

To learn more about Hive, you can see more at Hive's homepage: [https://hive.apache.org/](https://hive.apache.org/)

## HBase

![](https://i.pinimg.com/originals/7f/c8/e1/7fc8e11bdfe3534fa26303a44dcf2d5f.jpg)

HBase is a column-family database, storing data on HDFS, considered the database management system of Hadoop.

To better understand Column-Family you can read more articles about Bigtable: [Bigtable: A Distributed Storage System for Structured Data](https://github.com/demanejar/download-folder/blob/main/chang.pdf)

See more about Apache HBase at: [https://hbase.apache.org/](https://hbase.apache.org/)

## Hadoop MapReduce

![](https://i.pinimg.com/originals/f3/b8/a4/f3b8a4c9127a93eb012a84709efc18ac.jpg)

This is another data processing layer of Hadoop. It is capable of processing large structured and unstructured data as well as managing very large data files in parallel by dividing the job into a set of independent tasks (sub-jobs).

To see more about the idea of ​​MapReduce, you can review the article [MapReduce programming model for Bigdata](https://demanejar.github.io/posts/en/mapreduce-programming-model/)

## Apache Zookeeper

![](https://i.pinimg.com/originals/5a/bd/12/5abd12b8e7b13606c6fd13266c52a008.jpg)

Zookeeper is a service that provides highly reliable distributed coordination functions:

- Manage server group members
- Election of leaders
- Dynamic configuration information management
- Monitor system status

This is a core service, crucial in distributed systems.

See more about Zookeeper at: [https://zookeeper.apache.org/](https://zookeeper.apache.org/)

## YARN

Apache Hadoop YARN (Yet Another Resource Negotiator) introduced from Hadoop 2.0 is a technology that supports resource management and job scheduling in Hadoop.

We can see the presence of YARN as two daemons:

- Node Managers
- Resource Manager

## Apache Sqoop

Sqoop is a tool that allows batch data transfer from Apache Hadoop and structured databases such as relational databases.

Currently Apache Sqoop has been discontinued, you can see more about Sqoop at: [https://sqoop.apache.org/](https://sqoop.apache.org/)

## Apache Kafka

![](https://i.pinimg.com/originals/a9/00/50/a90050c575892a8f6cdc59180cfa5f1c.jpg)

Apache Kafka is a system created by linkedin to serve stream processing of data and then open-sourced. It was initially seen as a message queue but was later developed into a distributed processing platform.

Reference: [https://databricks.com/](https://databricks.com/glossary/hadoop-ecosystem), [https://www.apache.org/](https://www.apache.org/)
