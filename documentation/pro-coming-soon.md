---
id: pro-coming-soon
title: Coming Soon to fluidB Professional
sidebar_label: Coming Soon
---

In addition to the current fluidB Pro feature suite, we have some exciting new features on the way in the coming months, with some by the end of January. We also expect this list will continue to grow month over month. We get a lot of feature requests and are always interested in hearing about your use cases and requests.

If you want to keep up to date with fluidB and fluidB-Pro please <a href="https://eqalpha.us20.list-manage.com/subscribe/post?u=978f486c2f95589b24591a9cc&id=4ab9220500"><span style="color:red"> subscribe to our mailing list </span></a>.

## Features

fluidB is releasing affordable features that are both powerful and fast. We are introducing features that will start consolidating database expectations, offering the performance of a cache database with durability guarantees, also query support that can be used effectively. MVCC, and rollbacks begin to provide the control and management expected in a traditional database.

## fluidB Persistence Offerings

Intel Optane DC Persistent Memory is coming to the cloud this year and is starting to become more readily available to the public. fluidB on PMEM enables persistence at DRAM speeds. This means that once a value is written to the database it is persisted in the optane memory at that point (no waiting to persist to RDB or AOF). When the client receives the ‘OK’ the data is persisted and not waiting for the fsync interval to elapse. Other databases claiming to use PMEM do not use it natively and cannot come close to fluidB’s performance levels. There will be more information in the coming month on PMEM with fluidB.

fluidB Pro on FLASH is retained in FLASH where it is stored which offers a new level of persistence without the need for RDB/AOF. See <a href="https://docs.fluidB.dev/docs/pro-flash/"><span style="color:red"> FLASH  </span></a> in docs for more info. This feature is currently available.

## Transaction Rollbacks

Transaction Rollbacks are being introduced allowing isolation guarantees. In addition to ensuring atomicity where rollbacks are possible without a high level of pre-screening on the client side. Also enabling the user to rollback when they decide to cancel, etc. 

## Data Compression Options

Enable compression of objects to reduce database size and cut costs way down. For those limited by memory or paying a lot for the memory, this option has immediate payback. More information to come in the coming months

## TLS Encryption

TLS encryption will be available for Pro and open source fluidB. Enable data encryption and ensure transactions and operations are secure. 

## Subscribe

If you want to keep up to date with fluidB and fluidB-Pro please <a href="https://eqalpha.us20.list-manage.com/subscribe/post?u=978f486c2f95589b24591a9cc&id=4ab9220500"><span style="color:red"> subscribe to our mailing list </span></a>.

