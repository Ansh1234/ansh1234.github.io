---
layout: post
title : Garbage Collection 
description : Deep Dive into Java's Garbage Collection
published: true
text-to-display : [Introduction, Part1, Part2, Part3,Part4]
url-to-go : [/topics/garbagecollection/introduction.html,/topics/garbagecollection/gc1.html,/topics/garbagecollection/gc2.html,/topics/gc/gc3.html,/topics/garbagecollection/gc4.html]
---

## Garbage Collection in Java

Garbage Collectors are is one of the few areas where even the most experienced Java Developers fumble. Although Garbage Collection in Java is fairly straightforward, the reason why developers don’t have a hold on the topic is because Java itself takes care of this phenomenon and leaves nearly nothing in the hands of the developers. But in order to do better programming one must understand the underlying machinery. This post explains the garbage collection in the most widely used JVM implementation Hotspot which is  provided by Oracle. 

**Mark and Sweep Algorithm** : This algorithm is used by various Garbage collectors for garbage collection. The algorithm works in the following steps.
1. Identify all the root objects in the application and mark them ( Local variables and parameters of a function are root objects. Static variables are also root objects).
2. Starting from any root object, recursively traverse all the referenced objects and mark them.
3. Remove ( read sweep ) all the unmarked objects from the heap.

Let us understand this algorithm with a simple Java program which uses two classes Address and Street.


{{ page.title }}

{{ page.id }}
{% highlight java %}
public class Street {

  public String streetName = "Street";
  
  public Street(String streetName){
    this.streetName =streetName;
  }
  public Street(){ 
  }
}
{% endhighlight %}

{% highlight java %}
public class Address {

  public static void main(String[] args) {
    fun1();
    fun2();
  }

  public static void fun1(){
    Address addr1 = new Address();
    //Some code
  }

  public static void fun2(){
  	Address addr2 = new Address();
    //Some code
  }
}
{% endhighlight %}

While we are in fun2(), two objects i.e addr1 and addr2 from Address class are allocated memory. Both the Address objects internally create one Street object. So at this point of time, memory is allocated for 4 objects. Now lets assume that the garbage collector runs at this time. Out of 4 allocated objects, we have only 1 root objects which is addr2. addr1 is not a root object because the call to the function fun1 is already over and therefore addr1 is out of scope. Thus the GC marks all the root objects. 

Starting from the root objects, GC determines if they have a reference to any other object in the heap. addr1 references an object of Street. So the object of Street also becomes a live object. Thus the GC also marks the street object related to addr1.

After this phase is over, GC scans through all the available objects in the heap and checks whether the object is marked or not. The memory from all the objects which were not marked is being reclaimed. Therefore after the Garbage collection gets over, addr1 and the street object which it created internally doesn't exist in the memory. 

Lets say at the time of Garbage collection, we are inside fun2() method. At this point of time, we already have objects created for defaultAddress, addr1 and addr2. 

The objective of this algorithm is pretty straightforward as its name suggests. Scan through the entire Heap and mark all the live objects ( which are referenced from the application) . After completing the scan, sweep ( remove the objects from memory ) which are not marked.

Hotspot JVM uses generational data structure for the heap i.e the heap is divided into  generations. The generations are Young Generation and Old Generation. The generations in the heap are based on the analysis that most of the objects in the application are short lived and hence it makes more sense to initially put these objects into one place(Young Generation) and if the objects survive subsequent garbage collections move it to a tenured place (Old Generation).

We’ll discuss about the two in detail, but before that let’s take a look at one of the other non-heap generations provided by the JVM. 
 
**Permanent Generation**:  This generation contains all the metadata of the classes including the code for methods and variables related to class like static variables. ( Note : This generation has been removed in Java 8 ).

**Young Generation**: All the newly created objects are allocated in the young generation.
The young generation is further divided into three spaces.
- Eden Space.
- Survivor Space 1 (S1)
- Survivor Space 2 (S2)

**Old Generation** : After objects in the Young Generation survive a number of garbage collections, they are moved to Old Generation. 

 The ratio between the size of  Young Generation and Old Generation is 1:3 by default. This means that the Old Generation is thrice as large as the Young Generation. However this can be changed using the flag -XX:NewRatio. It is usually preferable that the Young Generation be bigger than the Old Generation but the JVM enforces no hard and fast rules for it. Reference.

**Garbage Collectors** : JVM provides a number of garbage collectors each having  distinct characteristics. Both the Young Generation and the Old Generation can run different garbage collectors.

**Garbage Collectors in Young Generation** 

_Copy ( use -XX:+UseSerialGC flag)_
It uses one thread to copy surviving objects from Eden to Survivor spaces and between Survivor spaces until it decides they've been there long enough, at which point it copies them into the old generation.

_PS Scavenge (use -XX:+UseParallelGC flag)_ 
It uses multiple threads in parallel and has some knowledge of how the old generation is collected (essentially written to work with the serial and PS old gen collectors).

_ParNew (use -XX:+UseParNewGC flag)_ 
It uses multiple threads in parallel and has an internal 'callback' that allows an old generation collector to operate on the objects it collects (really written to work with the concurrent collector).

_G1 Young Generation (enabled with -XX:+UseG1GC)_ 
the garbage first collector, uses the 'Garbage First' algorithm which splits up the heap into lots of smaller spaces, but these are still separated into Eden and Survivor spaces in the young generation for G1

**Garbage Collectors in Old Generation** 

_MarkSweepCompact (use -XX:+UseSerialGC flag)_ 
It uses a serial (one thread) full mark-sweep garbage collection algorithm, with optional compaction.

_PS MarkSweep (use -XX:+UseParallelOldGC flag)_ 
It uses a  parallelised version (i.e. uses multiple threads) of the MarkSweepCompact.

_ConcurrentMarkSweep (use -XX:+UseConcMarkSweepGC flag)_ 
It attempts to do most of the garbage collection work in the background without stopping application threads while it works (there are still phases where it has to stop application threads, but these phases are attempted to be kept to a minimum). Note if the concurrent collector fails to keep up with the garbage, it fails over to the serial MarkSweepCompact collector for (just) the next GC.

_G1 Mixed Generation (use -XX:+UseG1GC)_ 
This uses the 'Garbage First' algorithm which splits up the heap into lots of smaller spaces.

**Example :** 

Now, let us consider the following illustrations for the purpose of understanding. At the start of the application, both Young Generation and Old Generation will be empty.

| Day     | Meal    | Price |
| --------|---------|-------|
| Monday  | pasta   | $6    |
| Tuesday | chicken | $8    |


<table>
<tr>
<td>1</td>
<td>2</td>
</tr>
</table>
