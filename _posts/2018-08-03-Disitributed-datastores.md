---
title: Distributed databases
layout: post
---

Modern applications - like Google search, Facebook, etc - are data-driven; they
need to store and operate on tons of data. The traditional software architecture
where a single system stores the entire data on its disk and operates on it (all
alone) doesn't work well. To scale such applications (potentially serving
millions of clients) you need to harness the power of thousands of systems by
making them work in concert. Typically, a group of servers (called _web-servers_)
are dedicated to servicing client requests and another group for just storing the
data. Web-servers process client requests by querying the appropriate data
server for the client-requested data and sending back the processed results.

To make sure that the data is not lost - perhaps due to disk failures, network
issues - usually data is replicated on multiple systems. Replication makes 
distributed systems fault-tolerant. It also improves performance since you can
direct a given client request to any of the multiple servers available without
making any single replica a performance bottleneck.

This disributed architecture, although necessary and functional, is quite hard 
to get it right. You need to partition your data among the servers uniformly,
and remember exactly where each piece of data resides so
that the webservers can direct the  data request appropriately. The replicas must be
kept consistent i.e., all the servers must give the same value when queried for a
given data item. Servers crash, disks fail, networks disconnect, messages
timeout. You should be able to operate under such conditions and do so in an
efficient manner. The relational datamodel, concurrency control techniques,
consistency guarantees that are available in a traditional (single-system)
database cannot be implemented in disitributed databases, at least not in an
efficient way. Consequently, distributed datastores have to make appropriate
trade-offs and provide only those features and guarantees that are essential.


### Traditional databases
Traditional databases support transactions. A transaction is a single logical
operation that consists of potentially multiple reads and writes. For example,
withdrawing amount from your bank account is a transaction with the following operations:
reading your balance and writing back the updated balance. For transactions to
work as expected, databases need to satisfy certain properties known as __ACID__
properties. ACID stands for Atomicity, Consistency, Isolation, and Durability.

__Atomicity__ says that operations of a transaction commit as a whole or none at
all. It shouldn't be the case that you read the balance and ( since you've enough
balance ) then withdraw money, but did not do the final step of updating your
balance. 

__Consistency__ says that transactions should leave the database in a consistent
state after committing. Consistent here means that the database should still
satisfy all its integrity constraints. For example balance should never become
negative after withdrawal.

Consistency is actually an overloaded term. In
distributed systems, Consistency means that multiple copies of a given data
all have the same value. It would be better to think of __C__ in ACID as
standing for correctness.

__Isolation__ guarantees correctness of data in the face of concurrent updates.
Atomicity and Consistency talk about a single transaction happening in exclusion.
However, when multiple transactions are happening at the same time, operations
from one should not interfere with the other. It should appear as if these
concurrent transactions are happening one after the other in some __serial__ order. 

For example, suppose your bank account was a joint account with a balance of 150$. If you and the
other account holder tried to withdraw 100$ simultaneously, the bank should allow only one
of you to withdraw (because of insufficient funds). Although both of you are operating concurrently, it should
look as if you're trying one after the other and the last person should be
denied their withdrawal request.

__Durability__ says that once a transaction commits all its updates are seen
from the next transaction onwards. After your withdrawal transaction, any
subsequent read on balance should give you the updated
balance. The data should not be lost because of server crash or a disk failure. 


### Distributed databases:
Distributed databases partition and store their data on hundreds of servers.
Although this improves performance, it also limits them in many ways. For example,
relational datamodel which is the most widely used model in general is hard to
implement in a scalable and efficient manner on distributed platforms.

Relational datamodel and its operators (join, select etc) involve scanning the entire
table ( multiple tables in case of join ). Since the table(s) could be stored
on different servers, there will be multiple requests over the network to
all of these servers. That's a lot work and the worst part is only a fraction
of the data gathered actually satisfies the selection (or join) operation's criteria. So distributed databases
deviate from relational datamodel and adopt __NOSQL__. (NOSQL simplifies
datamodel by giving up certain aspects of relational model)

Transactions (and therefore ACID properties) are also generally not supported
because it involves getting locks on the keys that are stored across multiple
servers. Additionally, the setup of distributed systems creates new problems
that are unique to distributed databases; since data is replicated care
should be taken that all replicas are in sync. Also, you've to ensure that
data is always available. Data might become unavailable because of network
failures, server crashes etc. 

### CAP theorem 
CAP theorem provides an useful guideline for describing and designing
distributed systems. __CAP__ stands for Consisteny, Availability and Partition
tolerance (all explained below). CAP theorem states that distributed systems
can have only two (any two) out of these three properties.

__Consistency__ requires that a read return the most recent value. The
implication here is that a write on one system should be instantaneously
applied to all the replicas (giving us a single-copy system).

__Availability__ states that a request to a non-failing node should always result in
a response. (response = value of requested data; not some msg with 404/timeout
error).

__Partition tolerance__ requires that the system functions even during network
partitions ( several groups of systems each disconnected entirely from the
other). The system should serve its clients in such a way that it still respects 
all the promises/guarantees made regarding its availability and consistency.

Network partitions are unavoidable in today's world. So it is imperative that
the system be partition tolerant. Thus you have to make choice between
Consistency and Availability. Thus a distributed
system can either be __CP__ or __AP__ (but never CA).

### Drawbacks of CAP theorem

The choice between consistency and availability happens only during network
partitions. However, partitions are rare, and so during normal mode of operation
there is no need to give up on either C or A. This is particularly true if 
you realise that consistency and availability are more of a spectrum and not binary.
Consistency in CAP only refers to strong consistency (instantaneous updates)
which is theoretically impossible; it takes finite amount of time to propagate
writes to replicas. Instead of strong consistency we can choose weaker
consistency models. Depending on the degree of consistency chosen the 
system can either be highly-available (almost all the time returns a response) or
rarely-available.

CAP also doesn't take into account the latency of operations, but latency is
the most dominating factor nowadays. In fact,
many systems today compromise on almost all other design parameters for low
latency. In such a scenario, it would be wise to describe the system in terms
of consistency and availability during partitions, and consistency and latency
in the absence of partitions. Strongly consistent systems are less-available
(during partitions) and of high-latency (in their absence). Weakly consistent
systems are highly-available and are of low-latency. 

### Consistency models

__Linearizability__ is the strongest form of consistency. All the operations
must appear to have happened in some sequential order and the reads must
always return the value of the most recent write. The operations are ordered
according to real time - each operation happens at some point between 
its invocation and completion. There is not much
reordering possible except in case of overlapping operations. 

Linearizability satisfies composability property; if all the operations on data _var1_ are
linearizable and similarly for operations on _var2_, then the combined group
of operations on _var1_ and _var2_ is also linearizable.

Linearizability as such is not possible in wide-area networks because message
propagation over networks takes a finite amount of time. Read operations
therefore might not return the most recent write value. 

__Sequential Consistency__ requires that all operations happen in some
sequential order but the final ordering need not correspond to real time.
Therefore the operations can be reordered. Sequential consistency is not
composable.

__Causal Consistency__ orders only operations that are causally related (unlike 
sequential consistency that orders all ). If event __A__ causes event
__B__ then the two events are said to be causally related and they are always
ordered as event _A_ __followed__ by event _B_. Causal relation is
partial; it is possible that neither __A__ causes __B__ nor __B__ causes __A__
Such events are called concurrent events and they can be ordered differently
on different systems.

__Eventual Consistency__ is the weakest form of consistency. The only
guarantee it provides is that, in the absence of updates, all the replicas
will eventually have the same copy.
