---
layout: post
title: How We Store Data in the Cloud at Auth0
metatitle: How We Store Data in the Cloud at Auth0
description: Learn how Auth0 uses different datastores to serve a wide variety of use cases and power critical features.
metadescription: Learn how Auth0 uses different datastores, including MongoDB and PostgreSQL, to serve a wide variety of use cases and power critical features for customers.
date: 2018-09-07 13:26
category: Auth0 Engineering, Stack, Database
author:
  name: Dirceu Tiegs
  url: https://twitter.com/dirceu
  mail: dirceu.pereira@auth0.com
  avatar: https://cdn.auth0.com/blog/auziro/Dirceu-Tiegs.jpg
design: 
  bg_color: "#1B395E"
  image: https://cdn.auth0.com/blog/logos/auth0-architecture.png
tags:
  - database
  - engineering
  - redis
  - postgresql
  - aws
  - mongodb
  - elasticsearch
  - rds
  - sre
  - site-realiability
  - development
  - software
  - data
related:
  - 2018-08-15-auth0-architecture-running-in-multiple-cloud-providers-and-regions
  - 2018-06-07-developing-well-organized-apis-with-nodejs-joi-and-mongo
  - 2018-08-08-introduction-to-redis-install-cli-commands-and-data-types
---

Auth0 uses different datastores for different purposes. We have tons of datasets used to serve the wide variety of use cases and features we offer to our customers. In an age where data breaches are unfortunately getting more common, a critical side of choosing and using datastores is to consider reliability, durability, and safety. Our platform processes thousands of requests per second (**1.5 billion logins per month**) for customers all around the world &mdash; and we're growing very fast!

The Site Reliability team is a new initiative aimed at improving reliability and uptime in a data-driven way to support our customers' needs. Our team works closely with other teams to define and apply best practices through coding, writing, workshops, training, and leading different initiatives related to reliability, performance, and observability.

In this post, we will discuss the tools we use, why we use them and show more details about our internal setup. Some of these tools have been with us since the beginning &mdash; like MongoDB &mdash; while others are the result of extensive testing, research, and feature development &mdash; like PostgreSQL. Read on!

