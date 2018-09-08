---
layout: post
title: "Designing Data-Intensive Applications"
date: 2018-7-9 11:18:57
categories: books
tags: [books]
excerpt: "Personal review and notes of Designing Data-Intensive Applications"
---

# Summary

## Part I: Foundations of Data Systems

The book begins with a quick look at the characteristics of systems (reliability,
maintainability...) to set a common language to talk about. This part assumes a
single machine.

Then it jumps into different data models from relational to graph. Although
it's brief, it's an interesting look at the essence of them and the goals
that each one pursues. For example, it focuses on the convenience of different
models for representing many-to-many relationships as the basis for new needs.

Data models without persistence might be interesting from a philosophical point
of view but not from a practical one, so storage must be studied. The third chapter
explains OLTP vs OLAP systems. Within OLTP systems there's a great analysis of
log-like vs B-tree-like structures.

This part continues with an in-depth look at the lower detail: the encoding. It
compares several approaches, from language-dependent serialization to textual
formats (CSV, JSON...) or binary, schema-based formats (Protocol Buffers,
Avro...). It ends with a summary of data flows, data protocols for
communication (RPC, databases, REST, actor model...

## Part II: Distributed Data

The first step is replication, which is driven by the need for availability,
improving the latency, scalability or disconnected operation.
The main approaches are single-leader, multi-leader, and leaderless replication.
Replication can be synchronous or asynchronous. Replication lag is a challenge
for read-after-write consistency, monotonic reads, and consistent prefix reads.

For scalability, the main tool might be partitioning. The two main approaches
are key range partitioning and hash partitioning. For secondary indexes,
there are document-partitioned indexes and term-partitioned indexes. And you
need a way to route requests, either client know, a routing tier, or the
nodes themselves.

Transactions try to make concurrency easier. The transaction chapter describes
the whole set of issues that can occur (dirty reads, dirty writes, read skew,
lost updates, write skew, phantom reads), the different isolation levels with
the guarantees that they provide (read committed, snapshot isolation,
serializable). It ends with three different approaches to serialization
(literally in order, two-phase locking, and serializable snapshot isolation).

In distributed environments, a whole range of problems may occur, from
network failures to pauses. Systems must be prepared to detect and tolerate
them for reliability.

Consistency and consensus begin learning about linearizability, appealing but
slow. A looser guarantee is causality, and requires less coordination and has
better tolerance for network issues. But not everything can be implemented with
causal guarantees. Consensus algorithms lay in the middle. Consensus, which
is coming to an agreement among several nodes, is very useful for many tasks
and guarantees, but become really hard if you need fault tolerance. Anyway,
it comes with a cost.

## Part III: Derived Data

UNIX tools are a great example of batch processing tools because its
simplicity and composability make them really powerful. They're limited to
single nodes, though. MapReduce can be seen as the UNIX tools within a cluster,
with a distributed filesystem and small jobs that are used in workflows
(but with an intermediate state). Distributed batch processing must solve
partitioning and fault-tolerance, and implement join mechanisms.

Steam processing is similar to batching but unbounded. There are two types
of message brokers: AMQP/JMS style (with producers/consumers and explicit
message processing acknowledgment) and log-based (all messages in a partition
are read by a consumer). Log-based has similarities with the DB replication
log. You could even plug a change data capture system on top of it to turn
a DB into an event stream.

The final chapter exposes the author's vision about the future of data. As there's
no chance for a one-size-fits-all solution, we come to the realization that
some data integration is mandatory, and batch processing and event streams
help with that. Thinking about that and dataflows as transformations, the author
proposes _unbundling_ the components of a database and building an application
by composing those loosely coupled components. Anyway, we must be aware of
fault tolerance and performance balance. And, finally, there's a section about
data ethics.

# MHO

Depending on your knowledge and expertise you'll find more or less new stuff
in this book. For example, once you have a little background with databases
you'll probably know most of Part I, but even in these foundation chapters,
there are some gems, such as explaining serialization in depth, including
Protocol Buffer, Thrift or Avro. I find these chapters useful even if
they don't provide a lot of _new_ information, because they help in remembering
details and tradeoffs. Comparing overlapping technologies and designs is
always useful.

Despite the title, it's also a must read if you're interested in distributed
systems, as a consequence of Internet-scale data needing distributed databases.
The book spends many pages around distributed systems failures, consensus...

While it's not a book about specific technologies, it mentions many of the
state-of-the-art ones with context and explaining the differences. You can
learn how come ZooKeeper seems omnipresent or the different guarantees provided
by different distributed databases.

Although it's mostly abstract and generic, it also exposes real scenarios
and tooling best practices, such as "how do people load the output of a
MapReduce job into a database".

References! I mean... REFERENCES! There are chapters with +100 references. You
might be interested in reading all of them if, let's say, you're preparing for
a position in Google, but otherwise it's overwhelming. I will certainly go back
to the book from time to time to recall some topics, and that will be a great
moment to go through the references :) In fact, the heavy use of
internal references through the book makes me think that the author is aware of
this usage pattern ;)

It does a remarkable job of capturing the essence of the underlying common
characteristics. The climax of this might be the parallelism between creating
an index in a DB and creating a new follower replica, but there are many cases.
The last chapter about the future leverages that into a broader vision of data
systems.

As a guide for future work and application design, the idea of _unbundling the
database_ is really powerful and feels like the natural consequence of the
essence distillation effort that the author does. He goes to the underlying
characteristics and the problem that features solve and propose a loosely
coupled design for applications. Something worth exploring!

As a final closure, he writes down his thoughts about data ethics. There are
many relevant thoughts and memorable sentences, I'll just pick one:

> A blind belief in the supremacy of data for making decisions is not only
> delusional, it is positively dangerous

IMHO this book is a modern classic, a must read for every software engineer and
developer. I'm certain that it will be reread it from time.

