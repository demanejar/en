---
title: MapReduce programming model for Bigdata
author: trannguyenhan
date: 2021-06-24 08:00:00 +0700
categories: [Hadoop & Spark, Hadoop]
tags: [Ubuntu, mapreduce, Bigdata, Java]
math: true
mermaid: true
image:
  src: https://i.pinimg.com/originals/24/b3/8d/24b38d9f8ea2a4bddb32c1c95d290378.jpg
---
*MapReduce is a processing technique and a programming model for distributed computing to deploy and process big data. MapReduce contains two important tasks: map and reduce. WordCount is a typical example of MapReduce that I will illustrate in this article*

## Why was Mapreduce born?

As the introduction, everyone knows that MapReduce is a combination of map and reduce, which are the two main functions in this programming method. This programming model originates from Google, or more clearly, from a Google article. The problem is the need to parallelize calculations, distribute data and have high fault tolerance.

Simple and powerful MapReduce allows automatic parallelization and distribution of large-scale computations, combined with implementations of this programming model to achieve high performance on large computer clusters of hundreds, thousands of rows. thousands of computers.

## Programming model

The mapreduce model must be said to be extremely simple and easy to visualize. Programmers will only have to rewrite two functions in the MapReduce programming model: the map function and the reduce function.

### WordCount example

WordCount is a classic problem to illustrate MapReduce, the idea of ​​MapReduce is written similar to the following pseudo code:

```cpp 
map(string key, string value){
	// key: document name
	// value: document value
	for each word w in value{
		ComputeIntermediate(w,"1"); // tính toán các giá trị key/value trung gian
	} 
}

reduce(string key, List values){
	int result = 0;
	for each v in values {
		result += ParseInt(v); // tổng hợp giá trị 
	}
	
	Write(key, result); // ghi kết quả
}
```

- For example, now we need to perform a task of counting the number of occurrences of each word in a text. First we will split the text into lines, each line will be numbered. The input to the Map function will be key/value pairs that are the line/text number on that line.

- In _Map_, we will separate each word in a line and assign them an initial frequency value of 1.

- There will be a process in the middle that helps combine _Map_ outputs with the same key together into an array of values.

- _Reduce_ 's input will be a key/value pair of the word / and an array of the frequency of that word. In _Reduce_, we just need to add the values ​​in the array and get the result as the number of times each word appears in the input text.

To visualize better, you can see the following image:

![](https://i.pinimg.com/originals/01/f5/ab/01f5ab14f961c104919010b2faaf1d7b.jpg)

When programming _MapReduce_, the inputs and outputs for the _Map_ and _Reduce_ functions are something the programmer must determine before doing. For the WordCount problem, it seems simple, but in more complex problems, bringing it to the MapReduce model is not necessarily easy.

### MapReduce Model 

To summarize, the two functions Map and Reduce will have input, the output will be collapsed like the pseudo code below:

```cpp  
map()	    : (k1, v1)       => list(k2, v2)
reduce()	: (k2, list(v2)) => list(v2)
```

## Conclude

MapReduce is not the only model that helps parallelize well, but there are many other programming models that help process large data by parallelization. However, MapReduce is simple and accessible.

To understand more about MapReduce, you can learn and read Google's article [Mapreduce : Simplified Data Processing on Large Clusters](https://github.com/demanejar/download-folder/blob/main/mapreduce-osdi04.pdf). Of course, Google's article not only mentions this simple model but also Deeper issues such as the requirements when building a distributed system based on the MapReduce model, improved functions, performance and experiences. In this article, I mainly talk about the idea of ​​MapReduce, the rest I will write in the article about a specific framework, Hadoop MapReduce.
