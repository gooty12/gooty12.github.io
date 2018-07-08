---
title: Process and Threads
layout: post
---

Distributed systems can be viewed as a group of concurrent processes running on
different computers that share their work through messages over network. 
Understanding processes, threads, their differences and the different ways of
inter-process communication makes things easier for us as we start learning 
more involved concepts.

A process is a program that is in execution. Programs can be viewed as passive 
entities that contain a bunch of instructions. Process, on the other hand, is more
dynamic in nature containing not only just the instructions, but also its
execution state like memory (stack, heap allocated to it), registers that it uses,
resources like files, printers, etc that it has opened. It's a living entity!

Threads and processes are inter-related. Threads are where all the real action
takes place. A process is composed of several threads. If a process doesn't have
any threads, then there is no real difference between the both. In this case, we
can say that the process consists of only a single thread (so, process = thread).

However, most often, it is possible to construct a process as a set of independent
tasks that can be performed simultaneously or in parallel. For example, suppose
you want to sum all the elements in a huge array containing about a million
entries. This is a typical example where the process doesn't have to run 
sequentially adding each element one after the other starting from the beginning.
Instead, you can divide the array into smaller chunks and run the same logic on
each chunk in parallel. To do that, you create a thread for each chunk and let it
do its own computation independent of other threads. Finally, all these chunk sums
are added up to give the end result. This significantly reduces the execution 
time of a process.

Threads can be user-level or kernel-level. User-level threads are implemented
entirely in userspace, the operating system doesn't know about these threads.
All it knows is the process, the threads in the process are unknown. The main
advantage of this is that scheduling and managing the threads is done entirely
in the userspace. The management and scheduling is usually done by the thread
libraries provided by high-level languages like C/C++, Java, etc. The primary
advantage here is that we avoid context switches between the userspace
and kernel-space. Also, threads belonging to one process share most of their
execution state like heap memory, instructions, page-tables, etc., except for
stack and CPU-context (registers used). Thus scheduling threads is really fast.
However, the drawback is that even if one of the threads executes
a blocking system call, the entire process and thus the containing threads are
blocked.

Kernel-level threads, on the other hand, are implemented and scheduled by the
operating system. If one of the threads makes a blocking system call, the process
can still run provided that it has other threads that can be scheduled. The
disadvantage here is that every time you schedule another thread, you have to
switch to kernel-space and back.

Lightweight process (LWP) is another type of thread that combines the best of
both worlds. Each process can create multiple LWPs that are run by the operating
system. In Linux, you do this by invoking a clone system call optionally setting
flags to allow memory to be shared. In addition, you can also create user-level
threads (by using the thread library API calls) inside the LWPs. If one thread
blocks, another user-level thread can be scheduled in the same LWP by the thread
library. Finally, if one of the LWPs is removed by the OS (during timer interrupt)
the process can still make progress through its other LWPs.

Now that we know everything one needs to know about threads and processes, let's
focus on how threads are used in distributed systems. Servers can use threads to
serve clients more efficiently by overlapping communication and processing of the
request. Servers can be logically divided into communication threads that wait
for incoming requests. As soon as a request arrives, a new worker thread is
created to process the request. The communication thread immediately goes back
to listen for the new incoming requests.

On the client side threads can be used to reduce the communication latency. An
example would be web-browsers requesting web-pages. Web-pages containing images,
media usually take a long time to load. In order to reduce this latency, browsers
create multiple threads that in turn establish their own connections to the server
for getting the different parts of web-page. 
