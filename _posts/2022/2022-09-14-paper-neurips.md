---
title: Learning Distributed and Fair Policies for Network Load Balancing as Markov Potentia Game
date: 2022-09-14
description: Throughout the whole summer vacation, I have been waiting for this moment. Finally! My collaboration with my friend Zihan, who is pursuing his PhD at Princeton University, is acknowledged and accepted in the main conference of the 36th Conference on Neural Information Processing Systems (NeurIPS). It's like a dream come true.
categories:
- PhD
- Paper
tags:
- PhD
- Paper
photos:
- https://zhiyuanyaoj.github.io/assets/images/X/PhD/paper/publication/nips22-accept.jpeg
---

## Intro

Last year (in 2021), [I have published two workshop papers in NeurIPS](https://zyao.xyz/phd/paper/2021/12/19/paper-neurips/). This year, tegether with [Zihan](https://quantumiracle.github.io/webpage/), I managed to publish a paper in the main conference in [NeurIPS 2022](https://neurips.cc/Conferences/2022).
As an [A* ranking conference](http://portal.core.edu.au/conf-ranks/98/) on Machine Learning (ML), it is not an easy task.
We spared no effort to present the best of our contributions -- both theoretical and experimental.
I am looking forward to meeting the greatest ML researchers in the world in NeurIPS 2022.

## The Paper: [Learning Distributed and Fair Policies for Network Load Balancing as Markov Potential Game](https://arxiv.org/abs/2206.01451)

![Design](https://zhiyuanyaoj.github.io/assets/images/X/PhD/paper/publication/marllb-neurips22.png)

This paper investigates the network load balancing problem in data centers (DCs) where multiple load balancers (LBs) are deployed, using the multi-agent reinforcement learning (MARL) framework. The challenges of this problem consist of the heterogeneous processing architecture and dynamic environments, as well as limited and partial observability of each LB agent in distributed networking systems, which can largely degrade the performance of in-production load balancing algorithms in real-world setups. Centralised training and distributed execution (CTDE) RL scheme has been proposed to improve MARL performance, yet it incurs -- especially in distributed networking systems, which prefer distributed and plug-and-play design schemes -- additional communication and management overhead among agents. We formulate the multi-agent load balancing problem as a Markov potential game, with a carefully and properly designed workload distribution fairness as the potential function. A fully distributed MARL algorithm is proposed to approximate the Nash equilibrium of the game. Experimental evaluations involve both an event-driven simulator and a real-world system, where the proposed MARL load balancing algorithm shows close-to-optimal performance in simulations and superior results over in-production LBs in the real-world system.

## Discussion & Conclusion

I would like to highlight specifically the great experience that I had during the rebuttal process, which is supported by the [OpenReview](https://openreview.net) platform. The reviewers have asked valuable questions and helped us improve the overall quality of our paper. Though sometimes it can be frustrating to correct some reviewers' misunderstanding and run huge amount of additional experiments to convince some experts that our evaluations are sound, I felt great when the reviewers finally ackowledge our contribution. 

Cheers! ;)