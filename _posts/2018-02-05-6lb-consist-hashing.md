---
title: 6LB - Consistent Hashing 
date: 2018-02-05
description: Introduction and basic analysis of consistent hashing algorithm used in 6LB. [Stay tuned, more to come in this post :)]
categories:
- Algorithm
tags:
- Algorithm
photos:
- 
---

# Intro

_Stay tuned, more to come in this post :)_

**6LB** is a load-balancing approach that is application server load aware, yet is both application and application-layer protocol independent and does not rely on centralized monitoring or transmission of application state. It is authored by Yoann Desmouceaux, et al., under supervision of Mark Townsley and Thomas Clausen. This blog is posted to consolidate my understanding of the consistent hashing algorithm used in _6LB_. 

Consistent hashing is used, for instance, in _Maglev_ ([Ref \[2\]](https://www.usenix.org/system/files/conference/nsdi16/nsdi16-paper-eisenbud.pdf)), which proposes an algorithm mapping an incoming flow to one application instance. And a new consistent hashing algorithm which generalizes the one from _Maglev_ is introduced in _6LB_.

To be clear, _6LB_ uses 2 choices of application instances. However, the algorithm presented is agnostic to this value, and is therefore presented for lists of `C` instances.

# Algorithm

Before presenting the algorithm, notations used through the algorithm are summarized below in the table.

| Notation | Description |
| --- | --- |
| _M_ | Number of buckets |
| _N_ | Number of Servers |
| _i_ | Application instance \\(i \in \left{ 0, \dots, N-1 \right} \\) |


![Consistent Hashing Algorithm](https://www.zhiyuanyao.com/assets/images/X/cisco-intern/6lb-hash-alg.png)



# Example

![An Example of CHA](https://www.zhiyuanyao.com/assets/images/X/cisco-intern/6lb-hash-eg.png)

# Ref

- \[1\] 6LB: Scalable and Application-Aware Load Balancing with Segment Routing (currently unavailable, maybe later :) )
- \[2\] [Maglev: A fast and reliable software network load balancer](https://www.usenix.org/system/files/conference/nsdi16/nsdi16-paper-eisenbud.pdf)