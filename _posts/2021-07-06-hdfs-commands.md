---
title: Commands for manipulating files and directories on HDFS
author: trannguyenhan
date: 2021-07-06 16:00:00 +0700
categories: [Hadoop & Spark, Hadoop]
tags: [Hadoop, Apache Hadoop, Bigdata, HDFS]
math: true
mermaid: true
image:
  src: https://i.pinimg.com/originals/de/61/7d/de617d1ce71f621bbeba8b293996e9fc.jpg
---
The commands on HDFS are generally quite similar to the commands on Linux, both in terms of their functions and names, if you are familiar with Linux/Ubuntu then you probably don't need to learn much, so apply. just use it.

### help

Ask about common line in HDFS :

```bash
hdfs dfs -help
hdfs dfs -usege <utility_name>
```

Ask about a specific command:

```bash
hdfs dfs -help <statement>
VD: hdfs dfs -help ls
```

### mkdir

Make folder in HDFS:

```bash
hdfs dfs -mkdir /newfolder
```	

Make folder if this already exists, add parameter:

```bash
hdfs dfs -mkdir -p /newfolder
```

### ls

Display folder and file in directory:

```bash
hdfs dfs -ls /
```

Display folder and file in directory and all subfolder in this:

```bash
hdfs dfs -ls -R /
```

### put

Copy file from local folder to HDFS folder:

```bash
hdfs dfs -put ~/test.txt /newfolder/
hdfs dfs -copyFromLocal ~/test.txt /newfolder/
```

### get

Copy file from HDFS folder to local folder:

```bash
hdfs dfs -get /newfolder/test.txt /copyfromhdfs/
hdfs dfs -copyToLocal /newfolder/test.txt /copyfromhdfs/
```

### cat

Check content in file:

```bash
hdfs dfs -cat /newfolder/test.txt
```

### mv

Move file or folder from folder to folder:

```bash
hdfs dfs -mv /newfolder /DR
```

### cp

Copy file or folder from folder to folder:

```bash
hdfs dfs -cp /DR/newfolder/test.txt /DR
```

### rm

Delete file in HDFS:

```bash
hdfs dfs -rm /DR/test.txt
```

### getmerge

Merge file in HDFS and download to local:

```bash
hdfs dfs -getmerge /usr/trannguyenhan /home/trannguyenhan01092000/output.dat
```