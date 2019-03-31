---
categories:
- notes
comments: true
date: 2019-03-27T00:00:00Z
tags:
- Amazon Aurora
title: Amazon Aurora
draft: true
---

#### Decoupling of compute and storage

Decoupling of compute and storage helps in cloud environment to handle operations such as 

* replacing misbehaving or unreachable host
* adding replicas
* failing over from a writer to a replica 
* scaling the size of a database instance up or down, etc.

*Network between the database tier requesting I/Os and the storage tier increases tremendously with the decoupling of storage and compute.*

#### Sychronous operations

- Sychronous operations results in stalls and context switches. One such situation is a disk read due to cache miss in the database buffer cache. 
- A reading thread connot continue untils its read is complete, it may also incur the extra penalty of evicting and flushing a dirty cache page to accomodate the new page.

#### Transaction commits

- Transaction commits are another source of interference; a stall in committing one transaction can inhibit others from progressing. 
- Handling commits with multi-phase synchronization protocols such as 2-phase commit (2PC) is challenging in a cloudscale distributed system. 
- These protocols are intolerant of failure and high-scale distributed systems have a continual “background noise” of hard and soft failures. They are also high latency, as high scale systems are distributed across multiple data centers.


### Amazon Aurora

A new database service that addresses the above issues by more aggressively leveraging the **redo log** across a highly-distributed cloud environment.

- Each instance still includes most of the components of a traditional kernel (query processor, transactions,
locking, buffer cache, access methods and undo management) several functions (redo logging, durable storage, crash recovery, and backup/restore) are **off-loaded to the storage service**.

**Amazon Aurora architecture has three significant advantages over traditional database systems.**

- By building storage as an independent faulttolerant and self-healing service across multiple data-centers
    * we protect the database from performance variance and transient or permanent failures at either the networking or storage tiers.
- By only writing redo log records to storage 
    * we are able to reduce network IOPS by an order of magnitude. 
    * Once we removed this bottleneck, we were able to aggressively optimize numerous other points of contention, obtaining significant throughput improvements over the base MySQL.
- Move some of the most complex and critical functions (backup and redo recovery) from one-time expensive operations in the database engine to continuous asynchronous operations amortized across a large distributed fleet. 
    * This yields near-instant crash recovery without checkpointing as well as inexpensive backups that do not interfere with foreground processing.

#### DURABILITY AT SCALE

##### Replication and Correlated Failures

*Instances can fail anytime and it makes sense to decouple the storage and compute to recover from instance failures. Storages, network also fails in a large-scale cloud environment.*

One way to mitigate such failures is to use a **quorum-based voting protocol**. 
If each of the `V` copies of a replicated data item is assigned a vote, a read or write operation must respectively obtain a read quorum of `Vr` votes or a write quorum of `Vw` votes. To achieve consistency, the quorums must obey two rules.

- First, each read must be aware of the most recent write, formulated as `Vr + Vw > V`. This rule ensures the set of nodes used for a read intersects with the set of nodes used for a write and the read quorum contains *at least one location with the newest version*.
- Second, each write must be aware of the most recent write to avoid conflicting writes, formulated as `Vw > V/2`.

A common approach to tolerate the loss of a single node is to replicate data to `(V = 3)` nodes and rely on a write quorum of `2/3 (Vw = 2)` and a read quorum of `2/3 (Vr = 2)`.

We believe 2/3 quorums are inadequate. 


To understand why, let’s first understand the concept of an Availability Zone (AZ) in AWS. An AZ is a subset of a Region that is connected to other AZs in the region through low latency links but is isolated for most faults, including power, networking, software deployments, flooding, etc. Distributing data replicas across AZs ensures that typical failure modalities at scale only impact one data replica.

This implies that one can simply place each of the three replicas in a different AZ, and be tolerant to large-scale events in addition to the smaller individual failures.

However, in a large storage fleet, the background noise of failures implies that, at any given moment in time, some subset of disks or nodes may have failed and are being repaired. These failures may be spread independently across nodes in each of AZ A, B and C.

However, the failure of AZ C, due to a fire, roof failure, flood, etc, will break quorum for any of the replicas that concurrently have failures in AZ A or AZ B. At that point, in a 2/3 read quorum model, we will have lost two copies and will be unable to determine if the third is up to date. In other words, while the individual failures of replicas in each of the AZs are uncorrelated, the failure of an AZ is a correlated failure of all disks and nodes in that AZ. Quorums need to tolerate an AZ failure as well as concurrently occuring background noise failures.


In Aurora, we have chosen a design point of tolerating (a) losing
an entire AZ and one additional node (AZ+1) without losing data,
and (b) losing an entire AZ without impacting the ability to write
data. We achieve this by replicating each data item 6 ways across
3 AZs with 2 copies of each item in each AZ. We use a quorum
model with 6 votes (V = 6), a write quorum of 4/6 (Vw = 4), and a
read quorum of 3/6 (Vr = 3). With such a model, we can (a) lose a
single AZ and one additional node (a failure of 3 nodes) without
losing read availability, and (b) lose any two nodes, including a
single AZ failure and maintain write availability. Ensuring read
quorum enables us to rebuild write quorum by adding additional
replica copies.