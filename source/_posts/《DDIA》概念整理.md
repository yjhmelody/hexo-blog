---
title: 《DDIA》概念整理
date: 2018-08-30 20:00:00
tags: [Distrubuted-System, Book]
category: [CS]
---

最近指忙里抽闲的看《Designing Data-Intensive Applications》（《设计数据密集型应用》）这本书，主要书看中文为主。这本书非常好，我现在刚看完一半。这本书让你认识到了数据库系统和分布式系统的大概貌，没有深入繁琐的具体细节也没有让人浅尝辄止。这本书网络上评价很高，甚至豆瓣评分有2个评分，一个是9.5分，一个是10分。现在即使想作为一个普通的后端工程师也需要学习分布式知识了，强烈推荐这本书。

我发现书后面的术语表非常精良，个人认为：虽然可能还涵盖不到一半的核心概念，但是如果能深刻的理解这术语表部分的，那对数据库系统和分布式系统就有了比较好的整体认识了。

顺便，光看这本书就需要接触过数据结构，操作系统，计算机网络，数据库原理，还有少部分组成原理的知识，不然只能知其然不知其所以然。限于自己的个人水平和这方面不多的实践，有些部分还没有理解到位。个人认为，学习分布式可能还需要学习一点并行编程和网络编程，分布式系统可能是综合性比较强的方向了。并且根据具体要做的分布式系统，可能还需要机器学习，搜索引擎等知识。

