---
layout: post
title: Scaling
category:
- Computer Science series
---

Scaling horizontally: Adding more and more machines/nodes

Scaling vertically: Upgrade abilities of existing machines/nodes

## Load balancer

Load balancing helps in horizontal scaling, by distributing the requests to nodes. Benefits:

- Share works evenly among all nodes, so nodes will have similar load.
- Availability: if one node fails, others can take care of the request.

### Maintain [consistency among nodes](https://www.lecloud.net/post/7295452622/scalability-for-dummies-part-1-clones)

For any request, all nodes behind the load balancer should be able to process and response same content.

One method is making stateless node.

But for stateful (like session):

- Load balancer should distribute the sessioned request to the node that contains the session.
- Or store the state data in an external source (a separate db or cache). Then all nodes can get the session
from that source.

### Load balancing the load balancer

There is case when the load balancer is fail, or under heavy load.  
We can use multiple load balancers.

#### Approach: DNS

Here we use DNS server (as a LB) to distribute traffic among real LBs.  
It helps the client to choose which LB to send real requests to.  
Note that DNS server is actually load balancing the DNS request, not the real request. Real requests are
still routed to real LBs.

Also, there are 2 options:

- Let client decide which LB to use
  - Here DNS server responses a list of A records to all LBs
  - [Decision to choose which LB is totally on client](https://webmasters.stackexchange.com/questions/10927)
  - Client can randomly pick one, and retry to next one if failure
  - Cons: if the LB that client picked is fail, it has to retry to next one.
- DNS server decide which LB to use
  - DNS server has mechanism to check which LBs are healthy
  - DNS server only response A record to those healthy LBs
  - Pros: all LBs that client can know are recently healthy, no fail interrupted time in client

#### Approach: Floating IP

[Simple diagram here](https://blog.digitalocean.com/floating-ips-start-architecting-your-applications-for-high-availability/)

Here we have 2 LBs: active-passive. The public IP points to the active LB.

Internally, they are both watching each other using heart beat mechanism.  
When the active LB is down (stop sending heart beat), the passive LB knows and decides to takes over
the floating IP:

- It broadcasting ARP request (Tell all nodes to map that IP to MAC of the passive LB)
- The floating IP manager and all node update their ARP cache
- The floating IP now points to the passive LB

References:

- [Multiple load balancers](https://www.quora.com/High-Availability-How-clustering-multiple-load-balancers)

## Async workers

### Background work

### Message Queue

## Database scaling