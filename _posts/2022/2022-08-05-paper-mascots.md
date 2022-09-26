---
title: MASCOTS 2022 - Efficient Data-Driven Network Functions
date: 2022-08-05
description: Our networking feature collection and exploitation platform built on top of VPP will be presented in MASCOTS 2022 in Nice, France.
categories:
- PhD
- Paper
tags:
- PhD
- Paper
photos:
- https://zhiyuanyaoj.github.io/assets/images/X/PhD/paper/publication/aquarius-mascots.png
---

## Intro

Extending based on the paper [Towards Intelligent Load Balancing in Data Centers](https://arxiv.org/abs/2110.15788) which we have presented last year at the [5th Workshop on ML for Systems at NeurIPS 2021](https://zyao.xyz/phd/paper/2021/12/19/paper-neurips/), we have created a framework that allows for developing and deploying data-driven policies to optimize network functions in data center networks.
It serves as a toolbox that bridges the machine learning community and networking community.
This work is currently available on [arXiv](https://arxiv.org/abs/2208.11385) and it has been accepted in the [30th International Symposium on the Modeling, Analysis, and Simulation of Computer and Telecommunication Systems](https://mascots.iitis.pl/).

MASCOTS has long been evaluated as a [A-level conference](http://portal.core.edu.au/conf-ranks/1771/). It has received tons of positive feedback from various aspects, which rarely happens unless there's a wonderful community behind it.
I will take this chance and physically attend this conference in Nice, France, during October 18-20, 2022. Let me know if there'd be a chance to meet up and have some discussions! Looking forward to it!

## The Paper: [Efficient Data-Driven Network Functions](https://arxiv.org/abs/2208.11385)

Cloud environments require dynamic and adaptive networking policies. It is preferred to use heuristics over advanced learning algorithms in Virtual Network Functions (VNFs) in production because of high-performance constraints. This paper proposes Aquarius to passively yet efficiently gather observations and enable the use of machine learning to collect, infer, and supply accurate networking state information – without incurring additional signaling and management overhead. This paper illustrates the use of Aquarius with a traffic classifier, an auto-scaling system, and a load balancer – and demonstrates the use of three different machine learning paradigms – unsupervised, supervised, and reinforcement learning, within Aquarius, for inferring network state. Testbed evaluations show that Aquarius increases network state visibility and brings notable performance gains with low overhead.

Citation is preferred in the following format:
```
@inproceedings{yao2022efficient,
  title={Efficient Data-Driven Network Functions},
  author={Yao, Zhiyuan and Desmouceaux, Yoann and Fuertes, Juan Antonio Cordero and Townsley, Mark and Clausen, Thomas Heide},
  booktitle={30th International Symposium on the Modeling, Analysis, and Simulation of Computer and Telecommunication Systems (MASCOTS 2022)},
  year={2022}
}
```