这本书配合着学习 [MIT6.824](https://pdos.csail.mit.edu/6.824/schedule.html) 可能会非常好吧。

以下整理出了英文和中文的术语表。

<!-- more -->

## 英文

### asynchronous
Not waiting for something to complete (e.g., sending data over the network to another node), and not making any assumptions about how long it is going to take. See `Synchronous Versus Asynchronous Replication` on page 153, `Synchronous Versus Asynchronous Networks` on page 284, and `System Model and Reality` on page 306.

### atomic
1. In the context of concurrent operations:describing an operation that appears to take effect at a single point in time, so
another concurrent process can never encounter the operation in a `halffinished` state. See also isolation.
2. In the context of transactions: grouping together a set of writes that must either all be committed or all be rolled back, even if faults occur. See `Atomicity` on page 223 and `Atomic Commit and Two-Phase Commit (2PC)` on page 354.

### backpressure
Forcing the sender of some data to slow down because the recipient cannot keep up with it. Also known as flow control. See
`Messaging Systems` on page 441.

### batch process
A computation that takes some fixed (and usually large) set of data as input and produces some other data as output, without modifying the input. See Chapter 10.

### bounded
Having some known upper limit or size. Used for example in the context of network delay (see `Timeouts and Unbounded Delays` on page 281) and datasets (see the introduction to Chapter 11).

### Byzantine fault
A node that behaves incorrectly in some arbitrary way, for example by sending contradictory or malicious messages to other nodes. See `Byzantine Faults` on page 304.

### cache
A component that remembers recently used data in order to speed up future reads of the same data. It is generally not complete: thus, if some data is missing from the cache, it has to be fetched from some underlying, slower data storage system that has a complete copy of the data.

### CAP theorem
A widely misunderstood theoretical result that is not useful in practice. See `The CAP theorem` on page 336.

### causality
The dependency between events that arises when one thing `happens before` another thing in a system. For example, a later event that is in response to an earlier event, or builds upon an earlier event, or should be understood in the light of an earlier event. See `The `happens-before` relationship and concurrency` on page 186 and `Ordering and Causality` on page 339.

### consensus
A fundamental problem in distributed computing, concerning getting several nodes to agree on something (for example, which node should be the leader for a database cluster). The problem is much harder than it seems at first glance. See `Fault-Tolerant Consensus` on page 364.

### data warehouse
A database in which data from several different OLTP systems has been combined and prepared to be used for analytics purposes. See `Data Warehousing` on page 91.

### declarative
Describing the properties that something should have, but not the exact steps for how to achieve it. In the context of queries, a query optimizer takes a declarative query and decides how it should best be executed. See `Query Languages for Data` on page 42.

### denormalize
To introduce some amount of redundancy or duplication in a normalized dataset, typically in the form of a cache or index, in order to speed up reads. A denormalized value is a kind of precomputed query result, similar to a materialized view. See `Single-Object and MultiObject Operations` on page 228 and `Deriving several views from the same event log` on page 461.

### derived data
A dataset that is created from some other data through a repeatable process, which you could run again if necessary. Usually, derived data is needed to speed up a particular kind of read access to the data. Indexes, caches, and materialized views
are examples of derived data. See the introduction to Part III.

### deterministic
Describing a function that always produces the same output if you give it the same input. This means it cannot depend on random numbers, the time of day, network communication, or other unpredictable things.

### distributed
Running on several nodes connected by a network. Characterized by partial failures: some part of the system may be broken while other parts are still working, and it is often impossible for the software to know what exactly is broken. See `Faults and Partial Failures` on page 274.

### durable
Storing data in a way such that you believe it will not be lost, even if various faults occur. See `Durability` on page 226.

### ETL
Extract–Transform–Load. The process of extracting data from a source database, transforming it into a form that is more suitable for analytic queries, and loading it into a data warehouse or batch processing system. See `Data Warehousing` on page 91.

### failover
In systems that have a single leader, failover is the process of moving the leadership role from one node to another. See `Handling Node Outages` on page 156.

### fault-tolerant
Able to recover automatically if something goes wrong (e.g., if a machine crashes or a network link fails). See `Reliability` on page 6.

### flow control
See backpressure.

### follower
A replica that does not directly accept any writes from clients, but only processes data changes that it receives from a leader. Also known as a secondary, slave, read replica, or hot standby. See `Leaders and Followers` on page 152. 

### full-text search 
Searching text by arbitrary keywords, often with additional features such as matching similarly spelled words or synonyms. A full-text index is a kind of secondary index that supports such queries. See `Full-text search and fuzzy indexes` on page 88.

### graph
A data structure consisting of vertices (things that you can refer to, also known as nodes or entities) and edges (connections from one vertex to another, also known as relationships or arcs). See `Graph-Like Data Models` on page 49.

### hash
A function that turns an input into a random-looking number. The same input always returns the same number as output. Two different inputs are very likely to have two different numbers as output, although it is possible that two different inputs produce the same output (this is called a collision). See `Partitioning by Hash of Key` on page 203.

### idempotent
Describing an operation that can be safely retried; if it is executed more than once, it has the same effect as if it was only executed once. See `Idempotence` on page 478.

### index
A data structure that lets you efficiently search for all records that have a particular value in a particular field. See `Data
Structures That Power Your Database` on page 70.

### isolation
In the context of transactions, describing the degree to which concurrently executing transactions can interfere with each other. Serializable isolation provides the strongest guarantees, but weaker isolation levels are also used. See `Isolation` on page 225.

### join
To bring together records that have something in common. Most commonly used in the case where one record has a reference to another (a foreign key, a document reference, an edge in a graph) and a query needs to get the record that the reference points to. See `Many-to-One and Many-to-Many Relationships` on page 33 and `Reduce-Side Joins and Grouping` on page 403.

### leader
When data or a service is replicated across several nodes, the leader is the designated replica that is allowed to make changes. A leader may be elected through some protocol, or manually chosen by an administrator. Also known as the primary or master. See `Leaders and Followers` on page 152.

### linearizable
Behaving as if there was only a single copy of data in the system, which is updated by atomic operations. See `Linearizability` on page 324.

### locality
A performance optimization: putting several pieces of data in the same place if they are frequently needed at the same time. See `Data locality for queries` on page 41.

### lock
A mechanism to ensure that only one thread, node, or transaction can access something, and anyone else who wants to access the same thing must wait until the lock is released. See `Two-Phase Locking (2PL)` on page 257 and `The leader and the lock` on page 301.

### log
An append-only file for storing data. A write-ahead log is used to make a storage engine resilient against crashes (see `Making B-trees reliable` on page 82), a log-structured storage engine uses logs as its primary storage format (see `SSTables and LSM-Trees` on page 76), a replication log is used to copy writes from a leader to followers (see `Leaders and Followers` on
page 152), and an event log can represent a data stream (see `Partitioned Logs` on page 446).

### materialize
To perform a computation eagerly and write out its result, as opposed to calculating it on demand when requested. See `Aggregation: Data Cubes and Materialized Views` on page 101 and `Materialization of Intermediate State` on page 419.

### node
An instance of some software running on a computer, which communicates with other nodes via a network in order to accomplish some task.

### normalized
Structured in such a way that there is no redundancy or duplication. In a normalized database, when some piece of data changes, you only need to change it in one place, not many copies in many different places. See `Many-to-One and Many-to-Many Relationships` on page 33.

### OLAP
Online analytic processing. Access pattern characterized by aggregating (e.g., count, sum, average) over a large number of records. See `Transaction Processing or Analytics?` on page 90.

### OLTP
Online transaction processing. Access pattern characterized by fast queries that read or write a small number of records, usually indexed by key. See `Transaction Processing or Analytics?` on page 90.

### partitioning
Splitting up a large dataset or computation that is too big for a single machine into smaller parts and spreading them across several machines. Also known as sharding. See Chapter 6.

### percentile
A way of measuring the distribution of values by counting how many values are above or below some threshold. For example, the 95th percentile response time during some period is the time t such that 95% of requests in that period complete in less than t, and 5% take longer than t. See `Describing Performance` on page 13.

### primary key
A value (typically a number or a string) that uniquely identifies a record. In many applications, primary keys are generated by the system when a record is created (e.g., sequentially or randomly); they are not usually set by users. See also secondary index.

### quorum
The minimum number of nodes that need to vote on an operation before it can be considered successful. See `Quorums for reading and writing` on page 179.

### rebalance
To move data or services from one node to another in order to spread the load fairly. See `Rebalancing Partitions` on page 209.

### replication
Keeping a copy of the same data on several nodes (replicas) so that it remains accessible if a node becomes unreachable. See Chapter 5.

### schema
A description of the structure of some data, including its fields and datatypes. Whether some data conforms to a schema can be checked at various points in the data’s lifetime (see `Schema flexibility in the document model` on page 39), and a schema can change over time (see Chapter 4).

### secondary index
An additional data structure that is maintained alongside the primary data storage and which allows you to efficiently search for records that match a certain kind of condition. See `Other Indexing Structures` on page 85 and `Partitioning and Secondary Indexes` on page 206.

### serializable
A guarantee that if several transactions execute concurrently, they behave the same as if they had executed one at a time, in some serial order. See `Serializability` on page 251.

### shared-nothing
An architecture in which independent nodes—each with their own CPUs, memory, and disks are connected via a conventional network, in contrast to sharedmemory or shared-disk architectures. See the introduction to Part II.

### skew
1. Imbalanced load across partitions, such that some partitions have lots of requests or data, and others have much less. Also known as hot spots. See `Skewed Workloads and Relieving Hot Spots` on page 205 and `Handling skew` on page 407.
2. A timing anomaly that causes events to appear in an unexpected, nonsequential order. See the discussions of read skew in `Snapshot Isolation and Repeatable Read` on page 237, write skew in `Write Skew and Phantoms` on page 246, and clock skew in `Timestamps for ordering events` on page 291.

### split brain
A scenario in which two nodes simultaneously believe themselves to be the leader, and which may cause system guarantees to be violated. See `Handling Node Outages` on page 156 and `The Truth Is Defined by the Majority` on page 300.

### stored procedure
A way of encoding the logic of a transaction such that it can be entirely executed on a database server, without communicating back and forth with a client during the transaction. See `Actual Serial Execution` on page 252.

### stream process
A continually running computation that consumes a never-ending stream of events as input, and derives some output from it. See Chapter 11.

### synchronous
The opposite of asynchronous.

### system of record
A system that holds the primary, authoritative version of some data, also known as the source of truth. Changes are first written here, and other datasets may be derived from the system of record. See the introduction to Part III.

### timeout
One of the simplest ways of detecting a fault, namely by observing the lack of a response within some amount of time. However, it is impossible to know whether a timeout is due to a problem with the remote node, or an issue in the network. See `Timeouts and Unbounded Delays` on page 281.

### total order
A way of comparing things (e.g., timestamps) that allows you to always say which one of two things is greater and which one is lesser. An ordering in which some things are incomparable (you cannot say which is greater or smaller) is called a partial order. See `The causal order is not a total order` on page 341.

### transaction
Grouping together several reads and writes into a logical unit, in order to simplify error handling and concurrency issues. See Chapter 7.

### two-phase commit (2PC)
An algorithm to ensure that several database nodes either all commit or all abort a transaction. See `Atomic Commit and Two-Phase Commit (2PC)` on page 354.

### two-phase locking (2PL)
An algorithm for achieving serializable isolation that works by a transaction acquiring a lock on all data it reads or writes, and holding the lock until the end of the transaction. See `Two-Phase Locking (2PL)` on page 257.

### unbounded
Not having any known upper limit or size. The opposite of bounded.

## 中文

### 异步（asynchronous）
不等待某些事情完成（例如，将数据发送到网络中的另一个节点），并且不会假设要花多长时间。请参阅第153页上的`同步与异步复制`，第284页上的`同步与异步网络`，以及第306页上的`系统模型与现实`。

### 原子（atomic）
1. 在并发操作的上下文中：描述一个在单个时间点看起来生效的操作，所以另一个并发进程永远不会遇到处于`半完成`状态的操作。另见隔离。
2. 在事务的上下文中：将一些写入操作分为一组，这组写入要么全部提交成功，要么遇到错误时全部回滚。参见第223页的`原子性`和第354页的`原子提交和两阶段提交（2PC）`。

### 背压（backpressure）
接收方接收数据速度较慢时，强制降低发送方的数据发送速度。也称为流量控制。请参阅第441页上的`消息系统`。

### 批处理（batch process）
一种计算，它将一些固定的（通常是大的）数据集作为输入，并将其他一些数据作为输出，而不修改输入。见第十章。

### 边界（bounded）
有一些已知的上限或大小。例如，网络延迟情况（请参阅`超时和未定义的延迟`在本页281）和数据集（请参阅第11章的介绍）。

### 拜占庭故障（Byzantine fault）
表现异常的节点，这种异常可能以任意方式出现，例如向其他节点发送矛盾或恶意消息。请参阅第304页上的`拜占庭故障`。

### 缓存（cache）

一种组件，通过存储最近使用过的数据，加快未来对相同数据的读取速度。缓存中通常存放部分数据：因此，如果缓存中缺少某些数据，则必须从某些底层较慢的数据存储系统中，获取完整的数据副本。

### CAP定理（CAP theorem）
一个被广泛误解的理论结果，在实践中是没有用的。参见第336页的`CAP定理`。

### 因果关系（causality）
事件之间的依赖关系，当一件事发生在另一件事情之前。例如，后面的事件是对早期事件的回应，或者依赖于更早的事件，或者应该根据先前的事件来理解。请参阅第186页上的`发生之前的关系和并发性`和第339页上的`排序和因果关系`。

### 共识（consensus）
分布式计算的一个基本问题，就是让几个节点同意某些事情（例如，哪个节点应该是数据库集群的领导者）。问题比乍看起来要困难得多。请参阅第364页上的`容错共识`。

### 数据仓库（data warehouse）
一个数据库，其中来自几个不同的OLTP系统的数据已经被合并和准备用于分析目的。请参阅第91页上的`数据仓库`。

### 声明式（declarative）
描述某些东西应有的属性，但不知道如何实现它的确切步骤。在查询的上下文中，查询优化器采用声明性查询并决定如何最好地执行它。请参阅第42页上的`数据的查询语言`。

### 非规范化（denormalize）
为了加速读取，在标准数据集中引入一些冗余或重复数据，通常采用缓存或索引的形式。非规范化的值是一种预先计算的查询结果，像物化视图。请参见`单对象和多对象操作`（第228页）和`从同一事件日志中派生多个视图`（第461页）。

### 派生数据（derived data）
一种数据集，根据其他数据通过可重复运行的流程创建。必要时，你可以运行该流程再次创建派生数据。派生数据通常用于提高特定数据的读取速度。常见的派生数据有索引、缓存和物化视图。参见第三部分的介绍。

### 确定性（deterministic）
描述一个函数，如果给它相同的输入，则总是产生相同的输出。这意味着它不能依赖于随机数字、时间、网络通信或其他不可预测的事情。

### 分布式（distributed）
在由网络连接的多个节点上运行。对于部分节点故障，具有容错性：系统的一部分发生故障时，其他部分仍可以正常工作，通常情况下，软件无需了解故障相关的确切情况。请参阅第274页上的`故障和部分故障`。

### 持久（durable）
以某种方式存储数据，即使发生各种故障，也不会丢失数据。请参阅第226页上的`持久性`。

### ETL（Extract-Transform-Load）
提取-转换-加载（Extract-Transform-Load）。从源数据库中提取数据，将其转换为更适合分析查询的形式，并将其加载到数据仓库或批处理系统中的过程。请参阅第91页上的`数据仓库`。

### 故障转移（failover）
在具有单一领导者的系统中，故障转移是将领导角色从一个节点转移到另一个节点的过程。请参阅第156页的`处理节点故障`。

### 容错（fault-tolerant）
如果出现问题（例如，机器崩溃或网络连接失败），可以自动恢复。请参阅第6页上的`可靠性`。

### 流量控制（flow control）
见背压（backpressure）。

### 追随者（follower）
一种数据副本，仅处理领导者发出的数据变更，不直接接受来自客户端的任何写入。也称为辅助、仆从、只读副本或热备份。请参阅第152页上的`领导和追随者`。

### 全文检索（full-text search）
通过任意关键字来搜索文本，通常具有附加特征，例如匹配类似的拼写词或同义词。全文索引是一种支持这种查询的次级索引。请参阅第88页上的`全文搜索和模糊索引`。

### 图（graph）
一种数据结构，由顶点（可以指向的东西，也称为节点或实体）和边（从一个顶点到另一个顶点的连接，也称为关系或弧）组成。请参阅第49页上的`和图相似的数据模型`。 

### 散列（hash）
将输入转换为看起来像随机数值的函数。相同的输入会转换为相同的数值，不同的输入一般会转换为不同的数值，也可能转换为相同数值（也被称为冲突）。请参阅第203页的`根据键的散列值分隔`。

### 幂等（idempotent）
用于描述一种操作可以安全地重试执行，即执行多次的效果和执行一次的效果相同。请参阅第478页的`幂等`。

### 索引（index）
一种数据结构。通过索引，你可以根据特定字段的值，在所有数据记录中进行高效检索。请参阅第70页的`让数据库更强大的数据结构`。

### 隔离性（isolation）
在事务上下文中，用于描述并发执行事务的互相干扰程度。串行运行具有最强的隔离性，不过其它程度的隔离也通常被使用。请参阅第225页的`隔离`。

### 连接（join）
汇集有共同点的记录。在一个记录与另一个记录有关（外键，文档参考，图中的边）的情况下最常用，查询需要获取参考所指向的记录。请参阅第33页上的`多对一和多对多关系`和第393页上的`减少端连接和分组`。

### 领导者（leader）
当数据或服务被复制到多个节点时，由领导者分发已授权变更的数据副本。领导者可以通过某些协议选举产生，也可以由管理者手动选择。也被称为主人。请参阅第152页的`领导者和追随者`。

### 线性化（linearizable）
表现为系统中只有一份通过原子操作更新的数据副本。请参阅第324页的`线性化`。

### 局部性（locality）
一种性能优化方式，如果经常在相同的时间请求一些离散数据，把这些数据放到一个位置。请参阅第41页的`请求数据的局部性`。

### 锁（lock）
一种保证只有一个线程、节点或事务可以访问的机制，如果其它线程、节点或事务想访问相同元素，则必须等待锁被释放。请参阅第257页的`两段锁（2PL）`和301页的`领导者和锁`。

### 日志（log）
日志是一个只能以追加方式写入的文件，用于存放数据。预写式日志用于在存储引擎崩溃时恢复数据（请参阅第82页的`使二叉树更稳定`）；结构化日志存储引擎使用日志作为它的主要存储格式（请参阅第76页的`有序字符串表和日志结构的合并树`）；复制型日志用于把写入从领导者复制到追随者（请参阅第152页的`领导者和追随者`）；事件性日志可以表现为数据流（请参阅第446页的`分段日志`）。

### 物化（materialize）
急切地计算并写出结果，而不是在请求时计算。请参阅第101页的`聚合：数据立方和物化视图`和419页的`中间状态的物化`。

### 节点（node）
计算机上运行的一些软件的实例，通过网络与其他节点通信以完成某项任务。

### 规范化（normalized）
以没有冗余或重复的方式进行结构化。 在规范化数据库中，当某些数据发生变化时，您只需要在一个地方进行更改，而不是在许多不同的地方复制很多次。 请参阅第33页上的`多对一和多对多关系`。

### OLAP（Online Analytic Processing）
在线分析处理。 通过对大量记录进行聚合（例如，计数，总和，平均）来表征的访问模式。 请参阅第90页上的`交易处理或分析？`。

### OLTP（Online Transaction Processing）
在线事务处理。 访问模式的特点是快速查询，读取或写入少量记录，这些记录通常通过键索引。 请参阅第90页上的`交易处理或分析？`。

### 分区（partitioning）
将单机上的大型数据集或计算结果拆分为较小部分，并将其分布到多台机器上。 也称为分片。 见第6章。

### 百分位点（percentile）
通过计算有多少值高于或低于某个阈值来衡量值分布的方法。 例如，某个时间段的第95个百分位响应时间是时间t，则该时间段中，95％的请求完成时间小于t，5％的请求完成时间要比t长。 请参阅第13页上的`描述性能`。

### 主键（primary key）
唯一标识记录的值（通常是数字或字符串）。 在许多应用程序中，主键由系统在创建记录时生成（例如，按顺序或随机）; 它们通常不由用户设置。 另请参阅二级索引。

### 法定人数（quorum）
在操作完成之前，需要对操作进行投票的最少节点数量。 请参阅第179页上的`读和写的法定人数`。

### 再平衡（rebalance）
将数据或服务从一个节点移动到另一个节点以实现负载均衡。 请参阅第209页上的`再平衡分区`。

### 复制（replication）
在几个节点（副本）上保留相同数据的副本，以便在某些节点无法访问时，数据仍可访问。请参阅第5章。

### 模式（schema）
一些数据结构的描述，包括其字段和数据类型。 可以在数据生命周期的不同点检查某些数据是否符合模式（请参阅第39页上的`文档模型中的模式灵活性`），模式可以随时间变化（请参阅第4章）。

### 次级索引（secondary index）
与主要数据存储器一起维护的附加数据结构，使您可以高效地搜索与某种条件相匹配的记录。 请参阅第85页上的`其他索引结构`和第206页上的`分区和二级索引`。

### 可序列化（serializable）
保证多个并发事务同时执行时，它们的行为与按顺序逐个执行事务相同。 请参阅第251页上的`可序列化`。

### 无共享（shared-nothing）
与共享内存或共享磁盘架构相比，独立节点（每个节点都有自己的CPU，内存和磁盘）通过传统网络连接。 见第二部分的介绍。

### 偏斜（skew）
1. 各分区负载不平衡，例如某些分区有大量请求或数据，而其他分区则少得多。也被称为热点。请参阅第205页上的`工作负载偏斜和减轻热点`和第407页上的`处理偏斜`。
2. 时间线异常导致事件以不期望的顺序出现。 请参阅第237页上的`快照隔离和可重复读取`中的关于读取偏斜的讨论，第246页上的`写入偏斜和模糊`中的写入偏斜以及第291页上的`订购事件的时间戳`中的时钟偏斜。

### 脑裂（split brain）
两个节点同时认为自己是领导者的情况，这种情况可能违反系统担保。 请参阅第156页的`处理节点中断`和第300页的`真相由多数定义`。

### 存储过程（stored procdure）
一种对事务逻辑进行编码的方式，它可以完全在数据库服务器上执行，事务执行期间无需与客户端通信。 请参阅第252页的`实际串行执行`。

### 流处理（stream process）
持续运行的计算。可以持续接收事件流作为输入，并得出一些输出。 见第11章。

### 同步（synchronous）
异步的反义词。

### 记录系统（system of record）
一个保存主要权威版本数据的系统，也被称为真相的来源。首先在这里写入数据变更，其他数据集可以从记录系统派生。 参见第三部分的介绍。

### 超时（timeout）
检测故障的最简单方法之一，即在一段时间内观察是否缺乏响应。 但是，不可能知道超时是由于远程节点的问题还是网络中的问题造成的。 请参阅第281页上的`超时和无限延迟`。

### 全序（total order）
一种比较事物的方法（例如时间戳），可以让您总是说出两件事中哪一件更大，哪件更小。 总的来说，有些东西是无法比拟的（不能说哪个更大或更小）的顺序称为偏序。 请参见第341页的`因果顺序不是全序`。

### 事务（transaction）
为了简化错误处理和并发问题，将几个读写操作分组到一个逻辑单元中。 见第7章。

### 两阶段提交（2PC, two-phase commit）
一种确保多个数据库节点全部提交或全部中止事务的算法。 请参阅第354页上的`原子提交和两阶段提交（2PC）`。

### 两阶段锁定（2PL, two-phase locking）
一种用于实现可序列化隔离的算法，该算法通过事务获取对其读取或写入的所有数据的锁，直到事务结束。 请参阅第257页上的`两阶段锁定（2PL）`。

### 无边界（unbounded）
没有任何已知的上限或大小。 反义词是边界（bounded）。

## 参考

[设计数据密集型应用 - 中文翻译](https://legacy.gitbook.com/book/vonng/ddia-cn/details)
[ddia-references](https://github.com/ept/ddia-references)