---
title: Two Papers Accepted -- 5th Workshop on ML for Systems at NeurIPS 2021
date: 2021-12-19
description: To celebrate this Xmas a bit earlier, I pushed two short papers out.
categories:
- PhD
- Paper
tags:
- PhD
- Paper
photos:
- https://zhiyuanyaoj.github.io/assets/images/X/PhD/paper/publication/neurips21.png
---

## Intro

The [35th Conference on Neural Information Processing Systems (NeurIPS)](https://neurips.cc/Conferences/2021/) -- an [A* ranking conference](http://portal.core.edu.au/conf-ranks/98/) on Machine Learning (ML) -- took place at ZoomVille in December this year, right before the Xmas holiday. It was a great place to meet and talk to people -- though virtually -- about our research. One of my greatest interests during my PhD is to explore ways to elegantly apply ML techniques on networking problems. Thanks to the [5th Workshop on ML for Systems](http://mlforsystems.org/) at the NeurIPS 2021, I have a chance to communicate with the greatest ML researchers and my dearest system guys about my findings. The schedule for the workshop in Neurips 2021 can be found [here](https://nips.cc/Conferences/2021/ScheduleMultitrack?event=21866).

## 1st Paper: [Towards Intelligent Load Balancing in Data Centers](https://arxiv.org/abs/2110.15788)

The first paper is about network load balancers. As an important component in data center networks, load balancers help provide scalable services. Workload distribution algorithms are typically based on heuristics, e.g., Equal-Cost Multi-Path (ECMP), Weighted-Cost Multi-Path (WCMP) or naive machine learning (ML) algorithms, e.g., ridge regression. Advanced ML-based approaches help achieve performance gain in different networking and system problems. However, it is challenging to apply ML algorithms on networking problems in real-life systems. It requires domain knowledge to collect features from low-latency, high-throughput, and scalable networking systems, which are dynamic and heterogeneous. 

Therefore, we propose Aquarius to bridge the gap between ML and networking systems and demonstrates its usage in the context of network load balancers. We demonstrate its ability of to conduct both offline data analysis and online model deployment in realistic systems. The results show that the ML model trained and deployed using Aquarius improves load balancing performance yet they also reveal more challenges to be resolved to apply ML for networking systems. For instance, supervised ML algorithms are hard to generalize and adapt to the dynamically changing networking environment.

![Poster](https://zhiyuanyaoj.github.io/assets/images/X/PhD/paper/2021/mlb-poster.png)

> The video presentation of this paper can be found [here](https://slideslive.com/38971323/towards-intelligent-load-balancing-in-data-centers?ref=folder-92053).
>

## 2nd Paper: [Reinforced Workload Distribution Fairness](https://arxiv.org/abs/2111.00008)

The second paper is also about network load balancers, but it focuses more on developing fair and adaptive workload distribution algorithms. As we said, when load balancers operate in dynamic environments with limited monitoring of application server loads, they rely on heuristic algorithms that require manual configurations for fairness and performance. To alleviate that, we propose a distributed asynchronous reinforcement learning mechanism to -- with no active load balancer state monitoring and limited network observations -- improve the fairness of the workload distribution achieved by a load balancer. 

The performance of the proposed mechanism is evaluated and compared with state-of-the-art load balancing algorithms in a simulator, under configurations with progressively increasing complexities. Preliminary results show promise in RL-based load balancing algorithms and identify additional challenges and future research directions, including reward function design and model scalability.

![Poster](https://zhiyuanyaoj.github.io/assets/images/X/PhD/paper/2021/rlb-poster.png)

> The video presentation of this paper can be found [here](https://slideslive.com/38971331/reinforced-workload-distribution-fairness?ref=folder-92053).
>

## Discussion & Conclusion

It was a great experience talking about science and engineering at the 35th NeurIPS. It was also a fun experience presenting my works in gather.town.

![Gather.Town](https://zhiyuanyaoj.github.io/assets/images/X/PhD/paper/2021/zoomville.png)

I'm currently working on the two follow-up papers -- yes, during Xmas, in Swiss, when I go skiing/snowboarding. Hopefully, we can see more results in the brief future.

Cheers! ;)