---
layout: post
title: Distributed Systems
---
In a series of posts, starting from this, I will discuss how one can get started
with Distributed Systems on their own. I will divide the whole theory into few
topics, give a brief introduction and point to other resources that can help 
you learn things in detail. Note that whatever I'm going to discuss here, 
comes from my own experience of learning distributed systems on my own. So, 
keep in mind that this is not the only way to go about it. However, almost
everything that I discuss is based on the text _Distributed Systems : 
Principles and Paradigms_ by Andrew S. Tanenbum. So, hopefully, it's a great to
get started. Let's dive right in!

Distributed Systems Concepts can be broadly divided as follows:
1. Introduction
2. Distributed Systems Architectures.
3. Communication.
4. Time and Synchronization
5. Replication
6. Failure

### Introduction

So, what is a distributed system actually? A definition is in order:

<pre>
A distributed system is a collection of independent computers that apperars to
its users as a single coherent system.
</pre>

As the definition says distributed systems are made of several independent
computers. However, users should not be aware of this, so it is implicit that
these computers collaborate among themselves without the user ever noticing.
Collaboration is achieved through network communication, which is vital to the
successful functioning of any distributed system, so important that it can make
or break the entire system.

The definition doesn't say anything about these computer systems. In principle,
they can be anything ranging from supercomputers to a low-end desktop computer,
and each might have a different architecture with different operating systems
running on top it. In order to provide a uniform access to the applications or
clients, distributed systems are often organised as a middleware layer sitting
between the hardware/OS and client applications.

