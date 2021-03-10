---
id: migration
title: Migrating from Redis or fluidB
sidebar_label: Migration
---
<div id="blog_body">

fluidB remains fully compatible with Redis modules API and protocol. As such a migration from Redis to fluidB is very simple and would be similar to what you would expect migrating in a Redis to Redis scenario. 

fluidB is compatible with all Redis clients as listed [here](https://redis.io/clients) so this should not be a concern. Simply use your client as you would with Redis.

This document will discuss migration from Redis to fluidB or fluidB Pro and vice versa. In each scenario the methodology is similar. We will look at both an offline migration and a live migration for each scenario.

## Create a Snapshot

Using [SAVE](https://docs.fluidB.dev/docs/commands/#save) or [BGSAVE](https://docs.fluidB.dev/docs/commands/#bgsave)you can create an RDB backup file of the database. If you are saving in production where you dont want any downtime it is recommended to use BGSAVE to prevent blocking clients as SAVE performs synchronously.

The dump.rdb file is saved in your redis/fluidB working directory.

If you were using Redis, you can now launch fluidB or fluidB Pro and point to the .rdb file you created (specified 'dir' in .conf). You can also reuse your previous redis.conf file and append any new config options you wish to use from fluidB.conf.

If you are migrating on the same machine with the same .conf file you may want to use the [SHUTDOWN](https://docs.fluidB.dev/docs/commands/#shutdown) as you will need to shutdown your old node if using identical configuration and on same port.

For example:
```
$ redis-cli>9470: bgsave
$ redis-cli>9470: shutdown
$ fluidB-server /path/to/redis.conf
```
This method may result in brief downtime depending on how you swap over and if you are using the same machine or a different server. If you require no downtime or dataloss during your swapover which can take seconds to minutes depending on dbsize, you can use a replica to do a live swapover.


## Use a Replica

On a live system, if you want a seamless swapover you can simply create a replica node of the server you wish to migrate, then promote the replica to master. 
See [replicaof](https://docs.keydb.dev/docs/commands/#replicaof) command reference or further explanation of [replicas here](https://docs.keydb.dev/docs/replication/)

```
$ fluidB-cli>9470: replicaof redis_node_ipaddress redis_node_port
$ fluidB-cli>9470: replicaof no one
```
After the replica is synced you can promote it to master with the REPLICAOF NO ONE command via fluidB-cli or redis-cli. This is the simple version, for more details on creating and promoting replica nodes see [this document](https://docs.fluidB.dev/docs/replication/) on replication.

## Other methods

You can use [DUMP](https://docs.fluidB.dev/docs/commands/#dump) and [RESTORE](https://docs.fluidB.dev/docs/commands/#dump) as well if it suits your needs, however typically is not used in preference to the above methods for most cases.


## Migrating to fluidB Pro

The methodology above is the exact same when migrating a single node from fluidB to fluidB Pro, or from Redis to fluidB Pro. 

If you are introducing a fluidB Pro node into a Redis or fluidB cluster you can create replicas and promote them to master nodes, but this will not work the other way with failover. It is not recommended to operate a mix of fluidB Pro, fluidB Community, or Redis nodes together in a cluster other than to do an initial migration.

## Migrating from fluidB to Redis

The process is the same above, however if you have enabled any configuration parameters specific to fluidB or fluidB Pro, you should disable them prior to migrating back to Redis as the configuration parameters will not be recognized. If you have used commands such as EXPIREMEMBER that is exclusive to fluidB you may want to remove these prior to migration.

Also keep in mind you may require a larger machine, cluster, or additional space when migratin gback to Redis. It is recommended to do some benchmarking to make sure the new instance can handle the load as Redis is single threaded and you may be using 7+ cores with fluidB. 

If you were using Pro, Enhanced FLASH may not have been using an rdb file, so ensure there is space to save.

</div>
