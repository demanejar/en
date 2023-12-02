---
title: An overview of Hadoop 
author: trannguyenhan
date: 2021-06-29 20:52:00 +0700
categories: [Hadoop & Spark, Hadoop]
tags: [Hadoop, Apache Hadoop, Bigdata, HDFS, Hadoop Yarn]
math: true
mermaid: true
image:
  src: https://i.pinimg.com/originals/4a/3c/14/4a3c144fa89a85fd6dbccc07bdb8509a.jpg
---

*Hadoop is a framework based on a solution from Google to store and process large data. Hadoop uses the MapReduce algorithm to process input data in parallel. In short, Hadoop is used to develop applications that can perform complete statistical analysis on bulk data.*

***See more***: [***Mapreduce programming model for Bigdata***](https://demanejar.github.io/posts/mapreduce-programming-model/)

## Hadoop architecture 

Hadoop consists of 2 main layers:

- Processing and computing layer (MapReduce): MapReduce is a parallel programming model for processing large data on a cluster of commercial computers (commodity hardware).

- Storage Layer (HDFS): HDFS provides a distributed storage solution that is also designed to run on commercial computers.

In addition to the two components mentioned above, the Hadoop framework also includes the following two modules:

- Hadoop Common: libraries and utilities written in Java language

- Hadoop Yarn: scheduling and managing resources

![](https://i.pinimg.com/originals/4a/3c/14/4a3c144fa89a85fd6dbccc07bdb8509a.jpg)

## How does Hadoop work?

Hadoop solves the problem that if you or your company cannot afford to build super powerful servers, we can combine many commercial computers to create a cluster of machines capable of processing. a large amount of data. Machines in the cluster can read and process data in parallel to bring high efficiency. You can run code on a cluster of computers, this process goes through the following flow:

- Data is divided into folders and files. Each file is contained in a block with a predetermined fixed size (default is 128MB).

- These files are distributed across different nodes and clusters

- HDFS sits on top of the local file system, monitoring the process

- Blocks are saved in copies to prevent errors occurring on the hardware

- Check if the code is executed successfully

- Performing the _Sort_ step takes place between _Map_ and _Reduce_

- Send data to certain machines to perform the next steps

- Write a log for each completed job

## Advantages of Hadoop

- Hadoop allows users to write and test quickly on distributed systems. Hadoop efficiently and automatically distributes data and work across multiple machines in the same cluster.

- Hadoop does not require the hardware of the machines in the cluster, any computer can be part of the Hadoop cluster. Hadoop will assign reasonable work to each machine in accordance with the capabilities of each machine.

- Hadoop provides a system with high fault tolerance and availability. Instead, the Hadoop library has been designed to handle errors from the application layer.

- Clusters can be added or removed within the cluster without affecting running processes

- Another big advantage of Hadoop is that in addition to being open source, it is compatible across all platforms as it is based on Java.
