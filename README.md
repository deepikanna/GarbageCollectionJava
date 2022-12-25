# GarbageCollectionJava

## Introduction

Following points gives a brief description of what garbage collection is and why is it required :

- Automatic garbage collection is the process of looking at heap memory, identifying which objects are in use and which are not, and deleting the unused objects
- An unused object in memory is identified when an object is not referenced by any part of our program and is considered to be dead
- It removes these dead objects hence improving performance and controlling memory leaks

## Phases of Garbage Collection

Following points describes how a basic garbage collector works:

- Marking - Here the objects which are dead are identified by garbage collector and marked
- Normal Deletion - Here the unreferenced objects are removed leaving the live objects along with the reference to all free space available
- Deletion with compaction - For improving performance, its better to compact all the referenced object together and get a bigger free space of memory which is unallocated.

## Generational Garbage Collector

Following point discusses the need of generational garbage collector :

- As memory is allocated for many objects, traditional garbage collector spends a lot of time to clear all the unused references.
- Empirical analysis shows us that most objects in Java are short lived. Hence we introduce a generational garbage collector
- For this collector we divide heap into 3 different generations : Young, Old, and Permanent
- Young generation is where minor garbage collection takes place after all new objects allocated fills up this heap memory.
- We will now discuss in detail the process which happens in young generation:
  - Object Allocation - We fill up the Eden space first with new objects
  - Eden Space Filled - Once Eden space is filled minor garbage collection is invoked
  - Copying Referenced Objects - Live objects are copied into S0 survivor space (from space) and Eden space is cleared off the dead objects.
  - Object Aging - In next minor garbage collection when eden space is again filled, all the live objects in both Eden space and S0 survivor space (from space) is moved into S1 survivor space (to space) and all dead objects in Eden and S0 are cleared off.
  - Additional Aging - In next minor garbage collection when eden space is again filled, garbage collector again moves all live objects from Eden and S1 to S0 and dead unreferenced objects in Eden and S1 are cleared off.
- When the object ages unto a certain threshold period it is promoted from young generation into old generation. 
- Old generation garbage collection is a major garbage collection
- Major Garbage collection takes more time as most of objects are alive in this part of heap.
- Both minor garbage collection and major garbage collection experience Stop the World Event where all application threads are stopped to perform garbage collection.
- This hits the performance of system and reduces responsiveness
- Permanent generation consist of metadata required by JVM to describe classes and methods used in the application
- Classes maybe collected if found that the class is no longer required. Hence this collection happens in the full garbage collection.

## Types of Garbage Collectors used by JVM

- Serial GC
  - This is the simplest garbage collector where all garbage collection activities are performed in a single thread serially.
  - After every garbage collection, a compaction is performed
  - It leads to Stop the World Event during every garbage collection.
  - JVM argument to execute Serial GC is XX:+UseSerialGC

- Parallel GC
  - This GC is used for multi processor or multi threaded application
  - Multiple threads are used for Young generation and 1 thread is used for older generation
  - This also causes Stop the World event but it is used when lot of work needs to be done and application freeze is accepted
  - JVM argument to execute Parallel GC is XX:+UseParallelGC

- Parallel Old GC 
  - Same as Parallel GC but uses multiple threads for even the Older Generation
  - JVM argument to execute Parallel Old GC is XX:+UseParallelOldGC

- Concurrent Mark Sweep (CMS) GC
  - CMS GC is similar to parallel GC but it runs alongside the application thread
  - Hence it leads to low Stop the World event
  - But it also increases the CPU utilization.
  - JVM argument to execute CMS GC is XX:+UseConcMarkSweepGC

- Garbage First G1GC
  - It is parallel and concurrent GC but works in a totally different way
  - We also need large heap size for this GC
  - In this GC, heap is equally divided into large regions where objects are allocated
  - Multiple threads are used to run GC and any region can be young or old
  - It performs garbage collection by identifying the region with most amount of garbage i.e. unused references, hence it is called garbage first
  - JVM argument to execute G1GC is XX:+UseG1GC

- Epsilon GC
  - Its a do nothing GC. We can keep allocating memory but unreferenced objects are never removed. Hence once memory is exhausted JVM shuts down
  - It can be used only for applications where no memory leaks can happen or garbage free
  - JVM argument to execute Epsilon GC is XX:+UseEpsilonGC

- Shenandoah 
  - It does more work concurrently with the application thread
  - G1GC can evacuate its heap regions only when application is paused while Shenandoah can relocate objects concurrently with the application.
  - It compacts live objects and cleans memory and releases the RAM back to OS 
  - Since it is concurrent, it is also CPU intensive
  - JVM argument to execute Epsilon GC is XX:+UseShenandoahGC

- ZGC
  - It is for application with large heap size and low latency (low pause time)
  - It allows application to run while performing garbage collection
  - It uncommits unused memory and returns it to the operating system.
  - JVM argument to execute Epsilon GC is XX:+UseZGC
