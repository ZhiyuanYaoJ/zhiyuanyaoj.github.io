---
title: Aquarius - Enable Fast, Scalable, Data-Driven Service Management in the Cloud
date: 2022-09-10
description: A second journal publication, which contains lots of my efforts throughout my 3 years of PhD. The machine is ready to boost Machine-Learning-related studies in the networking community.
categories:
- PhD
- Paper
tags:
- PhD
- Paper
photos:
- https://zhiyuanyaoj.github.io/assets/images/X/PhD/paper/publication/aquarius-tnsm.png
---

## Intro

Extending based on the paper [Efficient Data-Driven Network Functions](https://zyao.xyz/phd/paper/2022/08/05/paper-mascots/) which I will physically present in the [30th International Symposium on the Modeling, Analysis, and Simulation of Computer and Telecommunication Systems](https://mascots.iitis.pl/) in Nice, France, we have developed a platform that enables to efficiently harness networking features and advanced machine learning (ML) algorithms in cloud networks to make informed and adaptive management and operational decisions.

The submission provides an in-depth analysis of the challenges of applying ML algorithms to real-world networking problems. Based on this analysis, a fast and scalable data collection and exploitation mechanism are proposed, specified, and implemented to enable learning algorithms to make inferences and open/close-loop control decisions based on fine-grained observations.

In addition to the contributions in our paper [Efficient Data-Driven Network Functions](https://zyao.xyz/phd/paper/2022/08/05/paper-mascots/), this manuscript proposes: 

- additional feature engineering analysis (PCA) and reasoning;
- an extensive benchmark of unsupervised clustering algorithms in the context of traffic classification;
- detailed offline training and benchmark of a wide range of supervised learning algorithms in the context of autoscaling systems;
- feature correlation and limitation analysis, and feature selection validation in real-world systems for reinforcement learning (RL) algorithm developed in a simulated environment in the context of Layer-4 load balancing problem;
- a high-level methodology blueprint for how to systematically approach the application of advanced learning algorithms on networking problems.

This work is accepted in proceedings of the ["Machine Learning and Artificial Intelligence for Managing Networks, Systems and Services" special issue](https://www.comsoc.org/publications/journals/ieee-tnsm/cfp/machine-learning-and-artificial-intelligence-managing-networks) in the [IEEE Transactions on Network and Service Management](https://ieeexplore.ieee.org/xpl/RecentIssue.jsp?punumber=4275028), which is a top-rank networking journal.
The pdf version of this paper is available [here](https://www.researchgate.net/profile/Zhiyuan_Yao13/publication/362600719_Aquarius_-Enable_Fast_Scalable_Data-Driven_Service_Management_in_the_Cloud/links/62fe6462e3c7de4c346664e6/Aquarius-Enable-Fast-Scalable-Data-Driven-Service-Management-in-the-Cloud.pdf).


## The Paper: [Aquarius - Enable Fast, Scalable, Data-Driven Service Management in the Cloud](https://ieeexplore.ieee.org/abstract/document/9852806)

In order to dynamically manage and update networking policies in cloud data centers, Virtual Network Functions (VNFs) use, and therefore actively collect, networking state information -and in the process, incur additional control signaling and management overhead, especially in larger data centers. In the meantime, VNFs in production prefer distributed and straightforward heuristics over advanced learning algorithms to avoid intractable additional processing latency under high-performance and low-latency networking constraints. This paper identifies the challenges of deploying learning algorithms in the context of cloud data centers, and proposes Aquarius to bridge the application of machine learning (ML) techniques on distributed systems and service management. Aquarius passively yet efficiently gathers reliable observations, and enables the use of ML techniques to collect, infer, and supply accurate networking state information -without incurring additional signaling and management overhead. It offers fine-grained and programmable visibility to distributed VNFs, and enables both open-and close-loop control over networking systems. This paper illustrates the use of Aquarius with a traffic classifier, an auto-scaling system, and a load balancer -and demonstrates the use of three different ML paradigms -unsupervised, supervised, and reinforcement learning, within Aquarius, for network state inference and service management. Testbed evaluations show that Aquarius suitably improves network state visibility and brings notable performance gains for various scenarios with low overhead.

Citation is preferred in the following format:
```
@article{yao2022aquarius,
  title={Aquarius-Enable Fast, Scalable, Data-Driven Service Management in the Cloud},
  author={Yao, Zhiyuan and Desmouceaux, Yoann and Cordero-Fuertes, Juan-Antonio and Townsley, Mark and Clausen, Thomas},
  journal={IEEE Transactions on Network and Service Management},
  year={2022},
  publisher={IEEE}
}
```