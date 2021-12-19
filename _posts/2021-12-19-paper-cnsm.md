---
title: Charon - Load-Aware Load-Balancing in P4
date: 2021-12-19
description: A paper published with my intern student Carmine at Cisco this summer.
categories:
- PhD
- Paper
tags:
- PhD
- Paper
photos:
---

## Intro

As a PhD student in Cisco, we have the opportunity to take intern students from École Polytechnique for research projects. During the summer in 2021, I took Carmine on board as one of my intern students, and we worked together on a hardware-related project. After four months, we managed to publish a short paper in proceedings of the [1st Workshop on Network Programmability with P4 (NetPA-TS2) at the IEEE 17th International Conference on Network and Service Management (CNSM)](https://ieeexplore.ieee.org/xpl/conhome/9615441/proceeding).


## The Paper: [Charon - Load-Aware Load-Balancing in P4 ](https://arxiv.org/abs/2110.14389)

Load-Balancers play an important role in data centers as they distribute network flows across application servers and guarantee per-connection consistency. It is hard however to make fair load balancing decisions so that all resources are efficiently occupied yet not overloaded. Tracking connection states allows load balancers to infer server load states and make informed decisions, but at the cost of additional memory space consumption. This makes it hard to implement on programmable hardware, which has constrained memory but offers line-rate performance. This paper presents Charon, a stateless load-aware load balancer that has line-rate performance implemented in P4-NetFPGA. Charon passively collects load states from application servers and employs the power-of-2-choices scheme to make load-aware load balancing decisions and improve resource utilization. Per-connection consistency is guaranteed statelessly by encoding server ID in a covert channel. The prototype design and implementation details are described in this paper. Simulation results show performance gains in terms of load distribution fairness, quality of service, throughput and processing latency.

Citation is prefered in the following format:
```
@inproceedings{rizzi2021charon,
  title={Charon: Load-Aware Load-Balancing in P4},
  author={Rizzi, Carmine and Yao, Zhiyuan and Desmouceaux, Yoann and Townsley, Mark and Clausen, Thomas},
  booktitle={2021 17th International Conference on Network and Service Management (CNSM)},
  pages={91--97},
  year={2021},
  organization={IEEE}
}
```

## Behind the Scene

When I proposed the project, I had a simple, "hardware-friendly" algorithm in my mind that can potentially help optimize the load balancing performance. That was when I started collaborating with Carmine, using the [P4-NetFPGA-SUME workflow](https://dl.acm.org/doi/10.1145/3289602.3293924).

During the first month, since Carmine was not familiar with load balancing problems, I sent him a list of related works (about 8-10 papers) and a survey draft to get him through some training of literature reviews. Throughout our discussions about different design choices of network load balancers, I found that he can be a great collaborator during brainstorming and can provide great ideas, e.g., by that time, nice categorization of design choices. 

During the second month, as he had enough background knowledge about the problem scope, I presented to him the algorithm that I would like to implement so that he could start thinking about the design taking into account hardware constraints. At the mean time, we started setting up P4-NetFPGA dev environment, which was not an easy task...

As it takes time to run simulations using Vivado tools, while he started implementing the basic load balancer functionalities in P4 with proper test scripts, I implemented an event-driven data center network simulator in Python to help investigate (i) how the proposed load balancing algorithm works, and (ii) how to design experiments to evaluate its performance.

After integrating the external module (in Verilog), our P4 load balancer program managed to pass the behavioral simulation but had no valid signal in the post-implementation simulation. We spent 2 weeks trying to figure out the issue, yet didn’t manage to resolve it in the end. And that was when we decided to put what we have achieved into a short workshop paper.

We will continue working on this project and fix the problem in the brief future.

## Discussion & Conclusion

I found tutoring and supervising interns a very interesting experience. I have had an honor to share my experience with [Josipovic Lana](https://scholar.google.ch/citations?user=AdaugN0AAAAJ&hl=en), who will be Carmine's PhD supervisor at ETHz. I wish and believe that Carmine will have a great PhD ahead!
