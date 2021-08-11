---
title: "DDIA Notes Ch.6: Partitioning"
date: 2021-08-10T21:26:38-05:00
tags: ['DDIA']
categories: ['System_Design']
author: 'Christian'
draft: false
---

# Ch 6: Partitioning
Note: These are my notes from reading chapter 6 of Designing Data Intensive
applications.

For large datasets, or very high query throughput, we need to break the data
up into partitions, also known as sharding.

- In effect, each partition is a small database of its own.

## Partitioning and Replication

Basically, you can use both partitioning and replication together to have
high availability. You can have copies of each partition stored on multiple 
nodes. And a node can store more than one partition.

## Partitioning of Key-Value data

The goal of partitioning is to evenly spread out the work among your nodes.
Something to look out for is hot spots, areas where lots of data is clustered
for some reason or other, and you end up getting bottlenecked.

### Partitioning by Key Range

An approach to partitioning is by separating your data into ranges. 
For example if you have it indexed by letter keys, you could have groups like
A through F, and G through O. Then, when you need to find something it becomes
easy as long as your keys are in sorted order.

### Partitioning by Hash of Key

Certain access patterns of the key-range partitioning method can lead to hot
spots. For ex: if the key is a timestamp, then the partitions correspond to
ranges of time, this can lead to hot spots if all the writes happen at the
same time. Because of this risk of skew and hot spots, many distributed 
datastores use a hash function to determine the partition for a given key.

A good hash function takes skewed data and makes it uniformly distributed.

Once you have a good hash function for keys, you can assign each partition a 
range of hashes rather than a range of keys.

### Skewed Workloads and Relieving Hot Spots

Sometimes having a good hash function isn't enough, because for example a hot
source could produce hot spots with the same hash. 
**A simple technique is to add a random number to the beginning or end of the key.** 

Just a two digit decimal random number would split the writes to the key evenly 
across 100 different keys.

### Partitioning and Secondary Index

Partitioning becomes more complex if secondary indexes are involved.

*What is a secondary index?*

A secondary index usually doesn't identify a record uniquely, but rather is a 
way of searching for occurrences of a particular value. 
Find all actions by user 123, find all cars whose color is red, etc.

**Problem: They don't map nearly to partitions.**

**solution: Document-based partitioning and term-based partitioning**

## Rebalancing Partitions

As your db grows and changes, data and requests need to be moved from one node to another.
The process of moving load around is called rebalancing.

### Strategies for Rebalancing

**Hash mod N**: Bad. Too costly, and moves data around unnecessarily

**Fixed number of partitions**: Pretty good, create many more partitions than 
there are nodes. The only thing that changes is the assignment of partitions to nodes.

**Dynamic partitioning:** key range-partitioned databases create partitions
dynamically. When a partition grows to exceed a configured size, it is split 
into two partitions so half the data ends up on each side.

An advantage is that the number of partitions adapts to the total data volume. 
But while the dataset is small, all writes have to be processed by a single node while the other nodes sit idle.

**Have a fixed number of partitions per node.**

## Request Routing

This is an instance of a more general problem called **service discovery.** Few different approaches:

- Allow clients to contact any node
- Send all requests from clients to a routing tier first
- Require that clients be aware of the partitioning and the assignment of partitions to nodes.

## Summary

Partitioning is necessary when you have a ton of data that storing and processing it on a single machine is not possible.

**Goal of partitioning:** Spread the data and query load evenly across multiple machines and avoid hot spots (nodes with high load).

**Two main approaches to partitioning:**

- **Key range partitioning**
- Hash partitioning

A secondary index also needs to be partitioned. Two approaches to secondary index partitioning:

- **Document partitioned indexes (local indexes):** secondary indexes are stored in the same partition as the primary key and value. Write can be to a single partition, but a read is heavy and requires a scatter/gather across all partitions.
- **Term-partitioned indexes (global indexes):** secondary indexes are partitioned separately. An entry in the secondary index may include records from all partitions of the primary key. Writes are heavier, but read can be from a single partition.
