---
layout: post
title: Distributed Systems
---

Distributed Systems have become ubiquitous in the recent years, almost all the
major companies today rely heavily on them to serve their massive customer base.
So, being a masters student in Computer Science, I decided to take a course on
it in the next semester. However, since it's summer and there's a lot time to
go, I also decided to learn and research a bit about the field on my own.
After one-and-half months of getting to know them, I should say I've fallen
in love with the field.

For anyone starting on their own, it's not an easy task (but not that difficult
either). There's so much to learn, lot of theory and concepts, so many famous,
classical distributed systems built in the past that are worth studying and 
just as many new cutting-edge systems. On top of it people keep throwing in a
lot of new terminology that can be really tough to digest if you're hearing
it for the first time.

After going through all of this (and more) in the past month, I decided to write
a few blog posts on the subject so that I retain what I've learnt till now and
also to help others. I'm only going to give a high-level overview though. You can
always refer the textbooks _Distributed Systems : Principles and Paradigms_ by
Andrew S. Tanenbum and _Distributed Systems : Concepts and Design_ by George
Coulouris for in-depth discussions. Also, since I'm no expert in this field, 
take everything that I say here with a grain of salt. Let's get started!

### Introduction

Distributed System can be defined as a collection of computers that
appears to its users as a single system.

In short, multiple computers share their work often communicating and sharing
their work through message passing over a network.

This idea of combining the computing power of multiple computers is not entirely
new. Many similar computing techniques were employed in the past. Supercomputers,
for example, combine thousands of processors connected to each other by a local
bus to solve computationally intensive tasks. _Supercomputers_ are used for research
in quantum mechanics, weather forecasting and such. Technically speaking, 
Supercomputers are one single (huge) computer, but the idea is same - you combine
the processing power of many processors to build a much powerful system.

The discovery of networks and other advances in the field later made it possible
to connect several autonomous computers with a high speed LAN. This form of
computing is called _Cluster computing_. Usually, the clusters in a computer
are identical in terms of their computing power and reside in one single room
or a building. Cluster computers work on a single task, they divide their task
and work simultaneously, exploiting the parallelism of the given task. They
communicate their work over the LAN.

Grid computing is yet another form of computing that was designed to utilize the
spare computing power of a collection of computers. In this technique, several
computers already part of the system, come together to form a grid and solve a
particular problem and then immediately disappear. Unlike cluster computers, 
grid computers differ vastly in their computing power and are geographically
more widely dispersed. They use wide-area-networking (WAN) instead of LAN.
Thus grid computing is ideal for non-interactive workloads which require less
communication. Grid computers often fall under different administrative domains.
For example, in academia, computers from different colleges form a grid network
to work on a specific research problem. 

All these forms of computing discussed above are great for computationally
intensive tasks, but not ideal for I/O or data intensive tasks. Imagine a 
google search engine or a facebook
server serving our request. It is safe to assume that at any given point
of time they receive millions of requests. In such cases, it doesn't
matter how powerful your server is, it has only a limited number of ports
to setup connections and handle requests. Moreover, you don't even
need the computing power of a supercomputer here, a typical high-end server
machine is enough. The key here is to distribute the workload and data
among multiple servers and databases so that no single server is heavily
loaded. Distributed Systems are ideal for these purposes.

Although the term Distributed system generally includes Grid and Cluster form
of computing (compute-intensive), most often it only means data-intensive form
of computing. For our discussions let's assume the same.

Distributed systems are often spread across the world. They collaborate and
communicate through networks. So networks are fundamental to the success of
distributed systems and limit the performance too. Networks are unreliable,
and when you cannot communicate to a group of computers, we have a network
partition. There is no way to reach to that group. Distributed systems have to
recover from such things.

One of the key characteristics of a distributed system is partial failure.
Normally when something bad happens (like a hard-drive crash) the entire system
stops working, but in distributed systems even if some of its components (member
computers) don't work, perhaps because of network problems, the rest of the system
should continue to work.

Building a fault-tolerant distributed system is quite hard. In case of network
partitions, we don't know if the message took a long time or the system is dead,
we cannot just conclude that it's a partition. Things are worse if the system that
went down held a lock to some resource. Distributed systems have to recover from
these. 

Replication is a technique widely employed in distributed systems both for saftey 
and performance reasons. If we have multiple copies of the data, we have something
to fall back on in case of a crash. Similarly, multiple servers and databases 
reduce the workload of single system by evenly distributing the requests. Also
by replicating the servers at multiple sites, the communication latency between the
client and server can be reduced bu redirecting the request to the nearest server. However,
it comes with a cost. Now, a single update has to be performed on all the copies
of the system. It turns out that it's not an easy task, a lot of communication
overhead is required. 

Much of what we call distributed systems is middleware that provides these services
to the applications. A running application should never know that it is
interacting with  a group of computers instead of one. Transparency and scalability
are the major goals of distributed systems.


