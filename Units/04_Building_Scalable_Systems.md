# Building Scalable Systems

## Table of Contents

- [Building Scalable Systems](#building-scalable-systems)
  - [Table of Contents](#table-of-contents)
  - [Consistency, Availability and Scalability](#consistency-availability-and-scalability)
    - [Consistency](#consistency)
    - [Availability](#availability)
    - [Scalability](#scalability)
    - [Performance vs Scalability](#performance-vs-scalability)
  - [Consistency in Distributed Systems](#consistency-in-distributed-systems)
    - [Eventual Consistency](#eventual-consistency)
    - [Strong Consistency](#strong-consistency)
  - [Laws of Scalability](#laws-of-scalability)
    - [Effect of Contention](#effect-of-contention)
      - [Amdahl's Law](#amdahls-law)
    - [Effect of Coherency Delay](#effect-of-coherency-delay)
      - [Gunther's Law](#gunthers-law)
  - [CAP Theorem](#cap-theorem)
    - [Partition Tolerance](#partition-tolerance)
  - [Sharding](#sharding)
    - [Consistency And Scalability](#consistency-and-scalability)
    - [Sharding For Consistency](#sharding-for-consistency)
    - [Effects of Sharding](#effects-of-sharding)
  - [CRDTs](#crdts)
    - [Availability And Scalability](#availability-and-scalability)
    - [CRDTs For Availability](#crdts-for-availability)
      - [CvRDTs](#cvrdts)
      - [CmRDTs](#cmrdts)
    - [Effects Of CRDTs](#effects-of-crdts)
  - [Consistency Or Availability](#consistency-or-availability)

## Consistency, Availability and Scalability

The world around us is a distributed system

### Consistency

If all members of the system agree on the state, then the system is consistent

### Availability

An available system will continue to respond, even in the presence of failures

### Scalability

A scalable system will be able to meet an increase in demand, without sacrificing responsiveness

### Performance vs Scalability

Performance and scalability are related but different

Improving **performance** optimizes **response time**\
The total number of requests (load) may not change

Improving **scalability** optimizes the ability to **handle load**\
Performance of each request may not change

Requests Per Second is a way to measure Performance and Scalability

Reactive Microservices tend to focus on Improving Scalability\
Scalability has no theoretical limit. We may be restricted by our implementation, but in a perfectly scalable system, we could scale forever

## Consistency in Distributed Systems

Distributed systems are separated by space\
The speed of light is the upper limit of the information speed\
There is always time required to reach consensus\
While transferring information, the state of the sender may have changed

### Eventual Consistency

After a system stops receiving updates, you can guarantee that all parts of the system will eventually converge on the same state

### Strong Consistency

All members of a system must agree on the state, before it becomes available

Locks are intoduced to simulate Strong Consistency
Locks introduce a problem in the form of Contention

Traditionally, monolithic architectures are based around Strong Consistency

## Laws of Scalability  

### Effect of Contention

Competition for a single limited resource can have **only one winner**\
Others are forced to wait for the winner to complete\
The time to free up increases as the number of things competing increases\
Increasing load will exceed acceptable time limits

#### Amdahl's Law

**Amdahl's Law** defines the **maximum improvement gained by parallel processing**\
Improvements from parallelization are **limited to the code that can be parallelized**\
Contention limits parallelization

### Effect of Coherency Delay

Synchronizing the states of multiple nodes is dones is done using **crosstalk** or **gossip**\
Each node sends messages to each other to inform of any state changes\
The time it takes for synchronization is called **Coherency Delay**\
Increasing the number of nodes increases the Coherency Delay

#### Gunther's Law

**Gunther's Universal Scalability Law** is based on Amdahl's Law\
It accounts also for **Coherency Delay**\
Coherency Delay results in **negative returns**\
As the system scales up, the time taken to synchronize and coordinate all nodes **exceed the benefits**

According to the "Mythical Man Month", adding more people to a late project will usually make the project slow down, rather than speed up

Linear scalability is almost always **unreachable**\
Linear scalability **requires total isolation**, stateless\
Reactive Systems try to **exploit the limitations** instead of avoiding them

Contention is reduced by:

- Isolating **Locks**
- Eliminating **Transactions**
- Avoiding **Blocking Operations**

Coherency Delay is reduced by:

- Using **Eventual Consistency**
- Building in **Autonomy**

We can get higher scalability reducing the things that prevents it

## CAP Theorem

A Distributed System **can not provide more than two of the following**:

- Consistency
- Availability
- Partition Tolerance

### Partition Tolerance

The system continues to operate despite an arbitrary number of messages is being dropped or delayed by the network

No distributed system is safe from it\
They can be caused by netowrk congestion\
They can be caused by a node that goes down\
They can be short or long

When a partition occurs there are two options:

- (AP) **Sacrifices Consistency** by **allowing to write** to both sides of the partition. When it is resolved **data must be merged** to restore consistency
- (CP) **Sacrifices Availability** by **disabling one side** of the partition. During the partition some part of the system will be unavailable

A **balance** between AP and CP should be found

## Sharding

### Consistency And Scalability

Consistency creates contention\
Contention means scalability will have diminishing or negative returns

To improves scalability contention must be eliminated and crosstalk reduced\
If they can't be eliminated they should be reduced

Locks with a **broad scope** (like Table Locks) creates **a lot of contention**\
Locks with a **smaller scope** (like Row/Record Locks) creates **less contention**

**Sharding** can be used to reduce contention and crosstalk\
It's applied at the **application level**

### Sharding For Consistency

It **partitions Entities** in the domain based on their id\
Group of Entities are called a **Shard**\
Each entity exists **only in one shard**\
Each shard exists **only in one location**\
Because each entity exists only in location, the distributed system problem is avoided\
Entity acts as a consistency boundary

A **coordinator** ensures that traffic for an entity is routed to the correct location\
The id of the entity is used to calcolate the Shard\
All traffic for a particular id goes through the **same entity**\
Aggregate roots are goods for sharding

A good shard key provides an even, **randomized distribution**\
**UUIDs** or **hash** are common sharding strategies since they provide randomized distribution\
Poor key selection results in hotspots\
The sweetspot is 10 shards for each node

### Effects of Sharding

Sharding **isolates contention** without eliminating it\
For a single entity **there is no contention**\
The coordinator is also a **source of contention**\
A sharded system limits the amounts of work the coordinator performs and isolates contention to single entities

Scalability is achieved by **distributing shards** over more machines\
Strong consistenct is achieved by **isolating operations** to a specific entity\
Choosing the **right shard keys** is essential to mantain good scalability

Sharding **sacrifices Availability** (CP)\
If a shard goes down, it is unavailable\
The shard will migrate to another node

Sharding allows each entity to mantain **cache consistency**\
The **cache is unique** to that entity

## CRDTs

### Availability And Scalability

CRDT is short for: Conflict-free Replicated Data Types

Sharding provides consistency and scalability\
CRDTs provides **high availablity** solution based on asynchronous replications

### CRDTs For Availability

CRDTs are specially designed Data Types\
They are **High Available** and **Eventually Consistent**\
Data is stored on **multiple replicas** for availability\
Updates are applied on one replica and then **copied asynchronously**\
Updates are **merged** to determine final state\
CRDTs **sacrifice Consistency** (AP)

#### CvRDTs

**Convergent** Replicated Data Types copy state between replicas\
A merge operation is required with the following property:

- **Commutative** (Can be applied in any order)
- **Associative** (Can be grouped in any order)
- **Idempotent** (Duplicates operations don't change the result)

#### CmRDTs

**Commutative** Replicated Data Types copy operations between replicas

Other Data Types can be created defining **merge functions**\
CRDTs can be **nested** to create complex structures\
Existing CRDTs include Sets, Registers, Counters, Maps

### Effects Of CRDTs

CRDTs are **stored in memory**\
Optionally, copying CRDTs to disk allows you to speed up recovery in the event of a failure\
CRDTs are best used for **small data sets with infrequent updates** that requires **high availability**

Limitations:

- You must **define a merging functions**, so they don't work with every Data Type
- Complex merge functions require the use of **tombstones** (marker that show something was deleted)
- Tombstones can causes data types that **get bigger** and never shrink

## Consistency Or Availability

The choice between Consistency and Availability is a **business decision**\
A **balance between them** should be found
