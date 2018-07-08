---
layout: post
title: Inter-process communication
---
Processes in distributed systems have to constantly communicate in order to
finish their task. For example, a client that needs some data from a remote
server can get it only if it can send a request message to the server. The server
then replies back with the appropriate data. Distributed systems become useless
if there are no tools available for the processes to communicate. Communication
services are implemented by the message delivery system as a middleware that
sits between the applications and the underlying OS. The message delivery
system is only a part of the distributed systems. As we shall see, the systems
software that makes up a distributed system includes several other
functionalities like fault-tolerance, consistency, synchronization, etc.

Communication can either be asynchronous or synchronous. In asynchronous
communication the sending process sends a message and continues immediately
without waiting for a reply. Similarly, on the receiving side, the receiver
doesn't block itself waiting for incoming requests, rather it is notified when
a new message arrives. In synchronous communication, the sender blocks itself
after sending a message. Synchronization can happen at different stages. A
process may unblock immediately once the message delivery system notifies that
it's going to take care of the delivery, or until the message is delivered to
the receiver, or sometimes until it gets a reply back.

Communication can also be categorized as being transient or persistent. In
transient communication the message is kept by the message delivery system only
as long as the sender and receiver are running. If the receiver is not running
or there is a transmission failure the message is discarded. In contrast, in
persistent communication, the message once delivered to the messaging service
is stored there until it is finally delivered to the sender. The messages are
guaranteed to be delivered to the receiver even if the receiver is temporarily
not running, or there is a transmission failure.

### Remote Procedure Call (RPC)
A Remote Procedure Call is just like a normal procedural call except that the
procedure/function invoked is in the address space of some other process that
resides either in the same system or some remote system. RPC abstraction hides
this fact by installing a stub that forwards the call to a remote procedure to
the appropriate process. If the procedure belongs to a remote process, a 
request (network packet) is made, the equivalent stub on the remote machine then
identifies the appropriate process and invokes the correct procedure. Once the
procedure returns a value it is sent along the same path to the sender.

RPCs are inherently synchronous in nature; sending process waits until it gets
a response. This kind of communication is well suited for client-server
architectures where a client issues a request and waits for a response. However,
every system cannot be modelled as a client-server architecture. For example,
consider email system, there is no waiting for a reply or an acknowledgement for
the mail you've just sent. The recipient need not even be checking his mail at
the time of sending, both the events are totally asynchronous. The mail delivery
system picks up the mail from you and delivers it to the recipient whenever
he/she logs into their account. These kind of communications are handled by 
message queues which is our next topic of discussion.

### Message Queues
Message queues offer applications persistent communication services. Each
process has its own private queue. Messages from other processes to this
process are sent to its private queue. The queue-manager, that is part of the
middleware, is responsible for the delivering messages to the correct process
queue. To accomplish this the queue-manager has to know the network address
of the remote process i.e. the system on which this process runs. It's
hard to keep track of the address of every process in a distributed system 
because of scalability issues and also due to the dynamic nature of the system
where a processes enter and leave the system constantly. To overcome this the
processes (i.e. their systems) involved form a logical network of their own.  
Each system keeps track and sends messages only to its nearest router which
then forwards the message in the direction of the recipient. Bear in mind that
this network and its connections are only logical unlike computer networks that
are physical. Such networks are called overlay networks formed on top of the
computer networks use the latter's services for the actual delivery of message.
Forming overlay networks will help provide the applications with services like
faul-tolerance, reliability, message-filtering, message-priorities and
multicasting etc.

### Multicasting and Gossip
