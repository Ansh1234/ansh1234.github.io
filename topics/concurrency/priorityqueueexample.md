---
layout: post
title: Blocking Queues in Java
description: "Deep dive into Thread fundamentals"
excerpt: "Deep dive into Java's Garbage Collection."
published: true
name : "Part5"
category : "Thread"
---

I will try to explain this problem with a fully functional code. But before diving into the code I would like to explain about PriorityBlockingQueue

**PriorityBlockingQueue** : PriorityBlockingQueue is an implementation of BlockingQueue. It accepts the tasks along with their priority and submits the task with the highest priority for execution first. If any two tasks have same priority, then we need to provide some custom logic to decide which task goes first.

Now lets get into the code straightaway.

**Driver class** : This class creates an executor which accepts tasks and later submits them for execution. Here we create two tasks one with LOW priority and the other with HIGH priority. Here we tell the executor to run a MAX  of 1 threads and use the PriorityBlockingQueue. 

{% highlight java %}
public static void main(String[] args) {

/*
Minimum number of threads that must be running : 0
Maximium number of threads that can be created : 1
If a thread is idle, then the minimum time to keep it alive : 1000
Which queue to use : PriorityBlockingQueue
*/
PriorityBlockingQueue queue = new PriorityBlockingQueue();
ThreadPoolExecutor executor = new ThreadPoolExecutor(0,1,
1000, TimeUnit.MILLISECONDS,queue);

MyTask task = new MyTask(Priority.LOW,"Low");
executor.execute(new MyFutureTask(task));
task = new MyTask(Priority.HIGH,"High");
executor.execute(new MyFutureTask(task));
}
{% endhighlight %}

**MyTask class** : MyTask implements Runnable and accepts priority as an argument in the constructor. When this task runs, it prints a message and then puts the thread to sleep for 1 second.

 
{% highlight java %}
public class MyTask implements Runnable {

public int getPriority() {
return priority.getValue();
}

private Priority priority;

public String getName() {
return name;
}

private String name;

public MyTask(Priority priority,String name){
this.priority = priority;
this.name = name;
}

@Override
public void run() {
System.out.println("The following Runnable is getting executed "+getName());
try {
Thread.sleep(1000);
} catch (InterruptedException e) {
e.printStackTrace();
}
}

}
{% endhighlight %}

**MyFutureTask class** : Since we are using PriorityBlocingQueue for holding our tasks, our tasks must be wrapped inside FutureTask and our implementation of FutureTask must implement Comparable interface. The Comparable interface compares the priority of 2 different tasks and submits the task with the highest priority for execution.

{% highlight java %}
public class MyFutureTask extends FutureTask<MyFutureTask>
implements Comparable<MyFutureTask> {

private  MyTask task = null;

public  MyFutureTask(MyTask task){
super(task,null);
this.task = task;
}

@Override
public int compareTo(MyFutureTask another) {
return task.getPriority() - another.task.getPriority();
}
}
{% endhighlight%}
