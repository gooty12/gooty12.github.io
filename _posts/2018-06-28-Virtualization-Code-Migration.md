---
layout: post
title: Virtualization, Code migration
---

Virtualization is a fundamental technique that is applied heavily in Operating
systems. Virtualization aids in effective utilization of limited hardware resources
by providing the abstractions necessary for the sharing of resources. For example,
multiple processes are able to run on a single processor (concurrently) because
operating systems create virtual processors (page-tables, process-tables, etc)
for each running process. Similarly, each running process is created an illusion
of having the entire memory for themselves through virtual memory.

Virtualization is also used to run software in an environment (hardware/software)
that wouldn't otherwise support them. An example would be running a windows
application on a Linux machine. Since the binary format of executables in both
differ, you cannot directly run windows executable files on Linux. A virtual
environment i.e. a windows virtual machine can be installed to run these
 windows applications.
 
Virtual machine monitor (VMM), also called Hypervisor, is a software that can
run multiple operating systems on top of it. VMM runs directly on hardware
and manages all the OSs that run on top of it. VMM is an OS-manager just like
an OS is a process-manager. In distributed systems, servers are usually run
on a VMM so that if some machine fails, all the servers i.e., OSs running on
that machine are migrated to another machine, provided that a VMM runs on the
new machine you don't have to worry about the architectural differences in
the hardware. Thus managing servers becomes much easier. As we shall see next
VMMs help us avoid portability issues during code migration.

### Code migration

Code migration is a technique where a program or sometimes even a running process
is migrated to another system. There are several reasons for doing this, the
primary reason being performance. For example, consider a client that performs
a complex query. The query is usually broken into several sub-queries by the
underlying interpreter. So the client has to make a request for each of them,
wait for the result and finally compute the combined result. That's a lot of
messages! Instead, if the client process is migrated to the server, all the
intermediate communication can be avoided, and just the final result can be sent.
 

Code migration is of two types. If a program text (i.e. instructions) can be
migrated and run on another system then the system is said to support weak
mobility. If a running process can be stopped, migrated and run on other system,
the system is said to support strong mobility. 

Weak mobility is easy to achieve. Strong mobility, on the other hand, is hard to
achieve since a running process is often associated with files, databases, open
communication ports and other local resources that are hard to transfer along.
Unless you can establish some global reference to these resources such that 
any system can access them strong mobility cannot be achieved. VMMs are very
helpful here. If we can migrate the entire operating system along with its
processes, then ports and other resources are retained. We don't have to deal
with the mobility issues.

