---
layout: post
title:  "Introduction to Concurrent Segment Search in OpenSearch"
authors: 
  - reta
  - sohami
date:   2023-09-25
categories: 
  - search
categories:
  - technical-post
meta_keywords: concurrent segment search, search performance, search concurrency, OpenSearch 2.10
meta_description: In OpenSearch 2.10, you can now enable concurrent segment search experimental feature in your OpenSearch cluster. With concurrent segment search, you can search through shard segments parallely and improve search performance for your workload.
has_science_table: true
---

## Overview
OpenSearch is a scalable, flexible, and extensible open-source search and analytics engine. It is built on top of [Apache Lucene](https://lucene.apache.org) and uses its powerful capabilities to ingest user data and serve search requests in millisecond latencies. 

We have come across use cases where users demand better search performance for their workload. For such high demanding latency use cases, users are fine with scaling their clusters for better latency at the cost of adding more resources. There are other set of users who may already have available resources in their cluster and are still unable to improve the latency of their search request. Apache Lucene constantly improves the search algorithms and heuristics, and OpenSearch is trying hard to bring these improvements into its engine for users to benefit. Some of those are straightforward optimizations but others require significant redesign of the system, like concurrent segment search. Concurrent segment search helps to improve the search latency in a generic way which can provide a huge win for different user workloads.

## Background
Before digging into the concurrent search, let us briefly talk about how OpenSearch stores indices and handles the search requests. OpenSearch is a distributed system, usually deployed as a cluster of nodes. Each cluster has one or more data nodes where the indices are being stored. Each index is split into smaller chunks called shards which are scattered across data nodes. In turn, each shard is an Apache Lucene index, which is composed out of so-called segments, each holding a subset of indexed documents. 