> Do you like to get your hands dirty by debugging and fixing issues in production? We are currently hiring engineers to [join the Site Reliability Engineering team](https://auth0.com/careers/positions?areas=Engineering)!

## MongoDB

[MongoDB](https://www.mongodb.com/) is our central datastore; it's our "source of truth" for most of the data that matter to our customers. We store settings, connections, rules, users, and much more.

Our MongoDB clusters serve many thousands of requests per second, but we don't store that much data: **our biggest MongoDB database has 30 GB**.

Given that our data sizes are tiny, we're still very comfortable with just a few big machines (tons of RAM and CPU cores, and high-speed disks) and we don't need fancy things like sharding &mdash; we can still do much with vertical scaling, and we still don't need horizontal scaling.

> As explained by David Beaumont from IBM on the article [_How to explain vertical and horizontal scaling in the cloud_](https://www.ibm.com/blogs/cloud-computing/2014/04/09/explain-vertical-horizontal-scaling-cloud/), **vertical scaling** can essentially resize our server with no change to our code while **horizontal scaling** affords the ability to scale wider to deal with traffic.

Each MongoDB cluster has six nodes:

- 1 Primary on the main region
- 1 Replica on the main region
- 1 Arbiter on the main region
- 2 Replicas on the failover region, hidden
- 1 Arbiter on the failover region, stopped

(The "main" and "failover" regions are explained in more details on our [Running In Multiple Cloud Providers And Regions post](https://auth0.com/blog/auth0-architecture-running-in-multiple-cloud-providers-and-regions/)).

Except for the arbiter on the failover region, all nodes are always connected to the replica set. We have a primary and replica that account for all queries under normal operations, and the nodes for failover regions just for disaster recovery.

We use [MongoDB Enterprise](https://www.mongodb.com/products/mongodb-enterprise-advanced) for all our cloud deployments: this helps us because of the additional metrics and also because of the excellent support we get from MongoDB.

The most challenging side of using MongoDB has been performance testing new queries: since we have multiple environments and regions with a wildly different amount of data, sometimes MongoDB might simply decide to _not_ use an index for some reason. We [rely on `$hint` for specific queries](https://docs.mongodb.com/manual/reference/operator/meta/hint/), but for most of the critical path, we try to ensure we have enough perf-testing coverage.

{% include tweet_quote.html quote_text="The most challenging part of using MongoDB has been performance testing new queries since we have multiple environments and regions with a wildly different data sets and usage patterns." %}

Since the conception of Auth0, MongoDB has been an essential piece of our infrastructure, and it should continue to be a massive part of our stack for a long time; it allowed us to iterate fast, grow to more than 1.5 billion authentication operations per month &mdash; and more.

## Elasticsearch

We have a long story with [Elasticsearch](https://www.elastic.co/products/elasticsearch), and not always a happy one. We use Elasticsearch to store three types of data for search:

1.  [User metadata](https://auth0.com/docs/metadata)
2.  [Audit logs](https://auth0.com/docs/logs): logs that our tenants can access via dashboard and API.
3.  Application logs: logs from our micro-services and "off-the-shelf" solutions like NGINX and MongoDB.

We use three different versions of ES:

1.  1.6, for certain tenants that we couldn't migrate yet to 5.4.
2.  2.3, for application logs.
3.  5.4, for user metadata.

We have multiple clusters per environment. For instance, in the US we have:

- 1 cluster with 2.3 for application logs (running [Kibana](https://www.elastic.co/products/kibana))
- 2 clusters with 5.4 for user metadata (regular and enterprise tenants)
- 2 clusters with 1.6 (for user metadata - tenants that couldn't be migrated to 5.4)

We have almost 80 Elasticsearch nodes in the US environment only. If we sum up all environments and regions, we probably have short of **200 big nodes running ES**. _Yikes!_ 💸

Our application logs clusters are by far the best ones. ES was pretty much made to deal with those kinds of logs, and besides occasional resizes we don't have many problems with them from an infrastructure perspective. **We store 2.3TB of data per day on them**.

We have had our issues with user metadata and audit logs on Elasticsearch though. Part of the reason is that we outgrew the tool, at least in the use case we have: we need to store hundreds of thousands of different fields for search, completely separated by tenant, and we need to scale for tenants with 1-10 users but also tenants with **6-8 million users**. Due to the performance and stability issues, we have decided to migrate two of those three use cases to PostgreSQL (more on that later).

Elasticsearch shines for general logging; if you use common patterns (daily indexes, metadata templates, hot and cold nodes), it can be a very powerful and stable tool.

Even for cases in which we found it not to be entirely adequate (user metadata and audit log search), it was certainly a great tool while we didn't reach "critical mass". [Elasticsearch helped us scale fast and gave us stability for years](https://auth0.engineering/from-slow-queries-to-over-the-top-performance-how-elasticsearch-helped-us-scale-4fe72ffcb823) before we outgrew it.

{% include tweet_quote.html quote_text="Due to performance and stability issues with Elasticsearch, we decided to migrate two of its three use cases to PostgreSQL. It was certainly a great tool while we didn't reach critical mass." %}

## PostgreSQL

We have three main databases stored in [PostgreSQL running on AWS RDS](https://aws.amazon.com/rds/postgresql/):

1.  Breached passwords
1.  User metadata (search v3)
1.  Tenant audit logs (under development)

Breached password detection protects and notifies your users when their credentials are leaked by a data breach of a third party. You can optionally prevent access until the user has reset their password. This feature uses a big [PostgreSQL database as a backend](https://aws.amazon.com/rds/postgresql/what-is-postgresql/): we have a subscription with a company that provides us with breached data, which we then import for querying.

Given the scalability issues we faced with Elasticsearch, we have completely rewritten the user metadata search feature into what we call [User Search v3](https://auth0.com/docs/users/search/v3), and we're [gradually migrating tenants](https://auth0.com/docs/users/search/v3#migrate-from-search-engine-v2-to-v3) to that new solution: it uses PostgreSQL instead of Elasticsearch for storage and search.

The performance differences between Elasticsearch and PostgreSQL are massive: instead of 3-5 thousand writes per second per cluster, we can have up to **400 thousand writes per second**, all with roughly the same infrastructure costs but with far less operational overhead (_thank you AWS RDS_ 💖).

Given the outstanding results of search v3 regarding performance and stability, we decided also to migrate our tenant logs feature. This is under active development and testing, but we expect great results from it.

In the future, we plan to keep using PostgreSQL more and more wherever it makes sense. As we gain more operational knowledge and build tooling around it, it will definitely become a big contender with MongoDB as the preferred storage choice for [new microservices](https://auth0.com/blog/an-introduction-to-microservices-part-1/): it's safe, fast, well documented, and very easy to operate.

{% include tweet_quote.html quote_text="The performance differences between Elasticsearch and PostgreSQL are massive: we can have up to 400 thousand writes per second, all with roughly the same infrastructure costs but with far less operational overhead." %}

## Redis

I don't have much to say on this one actually. [Redis](https://redis.io/) is just so... _awesome_. It's fast, it's stable, and we barely have to think about it, doing tens of thousands of requests per second on surprisingly small instances.

We use [Redis for caching](https://auth0.com/blog/introduction-to-redis-install-cli-commands-and-data-types/), not long-term storage; our "distribution of choice" for the cloud is [AWS ElastiCache](https://aws.amazon.com/elasticache/).

{% include tweet_quote.html quote_text="Redis is... awesome! We use it for caching with a cloud distribution through AWS ElastiCache." %}

## Recap

Auth0 is growing, and it's growing _fast_. Two years ago we were happy with Elasticsearch; now, we outgrew it for some of our use cases; who knows what the next tool we'll pass by is.

As we increase in size and scale, we are focusing more and more on performance and reliability testing on the datastores we use. We are starting to use tools like [AWS DynamoDB](https://aws.amazon.com/dynamodb/) for some secondary operations, and might test others ([RocksDB](https://rocksdb.org/)?) as we get new exciting use cases and features for our customers!

{% include asides/about-auth0.markdown %}
