---
title: Elastic Sketch - Adaptive and Fast Network-wide Measurements
date: 2019-01-31
description: A paper a day keeps the doctor awake.
categories:
- a3d2kpt
tags:
- a3d2kpt
photos:
- /assets/images/X/PhD/a3d2kpt/p561-yang/fig1.png
---

* [Link2Paper](https://qmro.qmul.ac.uk/xmlui/bitstream/handle/123456789/43547/Uhlig%20Elastic%20Sketch%20Adaptive%202018%20Accepted.pdf?sequence=1)
* [Author: Yang et al.] 
* [Keywords: Network measurement]

# Motivation

Existing measurement solutions mainly focus on a good trade-off among accuracy, speed and memory usage. Only the state-of-the-art UnivMon \[1\] pays attention of generality (using one sketch to process many tasks), and of course make great trade-off among the 4 dimensions. But traffic characteristics (available bandwidth, packet arrival rate, flow size distribution) can vary drastically.

# Related Work

## UnivMon \[1\]

It makes good trade-off among accuracy, speed, memory usage, and generality. However, they do not focus on one fundamental need: achieving accurate network measurements no matter how traffic characteristics(available bandwidth, flow size distribution, and packet rate) vary.

# Contribution

## Problem

It is desirable to design an elastic data structure which can dynamically allocate appropriate memory size for elephant flows. It requries the sketch to be elastic: adaptive to bandwidth, packet rate and flow size distribution. Besides, three other requirements in measurements: generic, fast and accurate.

## Solution

### Elastic

![Compression Algorithm](/assets/images/X/PhD/a3d2kpt/p561-yang/fig2.png)

![Merging Algorithm](/assets/images/X/PhD/a3d2kpt/p561-yang/fig4.png)

1. Adaptive to bandwidth: compress and merge sketches
2. Adaptive to high packet rate: only record elephant flows
3. Adaptive to number of elephant flows: dynamically increase memory size for heavy flows

![Adaptivity to number of elephant flows](/assets/images/X/PhD/a3d2kpt/p561-yang/fig5.png)

### Generic

1. Measurement tasks: keep necessary info for each packet while discard IDs of mouse flows
2. Platforms: both hardware and software version proposed as well as P4 version.

## Implementation

Implement this on six platforms: P4, FPGA, GPU, CPU, multi-core CPU and OVS.

## Evaluation

### Traces

CAIDA4 \[2\] trace is used.

### Accuracy 

Evaluation metrics are:

- Average relative error (ARE) of estimated flow sizes (nubmer of packets)
- F1 score in terms of heavy hitter (flows whose sizes are larger than a threshold) and heavy change detection (flows whose sizes in 2 adjacent time windows increase or decrease beyond a threshold)
- Weighted mean relative error (WMRE) to estimate flow size distribution
- Relative error (RE) to estimate accuracy of entropy of flow sizes and cardinality estimations (number of flows)

![Accuracy](/assets/images/X/PhD/a3d2kpt/p561-yang/fig9.png)

### Memory and Bandwidth Usage

It is observed that for flow size estimation, the bandwidth usage of Elastic is always less than its memory usage. The reason that the bandwidth usage does not significantly outperform the memory usage is that Elastic itself has achieved extremely high accuracy and thus the compression algorithm cannot easily improve it further.

![Memory and Bandwidth Usage](/assets/images/X/PhD/a3d2kpt/p561-yang/fig11.png)

### Elasticity

![Elasticity](/assets/images/X/PhD/a3d2kpt/p561-yang/fig15.png)

### Processing Speed

![CPU Platform](/assets/images/X/PhD/a3d2kpt/p561-yang/fig17.png)
![Different Platforms](/assets/images/X/PhD/a3d2kpt/p561-yang/fig19.png)

# Ref

- \[1\] [One Sketch to Rule Them All: Rethinking Network Flow Monitoring with UnivMon](http://users.ece.cmu.edu/~vsekar/papers/sigcomm16_univmon.pdf)
- \[2\] [The CAIDA Anonymized Internet Traces](http://www.caida.org/ data/overview/)