---
title: Install and deploy Hadoop single node 
author: trannguyenhan
date: 2021-07-01 16:00:00 +0700
categories: [Hadoop & Spark, Hadoop]
tags: [Hadoop, Apache Hadoop, Bigdata, HDFS, Hadoop Yarn]
math: true
mermaid: true
image:
  src: https://i.pinimg.com/originals/4a/3c/14/4a3c144fa89a85fd6dbccc07bdb8509a.jpg
---

*Every major industry is implementing Apache Hadoop as the standard framework for big data processing and storage. Hadoop is designed to be deployed across a network of hundreds or even thousands of dedicated servers. All these machines work together to deal with large volumes and huge data sets*

**_See more_** : [Overview of Hadoop](https://demanejar.github.io/posts/hadoop-introduction/)

![](https://1.bp.blogspot.com/-ptS_AgcV35I/YGGKkXVYm3I/AAAAAAAABTI/xWehG7DKqlYMgKDw9lxBpd9pJXFd8-kgACLcBGAsYHQ/s770/hadoop.logo_.tr_.jpg)

Hadoop is powerful and useful only when installed and exploited on multiple nodes, but for beginners, Hadoop Single node is a great start to get acquainted with hadoop. In this article, I will guide you to deploy Hadoop on 1 node (Hadoop Single node).

#### Conditions before installation

*   Your device must have jdk (version 8, 11 or 15 is fine, note that if you use hadoop 3.1.4, you can use jdk8, but if you use hadoop 3.2.2 or higher, use Java 11 or higher), if If not, you can install it with the following command:

```bash
sudo apt-get install openjdk-11-jdk -y
```

*   Your computer has SSH client and SSH server. If you don't have it, you can install it with the following command:

```bash
sudo apt-get install openssh-server openssh-client -y
```

## Set up User for Hadoop

Generate an SSH key pair and determine where it will be stored:

```bash
ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
```

The system will proceed to create and save the SSH key pair:

![](https://1.bp.blogspot.com/-SQkk3j6XsQk/YGGQGx4tzmI/AAAAAAAABTc/Im7azoBjBTcemvSNzqrPEQRf3SC2aSZJACLcBGAsYHQ/s736/Screenshot%2Bfrom%2B2021-03-29%2B15-29-40.png)

Using **_cat_** command to save **_public key_** to **_authorized\_keys_** in SSH directory:

```bash
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```

Delegate user permissions with the _**chmod**_ command :

```bash
chmod 0600 ~/.ssh/authorized_keys
```

Verify everything is set up correctly by ssh to localhost: 

```bash
ssh localhost
```

## Download and Install Hadoop on Ubuntu

Download a version of Hadoop from the official Hadoop distribution site at: [https://hadoop.apache.org/releases.html](https://hadoop.apache.org/releases.html)

![](https://1.bp.blogspot.com/-f6ANJNGMkUk/YGGMH9A3xfI/AAAAAAAABTQ/4r7GV3CZuQQNBvouMIew57h-JhPMDasZACLcBGAsYHQ/s1202/Screenshot%2Bfrom%2B2021-03-29%2B15-12-40.png)

Click on the **_binary_** in **_Binary download_**

Now put the compressed file you just downloaded anywhere and extract it with the command: 

```bash
tar xvzf hadoop-3.2.2.tar.gz
```

## Configuring and Deploying Hadoop Single Node (Pseudo-Distributed Mode)

To configure Hadoop for pseudo-distributed mode, we will edit the Hadoop configuration files in the `etc/hadoop` path and in the environment configuration file including the following files:

*   `.bashrc`
*   `hadoop-env.sh`
*   `core-site.xml`
*   `hdfs-site.xml`
*   `mapred-site.xml`
*   `yarn-site.xml`

**_Note_**: In the installation below, my Hadoop is placed in the folder `/opt/myapp`, you can put Hadoop anywhere, it doesn't have to be the same as me.

### Configure Hadoop environment variables (file .bashrc)

Open file `.bashrc` with `nano` : 

```bash
sudo nano ~/.bashrc
```

Define **_Hadoop_** enviroment by adding some variable below at end of the file (edit your hadoop home path match your hadoop path):

```
#Hadoop Related Options 
JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64 
export HADOOP_HOME=/opt/myapp/hadoop-3.2.2 
export HADOOP_INSTALL=$HADOOP_HOME
export HADOOP_MAPRED_HOME=$HADOOP_HOME 
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME 
export HADOOP_YARN_HOME=$HADOOP_HOME
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin
export HADOOP_OPTS="-Djava.library.path=$HADOOP_HOME/lib/native" 
```

Apply change with command:   `source ~/.bashrc`

### Edit file hadoop-env

open file `hadoop-env.sh` with `nano`:

```bash
sudo nano $HADOOP_HOME/etc/hadoop/hadoop-env.sh
```

Find the location as shown below, uncomment (remove sign #) the **JAVA\_HOME** and add your openjdk path: 

![](https://1.bp.blogspot.com/-bg35_Vkla3Y/YGH580alUaI/AAAAAAAABTs/hxsC-I7e6_ohlQHyiXVZyE0DktKa28UWgCPcBGAYYCw/s736/Screenshot%2Bfrom%2B2021-03-29%2B23-01-30.png)

### Edit file core-site.xml

Open file `core-site.xml` with `nano`:

```bash
sudo nano $HADOOP_HOME/etc/hadoop/core-site.xml
```

Add between 2 tags `configuration` to get the full content as follows:

```
<configuration>
<property>
	<name>hadoop.tmp.dir</name>
	<value>/opt/myapp/hadoop-3.2.2/tmpdata</value>
</property>
<property> 
	<name>fs.default.name</name> 
	<value>hdfs://localhost:9000</value>
</property>
</configuration>
```

`fs.default.name` Configure the address of HDFS, if not configured by default it will be placed at port 9000, if there is a duplicate port, change it to another port so Hadoop can operate normally.

### Edit file hdfs-site.xml

Open file `hdfs-site.xml` with `nano`:

```bash
sudo nano $HADOOP_HOME/etc/hadoop/hdfs-site.xml
```

Add between 2 tags `configuration` to get the full content as follows: 

```
<configuration>
<property>
	<name>dfs.data.dir</name> 
	<value>/opt/myapp/hadoop-3.2.2/dfsdata/namenode</value>
</property>
<property>
	<name>dfs.data.dir</name>
	<value>/opt/myapp/hadoop-3.2.2/dfsdata/datanode</value>
</property> 
<property>
	<name>dfs.replication</name> 
	<value>2</value>
</property>
</configuration>
```

`dfs.replication` configure the number of copies.

### Edit file mapred-site.xml

Open file `mapred-site.xml` with `nano`:
 
```bash
sudo nano $HADOOP_HOME/etc/hadoop/mapred-site.xml
```

Add between 2 tags `configuration` to get the full content as follows: 

```
<configuration>
<property>
	<name>mapreduce.framework.name</name>
	<value>yarn</value>
</property>
</configuration>
```

### Open file yarn-site.xml

Open file `yarn-site.xml` with `nano`:

```bash
sudo nano $HADOOP_HOME/etc/hadoop/yarn-site.xml
```

Add between 2 tags `configuration` to get the full content as follows: 

```
<configuration> 
<property>
	<name>yarn.nodemanager.aux-services</name>
	<value>mapreduce_shuffle</value>
</property>
<property> 
	<name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
	<value>org.apache.hadoop.mapred.ShuffleHandler</value>
</property>
<property>
	<name>yarn.resourcemanager.hostname</name>
	<value>127.0.0.1</value>
</property>
<property>
	<name>yarn.acl.enable</name>
	<value>0</value>
</property> 
<property>
	<name>yarn.nodemanager.env-whitelist</name>  
	<value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PERPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_MAPRED_HOME</value>
</property>
</configuration>
```

### Format HDFS namenode

Format namenode before start first service:

```bash
hdfs namenode -format
```

### Start Hadoop Cluster

In `sbin`, start hadoop with command: 

```bash
./start-all.sh
```

Check daemon running with command: 

```bash
jps
```

If the result is 6 daemons as follows, then you have configured correctly (you only pay attention to the 6 daemons above, you don't need to care about XMLServerLauncher): 

![](https://1.bp.blogspot.com/-XyWzBEAms_o/YGICM8Xp_4I/AAAAAAAABT0/_5oCJiK6cIYJX1WUUyVSfd8lvxvVsRlmACLcBGAsYHQ/s540/Screenshot%2Bfrom%2B2021-03-29%2B23-36-44.png)

### Access Hadoop UI from the browser

You can check whether Hadoop has been installed successfully or not at namenode's default port `9870`: 

```bash
localhost:9870
```

![](https://1.bp.blogspot.com/-FMSuLiP0Xqw/YGIC9MUQJkI/AAAAAAAABT8/c91MvynlbMAiLMr2J-k3G6NvHPF7tDPoACLcBGAsYHQ/s1920/Screenshot%2Bfrom%2B2021-03-29%2B23-39-14.png)

Check datanode at default port `9864`:

```bash
localhost:9864
```

![](https://1.bp.blogspot.com/-VCxFp52-k60/YGID8DuElgI/AAAAAAAABUY/JgschX9oirkyNPQuCLGI6nexjdCbO8BvwCLcBGAsYHQ/s1919/Screenshot%2Bfrom%2B2021-03-29%2B23-44-11.png)

Check out the Yarn resource manager at the portal `8088`:

```bash
locahost:8088
```

![](https://1.bp.blogspot.com/-s_b6FplcjHc/YGIDyR3m1tI/AAAAAAAABUU/vIs6SqtZc1kodlOpvF26b-U4PaXRQ5vuACLcBGAsYHQ/s1919/Screenshot%2Bfrom%2B2021-03-29%2B23-43-34.png)

Reference: [https://phoenixnap.com/](https://phoenixnap.com/kb/install-hadoop-ubuntu)
