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

<style>
table th:first-of-type {
    width: 100px;
}
</style>

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
| _N_ | Number of servers |
| _C_ | Size for segment routing list |
| _i_ | Application instance, range \[_0, N-1_\] |
| _j_ | Bucket, range \[_0, M-1_\]
| _p\[i\]_ | Pseudo-random permutation of {_0, 1, ..., M-1_} for application instance _i_ |
| _t_ | Lookup table {_0, ..., M-1_} ➡ {_0, ..., N-1_}$^C$, mapping each bucket to a list of _C_ application instances |
| _t\[j\]_ | An array of _C_ application instances, e.g. for bucket _j_, there will be _C_ application instances in its codomain in table _t_ |
| _nextIndex_ | This array lists the next index in each application's permutation _p\[i\]_ so as to indicate which bucket should each application instance take next time |
| _n_ | Counter for total assignments |
| _c_ | A tmp variable, denoting current bucket of interesting |
| _choice_ | A tmp variable, denoting the first avvailable position in the SR list |

![Consistent Hashing Algorithm](https://www.zhiyuanyao.com/assets/images/X/cisco-intern/6lb-hash-alg.png)

In this consistent hashing algorithm, there are two main sections, namely, **initialization** and **main loop**, which are introduced sequencially.

## Initialization

In terms of the demonstrated algorithm, four variables are initialized.

### $nextIndex$

$nextIndex$ is initialized as an array, with length of total number $N$ of application instance. The values inside are all set as $0$ so that for each application instance, its bucket of interest start from the first one in its permutation.

### $C$

As is mentioned above, $C$ represents the size for segment routing list. Here, $C = 2$ means that there will be two application instances in the segment routing list.

### $t$

Now that $C$ is set to $2$, we could now initialize our table $t$. It is initialized as a _M x C_ matrix, where _M_ represents the number of buckets, and _C_ stands for the size for SR list. All the values are initialized as $-1$, which means that no application instance has been assigned to this position. If none of the values in a row is $-1$, then this corresponding bucket is already full.

### $n$

Also as is mentioned above in the notation talbe, $n$ represents the total number of times that application is assigned to a bucket. Its value is initialized as $0$. And once $n = M \times C$, it indicates that the table $t$ is fully assigned.

## Main Loop

The main purpose for this algorithm is to balance the distribution of application instances into different buckets. There are two outer loops guarantee this to happen, namely, the tier-1 `while` loop and the tier-2 `for` loop. 

The `while` loop is used to traversal all application instances from time to time, meanwhile, it's used in case not all buckets are fully filled with application instances after traversal of all application instances. In the `for` loop, there are several condition-checking and loops, trying to find a vacant position in the buckets for an application instance. 

The logic in behind will be clearly demonstrated with an example below.

# Example

![An Example of Consistent Hashing Algorithm](https://www.zhiyuanyao.com/assets/images/X/cisco-intern/6lb-hash-eg.png)

# Ref

- \[1\] [6LB: Scalable and Application-Aware Load Balancing with Segment Routing](http://www.thomasclausen.net/wp-content/uploads/2018/02/2018-IEEE-Transactions-on-Networking-6LB-Scalable-and-Application-Aware-Load-Balancing-with-Segment-Routing.pdf)
- \[3\] [SRLB: The Power of Choices in Load Balancing with Segment Routing](http://www.thomasclausen.net/wp-content/uploads/2017/06/2017-ICDCS-SRLB-The-Power-of-Choices-in-Load-Balancing-with-Segment-Routing.pdf)
- \[2\] [Maglev: A fast and reliable software network load balancer](https://www.usenix.org/system/files/conference/nsdi16/nsdi16-paper-eisenbud.pdf)