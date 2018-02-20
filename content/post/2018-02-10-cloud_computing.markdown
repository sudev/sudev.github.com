---
categories:
- notes
comments: true
date: 2018-02-10T00:00:00Z
tags:
- large systems
- concepts
- cloud computing
title: Notes on cloud computing
draft: true
---

# Basics 

## What is a Cloud? 

A Cloud consists of a lot of storage resources, so you can store gigabytes, terabytes, petabytes, maybe even more of data with compute cycles located nearby. 

Two Kinds. 

* Single site clouds (a.k.a Datacenters)
	- Computer Nodes(groped into racks) ( AWS EC2's)
	- Switches connecting racks 
	- A network topology hierarchical (Availability Zones, VPC, Subnets)
	- Storage backend nodes connected to network (EBS, S3?)
	- Frontend for submitting jobs and receiving client requests (EMR, ECS)
	- Software Services (A lot of them)
* A geographically distributed cloud consists of 
	- Multiple datacenters (AWS US-East, AWS Mumbai)
	- Each site perhaps with a different structure and services

## What is an operating system? 

- User interface to hardware(device drivers) (USB,monitors)
- Provides Abstractions(process, file systems)
- Resource Manager(scheduler)
- Means of communication(networking)

# Distributed Systems

## MapReduce

Terms are borrowed from functional languages (eg lisp)

``` lisp 
(map square '(1 2 3 4))

(reduce + (1 4 9 16))

;; output 30
```


### Main Components of MR

- Global Resource Manager (RM)
	* Scehduling 
- Per-server Node Manager (NM)
	* Daemon and server specific functions
- Per-application (job) Application Master (AM)
	* Container negotiation with RM and NMs.
	* Detecting task failure of that job.

### Fault Tolerance

- NM heartbeats to RM 
	* If server fails, RM lets all effected AMs know, and AMs take action
- NM keeps track of each task running at its servers 
	* If task fails while in-progress, mark the task as idle and restart it  
- AM heartbeats to RM 
	* On failure, RM restarts AM, which syncs up with its running tasks
- RM Failure 
	* Use old checkpoints and bring up secondary RM

To deal with slow jobs [speculative execution][se] is used as mitigation. 

Locality. Maps are scheduled are to systems which have data where Maps data.

Papers/Blogs around map reduce.   

- [Google MapReduce Paper][mrp]   
- [Hadoop Design Doc][hadoop_design]  
- [Datadogs blog on MR/Hadoop][dd_blog]

# Gossip 

## Multicast Problem

A node in the distributed system wants to send a message to other nodes in the distributed system. *(Brodcast is to send a message to all nodes in a network, where multicast rather wants to send message to some/specific nodes within it's system.)*

Challenges 

- Nodes may crash (receier/sender)
- Packets may drop
- A lot of nodes

Ways to solve multicast problem. 

- **Centralised systems** 
	* where the sender sends messages to all nodes in a loop. Here, the sender might in between the transfer of messages, the time taken to send message to all nodes is in the `O(n)`.
- **Tree based** 
	* essentially, all of these protocols develop a spanning tree among the nodes or the processes in the group. These include, network level protocols, such as, IP multicast, where the spanning tree's among the routers and switches, in the underlying network, but also application level multicast protocols, such as, SRM, RMTP, TRAM, TNTP, and a whole bunch of others. 
	* Roots nodes are critical here, when nodes on the top of the tree fails, the downstream wont get any messages. 
	* Use wither acknowledgements(ACKs) or negative acknowledgements (NAKs) to repair multicasts not received. 
	* [SRM][srm] (Scalable Reliable Multicast) 
		- Uses NAKs
		- But adds random delays, and uses exponential backoff to avaoid NAK storms. 
	* [RMTP][rmtp] (Reliable Multicast transport protocol)
		- Uses ACKs
		- But ACKs only sent to designated receivers, which then re-transmit missing multicasts

All of these protocols still cause an `O(n)` ACK/NAK overhead and motivated the development of Gossip protocol. 

## Gossip 

* Periodically sender transmits message to random subset of receivers
* Usually UDP
* Node who have received the message are called the *infected nodes*. Now the infected nodes upon receiving the message will start gossiping the message to other nodes in the system, infecting more nodes. With time all nodes will be infected in the system.
* There will be duplicate messages with this approach. 
* Gossip can be both Push, Pull or a hydrid of both. The points discussed above realtes to a push based protocol.

### Analysis

Analyse the push based protocol.

```
∂x∕∂t = -βxy
```
Rate at which the uninfected nodes change with time is negative of `-βxy` where `-β` is the probability of contact between infected node and uninfected node. Also the number of nodes `n` is equal to `x+y`. x is number of unifected node and y is the number of infected node.

Value of `β`, essentially it is the probability that a uninfected node hits a infectected node. 

`β = b/n`

Need to understand the analysis better. [Read this][goosip_blog] and papers mentioned in it.

In nutshell, the gossip protocol in `c*log(n)` rounds all but `1/n(cb-2)` number of nodes receive the multicast. Each node has transmitted no more than `c*b*log(n)` goissip messages. Here `b` is the contact rate, `c` is the constant for log(n).

**Gossip will do the mesage propogation with `log(n)` iteration.**

### Implementations 

Many systems have successfully implemented gossip.

* Cassandra use gossip for maintaining membership protocol. Read [this][c_gossip] and [this][c_gossip2].
* Sensor Networks 
* Usenet NNTP


[se]: https://en.wikipedia.org/wiki/Speculative_execution
[mrp]: https://static.googleusercontent.com/media/research.google.com/en//archive/mapreduce-osdi04.pdf
[hadoop_design]: https://hadoop.apache.org/docs/r1.2.1/hdfs_design.pdf
[dd_blog]: https://www.datadoghq.com/blog/hadoop-architecture-overview/
[srm]: http://www.icir.org/floyd/papers/srm_ton.pdf
[rmtp]: https://pdfs.semanticscholar.org/2b55/4ae834827462bc629fde348e239a18ee0ff9.  
[goosip_blog]: http://alvaro-videla.com/2015/12/gossip-protocols.html
[c_gossip]: https://docs.datastax.com/en/cassandra/2.1/cassandra/architecture/architectureGossipAbout_c.html
[c_gossip2]: https://wiki.apache.org/cassandra/ArchitectureGossip