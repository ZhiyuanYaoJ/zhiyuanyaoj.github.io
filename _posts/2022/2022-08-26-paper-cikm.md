---
title: CIKM22 - Multi-Agent Reinforcement Learning for Network Load Balancing in Data Center
date: 2022-08-26
description: Glad to announce that my friend at Princeton University have pushed a paper on multi-agent reinforcement learning algorithms for load balancing problems to CIKM.
categories:
- PhD
- Paper
tags:
- PhD
- Paper
photos:
- https://zhiyuanyaoj.github.io/assets/images/X/PhD/paper/publication/cikm22.png
---


## Intro

Extending based on the paper ["Reinforced Workload Distribution Fairness"](https://arxiv.org/abs/2111.00008) which we have presented last year at the [5th Workshop on ML for Systems at NeurIPS 2021](https://zyao.xyz/phd/paper/2021/12/19/paper-neurips/), we have started investigating in multi-agent setup of load balancing problems in data center networks.
We have moved from an event-based simulator to a real-world networking testbed.
This work is currently available on [arXiv](https://arxiv.org/abs/2201.11727) and it has been accepted in the [31st ACM International Conference on Information and Knowledge Management (CIKM'22)](https://www.cikm2022.org).




## The Paper: [Multi-Agent Reinforcement Learning for Network Load Balancing in Data Center](https://arxiv.org/abs/2201.11727)

This paper presents the network load balancing problem, a challenging real-world task for multi-agent reinforcement learning (MARL) methods. Traditional heuristic solutions like Weighted-Cost Multi-Path (WCMP) and Local Shortest Queue (LSQ) are less flexible to the changing workload distributions and arrival rates, with a poor balance among multiple load balancers. The cooperative network load balancing task is formulated as a Dec-POMDP problem, which naturally induces the MARL methods. To bridge the reality gap for applying learning-based methods, all methods are directly trained and evaluated on an emulation system from moderate-to large-scale. Experiments on realistic testbeds show that the independent and "selfish" load balancing strategies are not necessarily the globally optimal ones, while the proposed MARL solution has a superior performance over different realistic settings. Additionally, the potential difficulties of MARL methods for network load balancing are analysed, which helps to draw the attention of the learning and network communities to such challenges.

Citation is preferred in the following format:
```
@inproceedings{yao2022multi,
  title={Multi-agent reinforcement learning for network load balancing in data center},
  author={Yao, Zhiyuan and Ding, Zihan and Clausen, Thomas},
  booktitle={31st ACM International Conference on Information and Knowledge Management (CIKM'22)},
  year={2022},
  doi={10.1145/3511808.3557133}
}
```