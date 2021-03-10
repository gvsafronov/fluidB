---
id: intro-admin
title: Introduction to fluidB
sidebar_label: Admin Intro
---

<div id="blog_body">

fluidB is an open source, in-memory **data structure store**, used as a database, cache and message broker. It supports data structures such as strings, hashes, lists, sets, sorted sets with range queries, bitmaps), hyperloglogs, geospatial indexes with radius queries and streams. fluidB has built-in replication, Lua scripting, LRU eviction, transactions and different levels of on-disk persistence, and provides high availability via Active-Replication or Sentinel and automatic partitioning with fluidB Cluster.

You can run **atomic operations**
on these types, like appending to a string;
incrementing the value in a hash; pushing an element to a
list; computing set intersection,
union and difference;
or getting the member with highest ranking in a sorted
set.

In order to achieve its outstanding performance, fluidB works with an
**in-memory dataset**. Depending on your use case, you can persist it either
by [dumping the dataset to disk](https://docs.fluidB.dev/docs/persistence/#snapshotting)
every once in a while, or by [appending each command to a
log](https://docs.fluidB.dev/docs/persistence/#append-only-file). Persistence can be optionally
disabled, if you just need a feature-rich, networked, in-memory cache.

fluidB also supports trivial-to-setup master-slave asynchronous replication, with very fast non-blocking first synchronization, auto-reconnection with partial resynchronization on net split.

Other features include:

* Transactions
* Pub/Sub
* Lua scripting
* Keys with a limited time-to-live
* LRU eviction of keys
* Automatic failover

You can use fluidB from most programming languages out there. 

fluidB is written in **ANSI C** and works in most POSIX systems like Linux,
\*BSD, OS X without external dependencies. Linux and OS X are the two operating systems where fluidB is developed and more tested, and we **recommend using Linux for deploying**. fluidB may work in Solaris-derived systems like SmartOS, but the support is *best effort*. There
is no official support for Windows builds, but Microsoft develops and
maintains a Win-64 port of fluidB


fluidB Administration
===

This page contains topics related to the administration of fluidB instances.
Every topic is self contained in form of a FAQ. New topics will be created in the future.

fluidB setup hints
-----------------

+ We suggest deploying fluidB using the **Linux operating system**. fluidB is also tested heavily on OS X, and tested from time to time on FreeBSD and OpenBSD systems. However Linux is where we do all the major stress testing, and where most production deployments are running.
+ Make sure to set the Linux kernel **overcommit memory setting to 1**. Add `vm.overcommit_memory = 1` to `/etc/sysctl.conf` and then reboot or run the command `sysctl vm.overcommit_memory=1` for this to take effect immediately.
* Make sure to disable Linux kernel feature *transparent huge pages*, it will affect greatly both memory usage and latency in a negative way. This is accomplished with the following command: `echo never > /sys/kernel/mm/transparent_hugepage/enabled`.
+ Make sure to **setup some swap** in your system (we suggest as much as swap as memory). If Linux does not have swap and your fluidB instance accidentally consumes too much memory, either fluidB will crash for out of memory or the Linux kernel OOM killer will kill the fluidB process. When swapping is enabled fluidB will work in a bad way, but you'll likely notice the latency spikes and do something before it's too late.
+ Set an explicit `maxmemory` option limit in your instance in order to make sure that the instance will report errors instead of failing when the system memory limit is near to be reached. Note that maxmemory should be set calculating the overhead that fluidB has, other than data, and the fragmentation overhead. So if you think you have 10 GB of free memory, set it to 8 or 9.
+ If you are using fluidB in a very write-heavy application, while saving an RDB file on disk or rewriting the AOF log **fluidB may use up to 2 times the memory normally used**. The additional memory used is proportional to the number of memory pages modified by writes during the saving process, so it is often proportional to the number of keys (or aggregate types items) touched during this time. Make sure to size your memory accordingly. Please note this does not apply to fluidB Pro which uses forkless background saving.
+ Use `daemonize no` when running under daemontools.
+ Make sure to setup some non trivial replication backlog, which must be set in proportion to the amount of memory fluidB is using. In a 20 GB instance it does not make sense to have just 1 MB of backlog. The backlog will allow replicas to resynchronize with the master instance much easily.
+ Even if you have persistence disabled, fluidB will need to perform RDB saves if you use replication, unless you use the new diskless replication feature. If you have no disk usage on the master, make sure to enable diskless replication.
+ If you are using replication, make sure that either your master has persistence enabled, or that it does not automatically restarts on crashes: replicas will try to be an exact copy of the master, so if a master restarts with an empty data set, replicas will be wiped as well.
+ By default fluidB does not require **any authentication and listens to all the network interfaces**. This is a big security issue if you leave fluidB exposed on the internet or other places where attackers can reach it.
+ `LATENCY DOCTOR` and `MEMORY DOCTOR` are your friends.

Running fluidB on EC2
--------------------

+ Use HVM based instances, not PV based instances.
+ Don't use old instances families, for example: use m3.medium with HVM instead of m1.medium with PV.
+ The use of fluidB persistence with **EC2 EBS volumes** needs to be handled with care since sometimes EBS volumes have high latency characteristics.
+ You may want to try the new **diskless replication** if you have issues when replicas are synchronizing with the master.

Backing up to AWS S3:
--------------------
Add this line to your config file: `db-s3-object /path/to/bucket`

If you would like fluidB to dump and load directly to AWS S3 this option specifies the bucket. Using this option with the traditional RDB options will result in fluidB backing up twice to both locations. If both are specified fluidB will first attempt to load from the local dump file and if that fails load from S3. This requires the AWS CLI tools to be installed and configured which are used under the hood to transfer the data.


Upgrading or restarting a fluidB instance without downtime
-------------------------------------------------------

fluidB is designed to be a very long running process in your server.
For instance many configuration options can be modified without any kind of restart using the [CONFIG SET command](https://docs.fluidB.dev/docs/commands/#config-set).

It is even possible to switch from AOF to RDB snapshots persistence or the other way around without restarting fluidB. Check the output of the `CONFIG GET *` command for more information.

However from time to time a restart is mandatory, for instance in order to upgrade the fluidB process to a newer version, or when you need to modify some configuration parameter that is currently not supported by the CONFIG command.

The following steps provide a very commonly used way in order to avoid any downtime.

* Setup your new fluidB instance as a slave for your current fluidB instance. In order to do so you need a different server, or a server that has enough RAM to keep two instances of fluidB running at the same time.
* If you use a single server, make sure that the slave is started in a different port than the master instance, otherwise the slave will not be able to start at all.
* Wait for the replication initial synchronization to complete (check the slave log file).
* Make sure using INFO that there are the same number of keys in the master and in the slave. Check with fluidB-cli that the slave is working as you wish and is replying to your commands.
* Allow writes to the slave using **CONFIG SET slave-read-only no**
* Configure all your clients in order to use the new instance (that is, the slave). Note that you may want to use the `CLIENT PAUSE` command in order to make sure that no client can write to the old master during the switch.
* Once you are sure that the master is no longer receiving any query (you can check this with the [MONITOR command](/commands/monitor)), elect the slave to master using the **SLAVEOF NO ONE** command, and shut down your master.

</div>