# Notes

<details>
<summary markdown="1">
**Click** to see my collection of notes. It's not a summary at all but a mix of
highlights, definitions, important stuff or things that I didn't know.
</summary>

<div markdown="1">
## Part I: Foundations of Data Systems

### 1. Reliable, Scalable, and Maintainable Applications

> (...) there are datastores that are also used as mes‐
sage queues (Redis), and there are message queues with database-like durability
guarantees (Apache Kafka). The boundaries between the categories are becoming blurred.

> Reliability:
> the system should continue to work correctly (performing the correct function at
the desired level of performance) even in the face of adversity (hardware or software
faults, and even human error).

> Scalability:
> as the system grows (in data volume, traffic volume, or complexity), there should
be reasonable ways of dealing with that growth.

> Maintainability
> Over time, many different people will work on the system (engineering and 
operations, both maintaining current behavior and adapting the system to new use
cases), and they should all be able to work on it productively.

#### Reliability

> Note that a fault is not the same as a failure. **A _fault_ is (...) one
component of the system deviating from its spec**, whereas **a _failure_ is when the system as a
whole stops providing the required service to the user**.

> there is a move toward systems that can tolerate the loss of entire machines, by
using software fault-tolerance techniques in preference or in addition to hardware
redundancy.

#### Scalability

##### Describing load

> we need to succinctly describe the current load on the system; only then can we
discuss growth questions. Load can be described
with a few numbers which we call load parameters. The best choice of parameters
depends on the architecture of your system: it may be requests per second to a web
server, the ratio of reads to writes in a database, the number of simultaneously active
users in a chat room, the hit rate on a cache, or something else.

##### Describing performance

> you can investigate what happens
when the load increases. You can look at it in two ways:
- When you increase a load parameter and keep the system resources (CPU, memory,
network bandwidth, etc.) unchanged, how is the performance of your system
affected?
- When you increase a load parameter, how much do you need to increase the
resources if you want to keep performance unchanged?

> In a batch processing system such as Hadoop, we usually care about throughput —the
number of records we can process per second, or the total time it takes to run a job
on a dataset of a certain size. In online systems, what’s usually more important is the
service’s response time—that is, the time between a client sending a request and
receiving a response.

> Latency and response time are often used synonymously, but they
are not the same. The response time is what the client sees: besides
the actual time to process the request (the service time), it includes
network delays and queueing delays. Latency is the duration that a
request is waiting to be handled—during which it is latent, awaiting service.

##### Approaches for coping with load

Scaling up vs. scaling out

> It is conceivable that distributed
data systems will become the default in the future, even for use cases that don’t
handle large volumes of data or traffic.

#### Maintainability

Operability + Simplicity + Evolvability




### 2. Data Models and Query Languages

**The network, hierarchical and document models**
- Network model is a generalization of the hierarchical model. In the tree
structure of the hierarchical model, every record has exactly one parent; in the
network model, a record could have multiple parents
  - The links between records in the network model were not foreign keys, but more like
pointers in a programming language (while still being stored on disk). The only way
of accessing a record was to follow a path from a root record along these chains of
links. This was called an access path.
- Document databases reverted back to the hierarchical model in one aspect: storing
nested records within their parent record rather than in a separate
table.
  - However, when it comes to representing many-to-one and many-to-many relation‐
ships, relational and document databases are not fundamentally different: in both
cases, the related item is referenced by a unique identifier, which is called a foreign
key in the relational model and a document reference in the document model.

#### Graph-Like Data Models

##### Property Graphs

- **Vertex**: id + outgoing edges + incoming edges + properties (k-v).
- **Edge**: id + tail vertex + head vertex + label (kind of relationship) +
            properties (k-v).

- Any vertex can have an edge connecting any other vertex.
- Given any vertex yo can efficiently find the incoming and outgoing vertex and
transverse the graph.
- Labels allow storing different kinds of information in a single graph.

##### Triple-Stores and SPARQL

Mostly equivalent to property graphs.

Related to the _semantic web_ through RDF, which is a format to specify data
combinable with other sources using the same namespaces and attributes.

> SPARQL is a query language for triple stores using the RDF model.

##### The Foundation: Datalog

Subset of Prolog, close to triple-store model but generalized.

Just like Prolog, it's based on rules (that can be reusable among different
queries).






### 3. Storage and Retrieval

#### Data Structures that Power Your Database

Log: append-only data file.

Index: additional structure derived from the primary data for improving
performance.

Log compaction: removal of old values of keys (by keeping just the last one).

##### Hash Indexes

Hash indexes: n-memory hash from key to a disk position. Example: Bitcask (default storage
engine in Riak).

Tombstone: record to mark deletion in a log.

Append-only logs are really convenient:
- Appending and merging are sequential write operations, faster than random writes.
- Concurrency and crash recovery are simpler.

... but have limitations:
- Its index hash table must fit in memory.
- Range queries are not efficient.

##### SSTables and LSM-Trees

**Sorted String Table (SSTable)**: storage segments that are sorted by key , and each key appears only once within each merged segment. Advantages over log segments with hash indexes:

- Merging is simple and efficient even if it doesn't fit in memory, with mergesort.
- Indexes can be sparse (thanks to sorting), you can scan from the previous known one.
- Previous approach leads to group records into blocks and compress them before writing to disk.

Constructing and maintaining SSTables:

1. On write, add it to a memtable (an in-memory balanced tree data structure such as red-black trees or AVL trees...).
2. When the memtable gets bigger than a threshold, write it out to disk, becoming the most recent segment.
3. To serve a read request, first try at the memtable, then use the on-disk segments.
4. From time to time, merge and compact.

Caveat: on crash, the most recent entries at the memtable would be lost, so they have to be written to a (unsorted) "emergency" log as well.

Implementation details:

