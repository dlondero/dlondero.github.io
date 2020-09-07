---
layout: post
title: Saving $70,000 a year on Atlas MongoDB
categories:
- mongodb
- nosql
---

**TL;DR Make sure to have all the right indexes on the right places. If necessary modify documents' structure in order to
create them. Optimize queries and commands so there are no in memory stages to run after queries. Make good use of 
memory: dataset and indexes must fit into memory to have good performances. If that's not the case and you start to see
high I/O values, then it's time to resize your collections.**

In the past months I got to help one of my clients to optimize their usage of Atlas MongoDB replicaSet cluster. Long 
story short they're now saving roughly **$70,000 a year** with the same amount of requests. Let's see some take aways 
from this experience and how you can replicate it.

#### Analyze slow queries
In order to optimize something you first need to know **what** to optimize. Then start from the things that allow to 
achieve highest results with the lowes effort. Atlas supports you with:
- [Query Profiler](https://docs.atlas.mongodb.com/tutorial/profile-database/#query-profiler): shows you slow-running operations and insights on their key perfomance statistics  
- [Performance Advisor](https://docs.atlas.mongodb.com/performance-advisor/#performance-advisor): suggests new indexes
to improve query performance of queries considered slow from MongoDB monitor

After having a bit of more information about what's going on on the system you can start to look into changes/optimizations.

#### Indexes
[Indexes](https://docs.mongodb.com/manual/indexes/) are vital on MongoDB in order to support the efficient execution of 
queries. Their mission is to limit the number of documents a query must inspect in order to give you a result: the smaller
the number, the better. Worst case scenario is when no index could be used for a query and an entire collection scan is 
performed: imagine running thousands of such queries on a collection with hundreds of millions documents, disaster will
follow.

It might happen that a document is not structured properly to support index creation on the right data fields. Don't be 
afraid of changing its structure if that allows to improve query performances. What about old documents? You say. Start 
creating new ones in a backward compatible way: still write data in the old way plus the new way. Then process old 
documents to add support for future index running a background process going through all documents (even if it takes weeks!).
In the end you'll be able to create the new index and take full advantage of it!

Do not forget about sorting! If you happen to have [compound indexes](https://docs.mongodb.com/manual/core/index-compound)
remember that sort order can matter in determining whether an index can support a sort operation or not. You could think
an index is used on a certain query while instead that is not the case, so make sure no blocking sorts are needed for 
your queries and learn [how to use indexes to sort query results](https://docs.mongodb.com/manual/tutorial/sort-results-with-indexes/).

#### Aggregation pipeline optimization
Aggregation pipeline operations have an optimization phase which attempts to reshape the pipeline for improved 
performance. Including the `explain` option in the `db.collection.aggregate()` method you could see how the optimizer 
transforms a particular aggregation pipeline. However sometimes aggregations are created in a way that the optimizer 
cannot do its magic. This was the case in this project for some aggregations (because of an internal library preparing
aggregations to pass to the MongoDB library): so for some cases with small changes it was possible to avoid in-memory 
sort stage simply combining `$match` and `$sort` stages at the start of the pipeline so they are logically equivalent
to a singe query with a sort and can use an index! This alone cut the CPU usage by 50%. When possible, place `$match` 
operators at the beginning of the pipeline.

#### Make good use of memory
When you have a good amount of memory and small collections (so small indexes) it's all roses. Things get complex when
collections and indexes grow so much that they can't fit into memory (caches: WiredTiget internal and filesystem cache) 
and evicting pages from cache does not help: high disk I/O, CPU usage skyrocketing, unusable system.

The default [WiredTiger internal cache size](https://docs.mongodb.com/manual/faq/diagnostics/#memory-diagnostics-for-the-wiredtiger-storage-engine) is the larger of either:
- 50% of (RAM - 1 GB)
- 256 MB

At some point the biggest and most used collection of the project had ~300M documents in it using 130GB of storage alone,
indexes accounted for another 30GB. Considering also some other collections on the cluster it was not possible to use
any cluster tier smaller than an M200 (240GB RAM / 1500GB storage / 64 vCPUs). **Analyzing the contents of the collection 
and applying the right data retention policy (only way to shrink the collection) things got luckily better going down to
only ~70M documents using less than 40GB of storage (plus 21GB of indexes)**. Yep, numbers are right and crazy!

So ask yourself if you need all that data and how you could have a smaller hot collection. You may not necessarily need 
to just delete it as you could move it to a cold storage or simply to a separate collection and change how the 
application accesses it. This depends on a case by case bases.

#### Wrapping up
The impact of all these actions was terrific as hourly cost for the cluster went down from $12.96 to $4.91. 
This means $70,518 ((12.96-4.91)*24*365) of savings over a year! In fact from an M200 the cluster was resized to 
M80 Low CPU (128GB RAM / 750GB storage / 16 vCPUs) thanks to all the improvements mentioned above.

## Want more?
You can [follow me](https://twitter.com/dlondero) on Twitter as I continue to document my journey as a consultant.
