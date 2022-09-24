---
title: Project Socket Stream with Spark Streaming
author: trannguyenhan 
date: 2021-09-23 20:52:00 +0700
categories: [Hadoop & Spark, Spark]
tags: [Spark Streaming, Bigdata, Spark]
math: true
mermaid: true
image:
  src: https://raw.githubusercontent.com/demanejar/image-collection/main/SparkStreaming/streaming-arch.png
---

*In this post, we consider a small example with Spark Streaming. My work is creating a project with Spark Streaming listen in port 7777 and filter line contain "error" word and print it to console.*

## Project preparation

This is a simple project write by Scala. You can see all project in [https://github.com/demanejar/socket-stream](https://github.com/demanejar/socket-stream), please clone this project to your local before run it.

There are 2 main file in this project, first is `SocketStream.scala` file:

```scala
import org.apache.spark._
import org.apache.spark.SparkContext._
import org.apache.spark.streaming.StreamingContext
import org.apache.spark.streaming.StreamingContext._
import org.apache.spark.streaming.dstream.DStream
import org.apache.spark.streaming.Duration
import org.apache.spark.streaming.Seconds

object SocketStream {
	def main(args : Array[String]){
		val conf = new SparkConf().setAppName("Socket-Stream")
		val ssc = new StreamingContext(conf, Seconds(1))
		val lines = ssc.socketTextStream("localhost", 7777)
		val errorLines = lines.filter(_.contains("error"))
		errorLines.print()
		ssc.start()
		ssc.awaitTermination()
	}
}
```

- If you run project in cluster with multi node, change hostname from `localhost` to master node IP.
- The task of this file is creating `StreamingContext` listening in port 7777, filter line contain `error` word and print it to console.

The remaining file is `build.sbt`:

```sbt
name := "socket-stream"
version := "0.0.1"
scalaVersion := "2.11.12"
libraryDependencies ++= Seq(
"org.apache.spark" %% "spark-core" % "2.4.1" % "provided",
"org.apache.spark" %% "spark-streaming" % "2.4.1"
)
```

- We must build project to file `.jar` before submit it to Spark cluster with spark-submit.
- If your computer no install `sbt`, you can refer how to install `sbt` in [https://www.scala-sbt.org/download.html](https://www.scala-sbt.org/download.html).

## Run project and see result

Start Spark and check status via UI address of master node in port 8080 (`spark://PC0628:7077`):

![](https://raw.githubusercontent.com/demanejar/image-collection/main/SocketStream/socket_stream.png)

Build project to `.jar` file with `sbt` command:

```bash
sbt clean package
```

![](https://raw.githubusercontent.com/demanejar/image-collection/main/SocketStream/sbt_clean_package.png)

Run spark-submit with `.jar` file just create:

```bash
spark-submit --master spark://PC0628:7077 --class SocketStream target/scala-2.11/socket-stream_2.11-0.0.1.jar
```

- `master` is address of master node
- `class` is path to main function of project

Open other terminal and run command below to start sending text through port 7777:

```bash
nc -l 7777
```

Result in console is very fast:

![](https://raw.githubusercontent.com/demanejar/image-collection/main/SocketStream/result_1.png)

![](https://raw.githubusercontent.com/demanejar/image-collection/main/SocketStream/result_2.png)

Open port 4040 to see again detail job just done (`localhost:4040`): 

![](https://raw.githubusercontent.com/demanejar/image-collection/main/SocketStream/4040.png)

Refer: Learning Spark - Zaharia M., et al. (trang 184)
