---
layout: post
title: Dynamo, COPS and RAMCloud
---

### Dynamo

Dynamo is a highly-available, key-value storage system. 

__Query Model__ : Key-value

__Consistency and conflict handling__ : Eventual consistency. Conflicts are
resolved by the application clients during reads (writes continue even during
conflicts, always writeable)

__ACID support__ : No transactions (only single key updates) and no isolation.

### Architecture

__Partitioning__ : Like Chord, data is distributed across multiple storage nodes
using consistent hashing. Uses the concept of _virtual_ nodes to attain a more
uniform data and load distribution. Each physical node is mapped to multiple
virtual nodes depending on its storage capacity (and other factors).

__Replication__ : Each key is assigned to a coordinator node that is
responsible for replicating the key on its N (replication parameter) clockwise
(unique) successor nodes. The list of nodes responsible for storing a
particular key is called _preference_ list.

__Data Versioning__ : Systems with eventual consistency have to deal with
multiple versions of the data. Dynamo uses vector clocks to identify 
inconsistencies. Every object at every node is associated with a vector clock
which is an array of counters where each counter represents the number of
updates at a particular node. A given object version is an ancestor of another
if the first version's counters are all less-than-or-equal to the
corresponding counters of second object's clock. Otherwise, they conflict and 
need to be resolved.

__Read and Write__ : Any operation involving a key _k_ is sent to its
coordinator node. The coordinator node sends the request to the N highest-ranked
nodes in the preference list of _k_. If at least W (write parameter) nodes
reply write is completed, and if at least R (read parameter) nodes reply read
is completed (in case of multiple versions of read all are sent). Quorum-like
protocol is employed; setting W+R>N ensures that at least one of the values
returned is uptodate.

__Hinted Handoff__ : Dynamo uses a sloppy version of Quorum to maintain high
availability. In case any of the first N replicas crash, it stores the data at
the next immediate healthy node. If the failed node comes back, the healthy node
transfers the data back to it. This ensures that at all times at least N nodes
have the copy of a key.

__Replica synchronization__ : In case the node that is temporarily holding the data
also fails then appropriate measures have to be taken to not loose data
permanently. To overcome this, dynamo implements an anti-entropy protocol where
in all the replicas periodically exchange their data with some number of random
nodes. Thus eventually all updates reach all replicas.

For membership and failure detection dynamo makes extensive use of gossip
based protocols.

### COPS

COPS is a key-value store that implements __scalable causal+__ consistency across
the keys stored in an entire cluster (previous causal models required all
keys be present on the same system). In addition, __COPS-GT__  also
supports _get transactions_. Get transactions give us a
consistent view of multiple keys without any locking and the operation also
doesn't block. COPS is categorized as an __ALPS__ system - Avaialable, Low
Latency, Partition tolerant, and highly Scalable.

### Causal+ consistency

Causal consistency requires that all the operations that are causally related
must appear to happen in the causal order.

Operation __a__ causally precedes __b__ if : 

1. If _a_ and _b_ are two operations in the same execution thread and _a_
happens before _b_.
2. If _a_ is a put operation and _b_ is a get that returns the value written
by _a_.

Causal relation is transitive (_a_ -> _b_ and _b_ -> _c_ then _a_ -> _c_).

Causal rleation is a partial relation. It is possible that neither _a_ -> _b_
nor _b_ -> _a_. Such operations are called concurrent operations and they can be
ordered differently by different replicas. This allows flexibility in
implementation and also improves performance. However, if _a_ and _b_ write
to the same key then if they aren't ordered uniformly across all the replicas,
the replicas will permanently diverge. Causal+ consistency avoids this by
supplementing causal model with _convergent conflict handling_ that guarantees
that all conflicting puts are handled in the same manner on all the replicas.

### System Design

Each datacenter in COPS stores the complete replica of the key-value store.
Within a datacenter all the operations are linearizable. This can be done
by partitioning the keyspace into _N_ linearizable partitions.

COPS stores <key, val> tuples only but COPS-GT stores <key, version, val, deps>.
_deps_ in COPS-GT are dependencies of the key, they must be tracked to support
get transactions.

For partitioning COPS uses consistent hashing and replicates the data using
chain replication. Writes in a cluster are synchronously replicated and
asynchronously across the clusters.

Each application server is linked to a COPS client library. The library 
provides the API for accessing the datastore. It tracks the dependencies
of a thread using the thread's _context_ which is a table of
<key, version, deps> entries. Whenever a get operation is executed in the thread,
the library adds the key and its dependencies to the current context. Similarly,
for a put operation, the current set of keys and their most recent versions
form the dependency list. This is added to the context. Note that dependencies
are needed only for COPS-GT. For COPS the context contains only <key, version>
entries.

### Writes

Writes in COPS are done using the _put_after(key, val, [deps], nearest, vers)_ API
call. The _deps_ field is filled only in case of COPS-GT. The _nearest_ field
represents the closest dependencies only. It suffices to check for only closest
dependencies because of their transitive nature. 

The _put_after_ operation checks if all the nearest dependencies are already
written before writing the current key to the store. Since operations in the
local cluster are linearized this is always true for local writes. COPS uses 
Lamport timestamps to give a global order on
all the writes involving a single key. Lamport timestamps respect potential
causality and avoid conflicts (last-writer always wins).

Unlike local writes, remote writes - writes replicated on remote replicas - cannot be guaranteed that all
the dependencies are satisfied. The client has to explicitly check if all the
dependencies are satisfied before committing a write. This is done using the
_dep_check(key, version)_ API call.

### Reads

COPS provides the _get_by_version(key, version)_ API for read operations.
If version=LATEST, the latest version of a key is returned and if a specific
version is mentioned that version is returned.

### Get transactions
Get transactions return a consistent view of multiple keys. Although individual
reads are causally consistent, reading involving multiple keys can be
inconsistent. For example, let's say you want to read two keys _k1_, _k2_, 
and that _k2_ depends on _k1_. Suppose after reading the key _k1_ and before reading
_k2_ an update is performed on _k1_ (by some other thread). The subsequent read on _k2_
on _k2_ returns a value that is consistent with the newer version of
_k1_ but not the retrieved version. To avoid this, COPS-GT provides get
transactions.

Get transaction operation issues reads to all the specified keys concurrently.
It then goes through the dependency list of each key, and checks if any of the
dependent keys also happens to be one of the retrieved keys. In such a case, we
need to ensure the retrieved value is up to date i.e., its version is 
greater-than-or-equal to the version specified in the dependency list. After 
scanning all the dependencies, it issues gets on all the keys for which 
newer versions are needed concurrently.

### RAMCloud
