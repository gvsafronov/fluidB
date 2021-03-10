---
id: pro-non-blocking-queries
title: Non-Blocking Queries
sidebar_label: Non-Blocking Queries
---

## The Stigma of Big Queries and Redis

You have likely seen memes like this before, or read in countless stackoverflow posts about the `KEYS` command crashing peoples servers or taking the website to crawling speeds. The screenshot below is taken from a RedisLabs article https://redislabs.com/blog/5-key-takeaways-for-developing-with-redis/

![image](https://keydb.dev/assets/img/blog/keys-prod-bad-time.png)
                                                       
Redis put out this meme in light humor of the KEYS command frequently being called in less than ideal circumstances, crashing production servers. I thought it was hilarious when I first saw it, however it makes you think, this happens with people looking for a solution and not being aware of command limitations. There is a lot you can do with the KEYS command and people seem to like using it because it helps solve their problems. It also makes maintenance and management much simpler. 

Redis put in SCAN command for a production use to split up the query. However querying is an important aspect when managing data.

## fluidB-Pro Querying

There are a lot of O(n) heavy commands that can drastically block resources and reduce performance. Especially with a single threaded engine like Redis. With the implementation of MVCC and KeyDB’s multithreading we can now perform complex queries concurrently without major performance penalties. Hence our answer to Redis’ south park meme comes with one of our philosphies… 

![image](https://keydb.dev/assets/img/blog/keys-prod-good-time.png)
                                                         
There’s a reason we often hear “don’t use KEYS”, and “don’t use fsync always”…there are a lot of “do not’s” with Redis that can affect performance in big ways. You shouldn’t need to be an expert to avoid major issues running your database. Redis’ single threaded nature and their requirement to keep a minimal codebase has ended up effecting the work required by the end user. This correlates to external tools, running multiple types of databases, excessive sharding, limited node capacity, etc.

When it comes to database management, if you care about your data you want to be able to properly manage and sift through it. Running queries to assist with understanding the data you have is important. Being able to run such queries in a non-blocking fashion, using separate threads, adds a powerful set of tools that doesn’t severely affect your production workloads and user experience.


