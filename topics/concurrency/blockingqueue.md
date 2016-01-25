---
layout: post
title: Blocking Queues in Java
description: "Deep dive into Thread fundamentals"
excerpt: "Deep dive into Java's Garbage Collection."
published: true
name : "Part5"
category : "Thread"
---

Blocking Queues are an important part of Executor framework. A Blocking queue can be considered as a queue for holding tasks submitted by the producer. The queue delivers this task to the consumer as soon as any thread becomes available for consuming it. Associating a queue with an executor has many advantages. The queue can declare the maximum number of tasks it can hold upto thus preventing OutOfMemoryException. There are various types of blocking queues in Java which provides different functionalites. We will see each of them in detail.

1.  ArrayBlockingQueue
2.  LinkedBlockingQueue
3.  PrioirtyBlockingQueue
4.  DelayQueue
5.  LinkedBlockingDequeue
6.  SynchronousBlockingQueue
7.  ScheduledQueue


**ArrayBlockingQueue** : As the name suggests, ArrayBlockingQueue internally uses an array for holding the tasks. Since it uses an array implementation, the size of the queue should be known at the time of instantiation. It means that the queue will not entertain more elements than its size. Any more tasks coming to the queue will simply be rejected. `ArrayBlockingQueue` uses FIFO approach. The task which comes in first will be delievered first. 


**LinkedBlockingQueue** : `LinkedBlockingQueue` internally uses a list for storing the tasks and hence the queue is dynamic in size. If not specified, the number of tasks the queue can hold is Integer.MAX_VALUE. If a upper bound is mentioned, then the queue will store not more than that number of tasks. `LinkedBlockingQueue` also works on FIFO approach. The advantage of using `LinkedBlockingQueue` over `ArrayBlockingQueue` is that it gives us the flexiblity of increasing and decreasing the size of this queue. 

**PriorityBlockingQueue** : `PriorityBlockingQueue` is a queue where tasks are picked based on the priority assigned to them. One thing to note here is that `PriorityBlockingQueue` only accepts an object of FutureTask otherwise it will throw a ClassCastException. So all the tasks passed to this queue must be wrapped inside FutureTask object and that object must have implemented Comparable interface. 

**SynchronousBlockingQueue** : `SynchronousBlockingQueue` is a queue which will accept tasks only when there is some thread out there to consume the task otherwise it will not accept the task.
