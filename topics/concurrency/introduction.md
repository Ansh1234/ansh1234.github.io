---
layout: post
title: Executors in Java
description: "Deep dive into Thread fundamentals"
excerpt: "Deep dive into Java's Garbage Collection."
published: true
name : "Part5"
category : "Thread"
text-to-display : [Introduction, Part1, Part2, Part3,Part4]
url-to-go : [/topics/concurrency/introduction.html,/topics/concurrency/blockingqueue.html,/topics/concurrency/priorityqueueexample.html,/topics/garbagecollection/gc3.html,/topics/garbagecollection/gc4.html]
---


Prior to Java 5, achieving concurrency was a difficult task for a developer. But thanks to the introduction of the very powerful Executor framework, developers can achieve this feat with relative ease. So what exactly does this framework try to achieve ?

1. The most important feature of this framework is seperation of concerns. It lets the developer to create tasks, and let the framework decide when, how and where to execute that task which is totally configurable. 
2. It relieves the developer with the creation of Threads.
3. It provides the developers various types of queues for storing the tasks. 

![_config.yml]({{ site.baseurl }}/images/division.png)

Before we deep dive, lets take a look at some of the terminologies which we will be using a lot in the post

 **Executor** : Executor is an interface which has only one method execute() which accepts any Runnable object.

 **ExecutorService** : An ExecutorService can be thought of as an interface to the threadpool which contains various functions like shutdown() etc which helps to know the current state of threads.

**ThreadPool** : ThreadPool is a term used for a pool of threads. Depending on the configuration, the number of threads in a pool will change.

**BlockingQueue** : Executors uses a blocking queue internally which a queue for holding tasks submitted to the executor. 

**Tasks** : Tasks are runnable objects meant to be executed in some thread. Like downloading an image from the internet can be task, 


The lifecycle of a task can be summarized by this diagram. 

![_config.yml]({{ site.baseurl }}/images/concurrency.png)

Every executor has an internal queue maintained by it for storing the tasks submitted to it. The queue checks in the thread pool whether any thread is available to pick up the task. If any thread is ready to pick up the task, then the queue will pass the task to the thread. If the queue is no longer able to accept tasks, then it simply rejects a tasks which throws RejectExecutionException. 
