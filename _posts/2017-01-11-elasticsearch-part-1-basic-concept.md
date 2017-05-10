---
layout: post
title: 'Elasticsearch: Part 1 - Basic concept'
date: '2017-01-11T17:26:00.000+07:00'
tags:
- Elasticsearch
thumbnail: https://3.bp.blogspot.com/-diH_MX6CB90/WHIQb7cJtoI/AAAAAAAAEJw/N41IVzPTawQSiRXHAbrOrIXNWaOjGtqlACLcB/s72-c/cluster-node-index.PNG
---

Many say that <a href="https://www.elastic.co/" rel="nofollow" target="_blank">Elasticsearch</a> is powerful and easy
to approach. I agree that it is powerful, but easy? I doubt that, at least to me. When I first met Elasticsearch, I
found that it has many terms and concepts, and query structure is way more complicated, with nested `query` and
`filter` and `term` things.

I don't have much time to research it, and my project is already implemented some base code to work with Elasticsearch,
so I just make some small changes, refer to base code. I don't feel good at that time, so now I decided to take a
serious research, and write down to post series to help me systematically understand Elasticsearch.

Reference:
- <a href="https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html" rel="nofollow" target="_blank">https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html</a>
- <a href="https://www.elastic.co/guide/en/elasticsearch/guide/current/index.html" rel="nofollow" target="_blank">https://www.elastic.co/guide/en/elasticsearch/guide/current/index.html</a>

All posts belong to this series:
- Part 1: Elasticsearch basic concept (this post)
- TBD

---

## Part 1: Elasticsearch basic concept

### Elasticsearch is Near Realtime
That means it isn't really realtime - it has a delay. This delay is between the time you index one piece of data, to
the time this data is ready to be searched.

### How data is stored in computer?
Elasticsearch stores its data in an sub-folder named `data`, in its root folder.  
Detail here: <a href="https://www.elastic.co/blog/found-dive-into-elasticsearch-storage" rel="nofollow" target="_blank">A Dive into the Elasticsearch Storage</a>  
Most of developer don't need to know where Elasticsearch stores data in the disk. I just list it here for reference.

### And how it organizes data logically?
The way Elasticsearch organize its data can be covered in these terms:
- Node: a single machine that run Elasticsearch, belong to a cluster
- Cluster: a group of nodes
- Document: data in JSON format, just like data row in SQL
- Type: documents can be categorized into types
- Index: a group of documents, belong to a cluster
- Shard: index is divided into shards, or just duplicated into shards

You may confuse between cluster, node and index. It is easier to see a pic:
![Cluster, Node and Index]({{ site.url }}/assets/images/cluster-node-index.png)
This picture describes one cluster, but in two point of view: node and index. You can see `node` and `index` are
separated concepts. By that, Index A can be divided into shards (but not necessary), and shards are spread among
nodes. That how `node` and `index` work.

Other concepts like document, type or shard are not that confuse, definition can be found here:
<a href="https://www.elastic.co/guide/en/elasticsearch/reference/current/_basic_concepts.html" rel="nofollow" target="_blank">Elasticsearch: Basic Concepts</a>

I will come back and update this post later.