- Looking up keys that doesn't exist at the database is slow, because it checks the memtable and all segments. To avoid that you can use bloom filters to tell whether keys appear or not.
- Strategies for order and timing of merging and compacting:
  - Size-tiered: newer and smaller SSTables are merged into older, larger ones.
  - Leveled: the key range is split up into smaller SSTables, and older data is moved into separate "levels".

##### B-Trees

Standard index implementation in many databases.

Key-value pairs sorted by key, allowing efficient key-value lookups and range queries.

While previous log-structured indexes break the database down into variable-size segments, B-trees break the database down into fixed-size blocks and read one page at a time. This corresponds more closely to the underlying hardware.

_Note: if you want to learn more about B-Trees, read SQL Performance Explained ([slides](https://juanignaciosl.github.io/postgresql/2016/06/04/sql-performance-explained-cylicon-valley.html))._

##### Comparing B-Trees and LSM-Trees

As a rule of thumb, B-trees are faster for reads, and LSM-trees are faster for writes. But that's often sensitive to workload, so you must test them with your particular workload.

Advantages of LSM-trees:
- Write amplification (one write resulting in many writes to the disk). In write-heavy applications the bottleneck can be the write rate, and write amplification can have a direct cost:
  - B-trees require writing several times: at least to the WAL and the tree page, but also page splits. And they always write full pages.
  - Log-structured indexes rewrite data due to compaction and merging. In general they can sustain higher write throughput because write amplification is usually lower and because compacting is sequential. If disk is magnetic this is specially important.
- LSM-trees can be compressed better, producing smaller files on disk.
- B-trees have fragmentation: then a page is split or a row can't fit in a existing page, some space in a page remains unused. LSM-trees are not page-oriented and rewrites remove fragmentation, so they have lower storage overloads.

Downsides of LSM-trees:
- Compaction can interfere with performance because of limited disk resources, producing less predictable response times.
- If write throughput is high compaction might not be able to keep up with the incoming writes rate, so the number of unmerged segments keeps increasing until you run out of space.
- There might be multiple copies of keys. At transactional environments using locks next to the keys is convenient, so B-Trees are more suitable.

#### Transaction Processing or Analytics?

**OLTP** (online transaction processing): access pattern of looking up a small number of records by key, with an index, and then insert or update based on user's input.

**OLAP** (online analytic processing): scan a few columns over a huge number of records and calculate aggregate statistics.

##### Data Warehousing

**Data warehouse**: separate, read-only database for analytics purposes.

##### Stars and Snowflakes: Schemas for Analytics

Most data warehouses are modeled in star schema: a large fact table with attributes and dimensions (FKs to other tables).

#### Column-Oriented Storage

Analytics query often specify a subset of columns, making column-oriented storage more suitable.

##### Column compression

Column values often are repetitive, making bitmap encoding compression very attractive (storing a value table and its indexes instead of the value itself), and even compressing the indexes themselves.




### 4. Encoding and Evolution

#### Formats for encoding data

Using language-specific formats is not recommended (you get coupled to
languages, and they're usually not efficient), and widespread encoding
formats such as XML, JSON or CSV are limited (ambiguous, incomplete and not
always without proper schema specification support).

##### Thrift and Protocol Buffers

They're binary encoding libraries that require an schema.libraries that require an schema.

Thrift has three different binary encoding formats. It uses aliases for fields.
Protocol Buffers is quite similar.

Forward compatibility (old code can read records that are written by new code)
is guaranteed:
- New fields are just ignored by the code, as (new) tag aren't read.
- Renaming a field just renames it in the schema, the tag number is kept.

Backwards compatibility (can new code read old records?):
- As long as each field has a unique tag number, it can be read.
- If you add a new field, it can't be required, because old records can't have it.

Removing a field is just like adding a field, with backward/forward concerns
reversed (you can only remove a field that is optional) and you can't reuse the
tag number.

Changing the type can lead to precision loss.

Protocol buffers doesn't have array types but a `repeated` keyword. You could
change from `optional` to `repeated`.

Thrift does have array types. You can't change from single-valued to
multi-valued as with Protocol Buffers, but you can have nested lists.

##### Avro

Thrift wasn't a good fit for Hadoop, specially for dynamically generated
schemas and dynamic languages, so Apache came out with Avro.

Schema-based. Both reader and writer must have compatible schemas, because the
binary encoding is just a concatenation of the values, without type or field
information.

Reader and writer schemas don't have to be _the same_, though. The library
compares both schemas and perform needed transformations, such as different
order or filling missing values with default ones.

For schema evolution, you must ensure that new and removed fields have default
values. `null` is supported through union types.

The schema can be bundled in the file if it has a lot of records (so it's not
a problem from storage point of view). A database could store a versioned
list of schemas. Finally, for network communication, the schema can be
negotiated in advance.

#### Modes of Dataflow

##### Dataflow Through Databases

##### Dataflow Through Services: REST and RPC



## Part II: Distributed Data

You want to distribute data because of scalability, fault tolerance/HA and/or
latency.

A first approach for scaling to higher load is a bigger machine, but the cost
grows faster than linearly and is limited to a single geographic location.

This part focuses on "shared-nothing architectures".

Ways to distribute data: replication vs. partitioning.




### 5. Replication

#### Leaders and Followers

Clients must send the writes to the leader, which propagates the changes to
the followers through the replication log. Reads can be queried both to leaders
and followers.

Followers can be synchronous or asynchronous. Synchronous guarantee that the
change is propagated, but might cause writes to fail if they're not available.

##### Handling Node Outages

**Follower failure: _Catch-up recovery_**. As followers keep a log, if there's
been a crash or network interruption, they can request the data from the last
one processed.

**Leader failure: _Failover_**. One of the followers needs to be promoted to
leader, clients need to be reconfigured, and other followers need to start
consuming data from the new leader.

##### Implementation of Replication Logs

- *Statement-based replication*: compact but with some issues, such as
non-deterministic functions (`now()`) or transactions.
- *Write-ahead log (WAL) shipping*: there's a log containing all writes, so
it can be sent to followers, as it allows regenerating the DB. The main
problem is that it describes the data on a very low level (byte changes in
disk blocks), so it depends on the storage engine.
- *Logical (row-based) log replication*: enough information about the rows that
change.
- *Trigger-based replication*: application-level code to perform changes.
Useful for complex (ad-hoc) situations.

#### Problems with Replication Lag

Synchronous replication is not feasible in practice (a single node failure can
bring the whole system down), so you must deal with asynchronous replication
lag. That comes with problems.

##### Reading Your Own Writes

If a user performs a change and immediately reads from a follower that doesn't
have the change yet the data won't be read. Read-after-write consistency might
be needed ("read-your-writes"). Techniques:
- If what a user can modify is not a big subset of the data, if you read
something that the user might've modified, read it from the leader.
- Decide whether reading from the leader or not. For example, tracking the
time before the last update or monitor the replication lag.
- Remember at the client the timestamp of the most recent write and ensure that
the served read is up-to-date. This is harder in a multi-device user
environment.

##### Monotonic reads

A user might do a query to one follower and then to another one that is behind
the previous one (in terms of replication), so the user perception might be
"going back in time". Monotonic reads is having the guarantee that this won't
happen. You can guarantee this by having users asking to the same followers.

##### Consistent Prefix Reads

Causally relationships should be read in order. For example, a question-answer
conversation must be read by people watching it in order. This is a problem at
sharded systems. You might need to record causal relationships explicitly.

#### Multi-Leader Replication

In multi-leader configurations each leader is also a follower to the other
leaders.

A conflict resolution system is needed.

You probably need multi-leader replication in **multi-datacenter setups**.
Otherwise the whole system might be slower (due to the need of routing all
reads through the leader).

**Offline devices support** is another case of multi-leader replication, as
they also support writes.

**Real-time collaborative editing** are essentially multi-leader replication
cases.

##### Handling Write Conflicts

There are several approaches. One of the most interesting one is **converging
towards a consistent state**, because it avoids locks or simulating a single
leader approach. The goal is allow conflicting writes but set up a conflict
resolution strategy so all DBs end in the same state. Strategy example:
- Bind the edition with a timestamp and keep the last one.
- Bind a unique id and keep the higher one.
- Merge.
- Set up an explicit structure and somehow prompt for resolution.

##### Automatic Conflict Resolution

Ongoing research:
- Conflict-free replicated datatypes (CRDTs): data structures (sets, maps...)
that can be concurrently edited by multiple users, resolving conflicts in
sensible ways with two-way merges. Riak has implemented some.
- Mergeable persistent data structures: track history (similar to Git) and
use a three-way merge function.
- Operational transformation: algorithm designed for concurrent editing of
an ordered list of items (such as characters at Etherpad or Google Docs, which
use it).

##### Multi-Leader Replication Topologies

The most common approaches are circular, star and all-to-all topologies.

Star and all-to-all topologies make all data go through all nodes, one by
one. Ids are assigned to avoid infinite loops. One failure can stop
propagation. On the other hand, all-to-all strategies can have other issues,
as ordering is not granted, potentially causing casualty errors. To avoid
that you can use _version vectors_.

#### Leaderless replication

Clients can send writes to any DB. Dynamo, Riak, Cassandra and Voldemort use
this approach.

In these setups, a client send both writes and reads to multiple nodes. That
way you don't even need to handle failovers.

Mechanisms to cope with failover:
- Read repair: when a client reads data and some of the answers are outdated,
they write the result back to the outdated replicas. This works well with
frequent reads.
- Anti-entropy process: a background process is always monitoring and updating
missing data.

**Quorum reads and writes**: given `n` replicas, if the write must be confirmed
by `w` writes and `r` nodes must be queried in each read, `w + r > n`. As at
least one of the node `r` nodes must be up to date, we'd get an up-to-date
value. This also allows some tolerance to unavailable nodes:
- if `w < n` writes can be processed if a node is unavailable.
- if `r < n` reads can be processed if a node is unavailable.
- with `n=3, w=2, r=2` one unavailable nodes can be tolerated.
- with `n=5, w=3, r=3` two unavailable nodes can be tolerated.

Normally, reads and writes are sent to all nodes. `w` and `r` determine the
number of nodes that we wait for.

##### Limitations of Quorum Consistency

Even with quourum, edge cases are likely to happen, because of hardware
failures, failed reads, replication lag and other. This kind of system is
suited for applications that can cope with eventual consistency.
Anyway, monitoring replication lag and staleness is a key practice in a
healthy system.

##### Sloppy Quorums and Hinted Handoff

**Sloppy quorum**: in case of not reaching `w` nodes in a write, instead of
failing, choose a different set of `w` nodes ("overriding the normal sharding
policy"). This assumes that you could get an old value. When the original `w`
nodes are back, push the write there.

In multi-datacenter replication, systems like Cassandra, Voldemort or Riak
keep most of the communication local by waiting only for nodes within the same
datacenter (although the writes are sent asynchronously to all replicas in
all datacenters).

##### Detecting Concurrent Writes

**Last write wins (LWW)**: discarding concurrent writes. When there are
concurrent writes, they get assigned a timestamp, and older keys are
overwritten. This guarantees convergence at the cost of data loss.
A recommended way of using Cassandra is using a UUID as the key, so
each write is unique (and immutable), so concurrent updates are impossible.

_Note that two operations are said to be concurrent if they don't know about
each other, no matter the time. That's because time in distributed systems
is complicated._

##### Capturing the happens-before relationship

Clients can send the last known version number, and the server can return
all the values that have not been overwritten, and the actual version number
after the write. That way, the server
can determine if operations are concurrent based on the version numbers.

##### Merging concurrently written values

Although previous algorithm ensures that there's no data loss, clients must
resolve the conflict, for example, with unions. But plain unions would
drop deletions. To avoid that, instead of deleting items, items are marked
as deleted, thus allowing unions.

##### Version vectors

In leaderless replication, instead of keeping a single version number, a
different version number is tracked in each replica, allowing to
differentiate between an overwrite and a concurrent write.


### 6. Partitioning

The main reason for _partitioning_ (breaking up data into partitions) is
scalability.

#### Partitioning and Replication

Partitioning is usually combined with replication.

#### Partitioning of Key-Value Data

Partitioning schema is important because if it were _skewed_ one node might
have to handle most of the load, making partitioning inefficient.

##### Partitioning by Key Range

Given a key with a continuous range of values, we can split it among nodes.
Boundaries could be chosen automatically or by an administrator.
Within a partition we can keep the keys in order, making range scans easy.
This can lead to hot spots. For example, if the key were the creation date,
all writes would go to the same node. In these cases you could prepend a
prefix that distributed the load among the nodes.

##### Partitioning by Hash of Key

To distribute the load evenly you could apply a hash function to the key and
then partition by the range of the hash of the key.
Although this balances the load, it makes range searches by key very
inefficient.
Cassandra uses a compromise: a table can be declared with a compound PK, and
only the first column is hashed. So, queries where the first column has a
fixed values are efficient (as all data is at the same node, and sorted by
the rest of columns).

##### Skewed Workloads and Relieving Hot Spots

Even hash partitioning can't avoid hotspots if one key is used intensively.
In those scenarios you could add random numbers in front of "hot keys" to
balance the partitioning, at the cost of increasing the bookkeeping (you have
to query all nodes for queries on hot keys. That tradeoff nowadays must be
handled at application level.

#### Partitioning and Secondary Indexes

While secondary indexes are the bread and butter to relational databases,
distributed DBs have avoided them because they don't match well with
partitioning.

##### Partitioning Secondary Indexes by Document

The index contains the keys to the documents within its partition that contain
the value.
With this system, each partition is completely separated and contains the 
secondary index for the documents at it. Also called "local indexes".
Using it requires querying all partitions.
This schema is prone to tail latency amplification.
Most vendors recommend a partitioning scheme where secondary indexes can be
served from the same partition, but that's not always possible.

##### Partitioning Secondary Indexes by Term

Instead of having a local index for each partition, the index itself can be
partitioned. For example, we might have a secondary index on a term and have
the index of values a-m in one partition and the index of n-z in other. This
way you only have to query one index.

It makes reads more efficient but writes are slower, as a single document
write might have to update indexes at many partitions.

#### Rebalancing Partitions

Rebalancing: moving data (partitions) from one node to another.

Don't use `hash mod N`, because if `N` changes you have to move your data all
around.

Using a **fixed number of partitions**, relatively larger than N, and assign them
to the nodes, is a easy and good approach. The main problem is choosing a
number that is not too low (because it's an upper boundary for N) nor too
high (managing partitions has a cost).

Specially for key-range partitioning, but also useful for hash partitioning,
there's **dynamic partitioning**: the DB starts with a single partition (or a
preconfigured _pre-splitting_ and as ranges are populated, partitions are split
or merged. This optimizes the partition overhead.

Previous approaches were proportional to the size of the dataset. For hash
partitioning you can use **partitioning proportionally to nodes**: each node
has a fixed number of partitions. This keeps the size of each partition
fairly stable.

##### Operations: Automatic or Manual Rebalancing

Balancing is expensive, so automatic balancing can lead to surprises to avoid
network or IO overload. Putting a human in the loop is often convenient.

#### Request Routing

In order to do a request, we need to know which node to connect to. This is a
problem of _service discovery_. We could delegate on the client, on a routing
tier, or in the nodes themselves.
Be it the routing engine, the client or the nodes, we need a mechanism that
learns about changes in partitions so the reqeusts are sent to the right nodes.
Many technologies (HBase, Kafka...) use ZooKeeper for service discovery. It
talks to the nodes and keep track of the IPs and partitions.
Cassandra takes a different approach, with a gossip protocol among the nodes
to disseminate the changes. If a request is sent to the wrong node, it's
redirected.

### 7. Transactions

#### The Slippery Concept of a Transaction

Transaction guarantees are often described as **ACID**: Atomicity, Consistency,
Isolation and Durability (although definitions are vague and ACID has become a
marketing term).

**Atomicity**: a transaction is atomic if when an error occurs in the middle
of it, all the writes are discarded or undone and the transaction is aborted.

**Consistency**: if the transaction starts in a valid state (from the
standpoint of the variants of your application) and writes preserve the
validity, the invariants are always satisfied. Consistency actually belongs
to the application, that must take advantage of Atomicity, Isolation and
Durability to be consistent.

**Isolation**: if two transactions occur concurrently, they must run as if
they were run in isolation (serially).

**Durability**: if the transaction ends successfully (written to a
non-volatile store), the data won't be lost. This often implies writing to a
write-ahead log.

##### Single-Object and Multi-Object Operations

In order to change data in several object (rows, documents...) the DB must
support a way to group operations in a transaction.

Single-object operations, such as updating one record or incrementing a counter
(if the database provides that mechanism) are not considered transactions.

#### Weak Isolation Levels

ACID has a performance cost. Knowing the isolation levels can help you choosing
the right tool.

##### Read Committed

When you read you only read committed data. When you write, you only overwrite
committed data.

Dirty writes are often prevented with row locks that transactions must get
before modifying a value.

Dirty reads could be prevented with a lock as well, but that would cause long
transactions to block reads for a long time. Instead, both the old value and
the new one are remembered until the transaction ends. Queries outside the
transaction read the old one, queries inside read the new one.

##### Snapshot Isolation and Repeatable Read

_Nonrepeatable read or read skew_: two consecutive queries return different
values for the same field (because a transaction changed it). That's considered
acceptable under read committed isolation level, but it's not for long running
queries such as the ones under analytical queries or backups.
Snapshot isolation fixes this: each transaction reads from a consistent
snapshot of the DB.

It's usually implemented with MVCC (multi-version concurrency control): each
transaction gets a unique identifier, and when it modifies some data, the new
one is tagged with that id. That way the DB know what data should be seen
by each transaction.

Reads are based in visibility rules: a record is visible if the transaction
that created that object was already committed and the object is not marked
for deletion by a committed transaction. This is performed by remembering
ongoing transactions when another transaction begins.

##### Preventing lost updates

In concurrent read-modify-write cycles, you can lose updates (because the first
transaction doesn't know about the first one).

Mechanisms:
- Atomic write operations: `update counters set val = val + 1 where key = 1`.
- Explicit locking: adding `... for update` in a select grants a write lock.
- Automatic detection and retry.
- Compare and set: add the old value at the `where` of the write query.

##### Conflict resolution and replication

In replicated DBs you can't use locks or compare-and-set, because there are
several copies. In these scenarios, using special data structures that allow
fixing the conflict at application level, or that take advantage of commutative
properties of some operations, are the way to go.
The alternative, last write wins, is prone to lost updates.

##### Write Skew and Phantoms

**Write skew**: two transactions read the same objects, and then update some of
those objects. It's hard to control. You might be able to do it with true
serializable isolation, triggers and materialized views, or explicit locks.

The pattern is doing a select that then, depending on application logic,
performs a write. This, having a write in one transaction changing the result
of a search query in another, is called a phantom. You can't use a lock to
avoid them, because there's no object to attach it to it.

A solution is materializing conflicts: adding rows that allow explicit locking.
It's not a great solution because it's error-prone and leaks a concurrency
control into the data model. Serializable isolation is preferable.

#### Serializability

Serialization provides the strongest guarantees, but with a cost.

##### Actual Serial Execution

Single-threaded systems allow executing transactions actually in serial, and
can perform well. They're nevertheless limited to a CPU core, and should only
be used if all data fits in memory.

###### Encapsulating transactions in stored procedures

To get rid of human interaction and reduce network communication, you can
perform the complete transaction in a safe stored procedure.

##### Two-Phase Locking (2PL)

In 2PL, if a transaction wants to read a object written by another one, it
must wait. And if a transaction wants to write an object read by another, it
must wait as well.

It's implemented with locks in shared mode (for reads) and exclusive mode (for
reads). Exclusive lock imply waiting.

The main problem is performance. Partly by management overhead but mostly
because the loss of concurrency. They're _really_ slow at high percentiles.

For phantom prevention a DB must implement predicate locks, which are locks
over objects that match some search condition (even if they don't exist yet!).

Predicate locks are expensive to check, so index-range locks are a good
compromise. Instead of locking the precise ranges, they leverage existing
indexes to create the locks on them. They're broader, but much faster.

##### Serializable Snapshot Isolation (SSI)

SSI is optimistic in the sense that it allows transaction to operate as if
nothing wrong happened, and performs the checks at commit. If everything was
OK (if the transactions actually run in serial) it allows committing, otherwise
transaction is aborted.

This performs good if contention (access to the same resource) is low and there
are spare resources.

##### Decisions based on an outdated premise

If the transaction makes decisions based upon a read in the beginning, on
commit the premise must be checked again.

**Detecting stale MVCC reads** (uncommitted writes before the read).

**Detecting writes that affect prior reads** (write after the read).


### 8. The Trouble with Distributed Systems

#### Faults and Partial Failures

In single-machine there's no _essential_ reason for things to go wrong. As a
single computer is deterministic, the problems are always faulty software.
In distributed systems that's no longer true, because you must face real-world
issues, making the system non-deterministic and prone to partial failures.

#### Unreliable Networks

There's a myriad of ways a network can fail, and some components need to detect
failures to react (for example, a load balancer). Timeouts are the only
reliable mechanism for detecting a fault.

#### Unreliable Clocks

Monotonic clocks are clocks that always move ahead, valid for measuring
duration.

Spanner uses time of the day for generating transaction ids for snapshot
isolation across datacenters. That's normally hard to achieve, but in their
case it's based on a TrueTime API that returns the earliest possible time
and the latest possible time. If given two pairs they don't overlap, they have
confidence that one transaction happened before another. That helps avoiding
causality issues. In order to finish a commit it must wait until all possible
transactions that might've read values also end. So, to reduce wait time it
must improve clock synchronization.

#### Knowledge, Truth and Lies

One node own knowledge can't be trusted because of individual issues (GC...),
and because node faults can't be reliably distinguished from network faults,
so "the truth" is determined by a absolute majority of more than half the
nodes.

Fencing tokens (that can be implemented returning a monotonic token when the
lock is granted) fix the problem of granting a lock to a unresponsive client.

Byzantine faults are problems caused by nodes that "lie", like sending
corrupted messages or claiming something that is not the truth (from a
protocol standpoint). This book assume that these kind of problems don't appear
because nodes are under your control and hardware failures are assisted at
hardware level. Byzantine faults often need to be addressed by consensus of
super majorities (two thirds).

As algorithms need to be defined independently from the hardware, we need to
define which assumptions they can work with. This is done with system models:

Models in regard of timing assumptions:
- Synchronous model: bounded timing. Unrealistic.
- Partially synchronous model: like synchronous, _most of the time_. Realistic.
- Asynchronous model.

Models in regard of node failures:
- Crash-stop faults: the only failure is stop responding and never coming back.
- Crash-recovery faults: after a crash a node can start responding again.
- Byzantine faults: nodes can do anything.

An algorithm is considered *correct* in a system model if it always satisfies
its properties in all situations that can happen in that system model.

There are two kind of properties:
- If a *safety property* is violated we can point a particular point in time at
which it was broken, and the violation can't be undone.
- If a *liveness property* is violated we can't point a particular point of
time for the violation, but there's hope that it may be satisfied in the
future.

### 9. Consistency and consensus

A good approach for building fault-tolerant systems is creating general-purpose
abstractions with useful guarantees, and then let software rely on those
guarantees.

#### Linearizability

Linearizability is making a system look like there's only one copy of the data
and all operations are atomic.

It's not the same than serializability, which is an isolation property of
transactions. Linearizability is all about the recency of individual records.

##### Relying in linearizability

Important in many applications:
- Locks and leader election: in distributed locks, the underlying storage must
be linearizable. Consensus algorithms rely on this.
- Constraints and uniqueness.
- Cross-channel timing dependencies.

#### Ordering Guarantees

##### Ordering and Causalty

Causal order is not a total order. Total order allows any two elements to be
compared. Linearizability has total order, and implies causality.

Causality can be preserved without linearizability, and it's the strongest
possible consistency model that doesn't slow down because of network delays,
and remains available in face of network failures.

In order to determine causality, the dependencies must be tracked, for example
with version vectors. Nevertheless, storing all that is impractical and can
be replaced by sequence number ordering. If we can just add a monotonic integer
that's enough to be consistent with causality. A replication log is also valid.

Without a single leader, generating sequence numbers is not easy, and
approximations are often used, such as timestamps or pre-allocated ranges,
which are inconsistent with causality.

There's other simple method which is consistent: *Lamport timestamps*.
Essentially, it's keeping track of the number of operations performed to keep
track of the causality. Each node has an identifier and a counter. The
identifier provides uniqueness. Each operation goes with the counter, and
if any node or client receives a bigger counter than the one stored, it's
updated.

Lamport timestamps can provide causally dependency at a lower storage cost than
version vectors, but version vectors can detect concurrent operations.

Total ordering is not enough for guarantees such as uniqueness, because the
order is only valid afterwards, not for taking decisions in the moment.

##### Total Order Broadcast

Total order broadcast is a protocol aimed to provide total order guarantees in
distributed environments, without leaders. It requires reliable delivery and
total ordered delivery.

#### Distributed Transactions and Consensus

Consensus is a hard topic required for many needs such as leader election and
atomic commits. In the asynchronous model, it's impossible to solve, but if
nodes can use timeouts the problem becomes solvable.

##### Atomic Commit and Two-Phase Commit (2PC)

In 2PC, applications write on several nodes as usual. For the commit, a
`prepare` signal is first sent by the coordinator, and once both answers are
received, the final `commit` is sent. A global transaction id is shared in all
requests. The `prepare` signal is only returned by the nodes if they can
guarantee that the commit can take place, so nodes actually perform all needed
tasks, but without committing. When the coordinator receives all OKs from the
`prepare`, it's written to disk into a log and sent to the nodes.

It's a blocking protocol because nodes might have to wait for a coordinator
recovery after the `prepare`. In theory it could be done non-blocking, but it's
not practical.

##### Fault-Tolerant Consensus

Properties:
- Uniform agreement: all nodes decide the same.
- Integrity: no node decides twice.
- Validity: the decision was proposed by a node. Rules out trivial solutions.
- Termination: nodes that don't crash decide. Formalizes fault-tolerance.
Subject to the assumption that at least half of the nodes don't crash.

Most algorithms decide upon a sequence of numbers, so they become total order
broadcast algorithms.

In single-leader replication that is not manually solved, on failover a new
leader must be chosen, which is closer to consensus.

Consensus algorithm only guarantee uniqueness of leader within the same epoch,
which roughly is a monotonic number that is increased when a new leader is
chosen. When a decision must be taken, the leader first asks whether it's still
the leader, and then the voting takes place. At least one of the nodes that
confirmed the leadership must take place in the voting.

The biggest differences between fault-tolerant consensus algorithms and 2PC is
that in 2PC the leader is always the same and it must wait for all followers.

Costs:
- The agreement before the voting is close to synchronous communication.
- It requires strict majority, so at least 3 nodes are needed to tolerate 1
fault, and 5 for 2. And network partitions cause blocking of the smaller one.
- Most consensus algorithms assume a fixed number of nodes.
- As they rely on timeouts to detect faulty nodes, performance is harmed on
geographically distant nodes.

##### Membership and Coordination Services

ZooKeeper is seen as a distributed key-value store that implements
fault-tolerant consensus and has other properties:
- Linearizable atomic operations
- Total ordering of operations
- Failure detection
- Change notifications

## Part III: Derived Data

### 10. Batch Processing

#### Batch Processing with Unix Tools

UNIX design philosophy (small tools, composability...) created a really
powerful tooling for batch processing.

#### MapReduce and Distributed Filesystems

MapReduce processes are analogous to UNIX tools, but instead of operating on
stdin and stdout they use distributed filesystems such as HDFS.

HDFS is based on shared-nothing principle. Consists of a daemon in every node,
and a central NameNode that keeps information of which file blocks are stored
on which machine. Conceptually it's one big filesystem over a machine network.

**MapReduce**:
1.- Split files in records.
2.- Call the mapper function, extract a key from each record, leave the value
empty.
3.- Sort all key-value pairs by key.
4.- Call the reducer function to iterate over sorted key-value pairs.

To create a MapReduce job you need to implement the mapper and the reducer
functions:
- Mapper: called once per record, generates any number of key-value pairs.
- Reducer: receives an iterator over a collection of values with the same key.

The advantage over UNIX tools is that it can be parallelized.

The scheduler tries to run the mapper where there's a replica of the input
file, _putting the computation near the data_.

As the number of problems that you can solve with a single MapReduce job is
limited, you usually work with worflows, chained jobs, which materialized
state among them.

There's no support for "traditional joins", so specific algorithms must be
developed. For example, sort-merge joins, where the mapper outputs different
data for a given key (for example, user birth date followed by activity) and
then the reducer processes the information by user.

If some keys have much more data than others (such as celebrities in a social
network), called hot spots, reducers job might have to be split among several
nodes.

Hadoop encourages storing a lot of raw data to be processed later.

#### Beyond MapReduce

##### Materialization of Intermediate State

MapReduce jobs need to finish before the next can start, and that often makes
mappers redundant (as they just read the output of the previous jobs). Also
forces to write a lot of temporary data.

To overcome that, some dataflow engines such as Spark, Tez and Flink appeared,
handling the workflow as a single job. There's no role (map/reduce) separation,
just "operators".

##### Graphs and Iterative Processing

Graphs require iterative processing, which can't be expressed in MapReduce
terms, because it only performs a single pass over the data. To overcome this,
the Pregel model, in each iteration a function is called for each vertex, and
the node remembers its state (similar to an actor model, but with
fault-tolerance).

### 11. Stream Processing

#### Transmitting Event Streams

Messaging systems:
- TCP/UNIX pipe: just allow 1-1.
- Publish/subscribe. Different systems:
  - What if publish rate is faster than consumption? Drop, buffer or
    backpressure.
  - What if node crashes? Is message loss acceptable?

##### Direct messaging from producers to consumers

UDP multicast, ZeroMQ, direct RPC/HTTP calls... They require application level
code to be aware of possible data loss.

##### Message brokers

Intermediate server similar to a database but optimized
for streaming processing. Makes consumers asynchronous. Often provides
unbounded queuing. Differences from databases:
- Messages are usually deleted after delivery.
- Assume small working sets.
- They support subscribing to topics, which is related to secondary indexes.
- Do not support querying.

**Multiple consumers**: load balancing vs. fan-out.

**Acknowledges and redelivery**: customers must send a confirmation when they
process the message. This can cause message reordering if load-balancing is
used.

##### Partitioned Logs

They combine DB durability with message brokers low-latency notifications.

Logs vs. traditional messaging:
- Log supports fan-out trivially: readers can consume the log without
  interfering.
- To achieve load balancing, the broker can assign partitions to node groups.
  Tradeoffs:
  - The number of nodes sharing the work of consuming a topic can be at most
    the number of log partitions, because messages within the same partition
    are delivered to the same node. Using a thread pool makes offset management
    complicated.
  - If a single message is slow to process, it holds up the processing of
    subsequent messages.

Conclusion: if message processing is slow and you need to parallelize
processing on a message-by-message basis, and ordering is not important, a
traditional message broker is preferable. If ordering is important and
processing is fast, logs-based works well.

##### Databases and Streams

Reading the "internal" DB replication log can be seen as a way to detect data
changes and react to them (such as writing to a search index).

Event Sourcing and CQRS are techniques closely related to that. For example,
we can use the immutable log for the events and derive read-only views.

It also affects concurrency control. On one hand, it has the problem that
consumers are asynchronous, so providing read-your-own-writes guarantee might
become harder. On the other, it allows addressing concurrency differently:
you could store an atomic event that completely resembles the user action.

##### Processing Streams

Uses of stream processing:
- Complex event processing
- Stream analytics
- Maintaining materialized views
- Search on streams
- Message passing and RPC

Reasoning about time is often based on windows of time. Types:
- Tumbling window: fixed length, consecutive
- Hopping window: fixed length, with overlapping
- Sliding window: all the events that occur within an interval ("last 5 min")
- Session window: all the events that occur closely, ending on inactivity

##### Stream joins

- Stream-stream joins: the processor maintains state with events indexed by
  the join field. This way it can react and emit a new join event on match.
  This can include a session window.
- Stream-table ("stream enrichment"): a copy of the DB is kept (in sync) at the
  processor. On join, an event is emitted.
- Table-table join

### 12. The Future of Data Systems

#### Data integration

##### Combining Specialized Tools by Deriving Data

Reasoning about data flows is hard, and you probably need to derive data.
Using total order (based on event sourcing logs or change data capture) is a
way to handle that easily. It's also a way to avoid distributed transactions
for data integration in some cases.

#### Unbundling Databases

Thinking about what a database provides (secondary indexes, materialized views,
replication logs...) and how a index is created (similar to setting up a new
follower replica), you can begin seeing the dataflow across an entire
organization as a big database. Assuming that there's no single storage
system that can fit all needs, there might be two approaches for providing a
cohesive system:
- Federated databases, unifying reads: provide a unified query interface to
a variety of storage engines. FDWs are an example of this.
- Unbundled databases, unifying writes: make it easier to reliably plug
together storage systems (with CDC or event logs).

**Designing Applications Around Dataflow**: application code might be seen as a
derivation function, listening to changes
of the data, creating derived datasets as needed and removing the need for
querying outside the local machine (as the local data would always be updated).

The derived dataset can be seen as the meeting point between the read and write
paths, and shifting it is interesting. Depending on the needs, the two paths
might do more work.

Taking this dataflow idea to the limit, and given the improved features of web
clients, maybe we should consider designing all applications like messaging
applications are, pushing changes to the clients all the time.

#### Aiming for Correctness

Even if the application uses a data store with strong guarantees, or even
immutability, it's not free of errors. Generally speaking, end-to-end safety
measurements are needed in order to guarantee correctness.

The so called "eventual consistency" conflates two topics: timeliness and
integrity. While integrity is a must, timeliness and other constraints can
often be loosely interpreted as part of the normal business rules.

Either to protect us from hardware failures or from software bugs, we should
consider designing for auditability.

#### Doing the Right Thing

This section must be read, can't be summarized.

</div>
</details>
