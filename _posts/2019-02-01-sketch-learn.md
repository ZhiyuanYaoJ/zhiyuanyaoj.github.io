---
title: SketchLearn - Relieving User Burdens in Approximate Measurement with Automated Statistical Inference
date: 2019-02-01
description: A paper a day keeps the doctor awake.
categories:
- a3d2kpt
tags:
- a3d2kpt
photos:
- /assets/images/X/PhD/a3d2kpt/sketch-learn/fig3.png
---

* [Link2Paper](https://www.cse.cuhk.edu.hk/~pclee/www/pubs/tech_sketchlearn.pdf)
* [Author: Qun Huang et al.] 
* [Keywords: Network Measurement, Sketch]

# Motivation

Network measurement is challenged to fulfill stringent resource requirements in the face of massive network traffic. While approximate measurement can trade accuracy for resource savings, it demands intensive manual efforts to configure the right resource-accuracy trade-offs in real deployment. Such user burdens are caused by how existing approximate measurement approaches inherently deal with resource conflicts when tracking massive network traffic with limited resources. In particular, they tightly couple resource configurations with accuracy parameters, so as to provision sufficient resources to bound the measurement errors. Such tight binding leads to several practical limitations: 

1. administrators need to specify tolerable error levels as input for resource configurations;
2. each configuration is tied to specific parameters (e.g., thresholds);
3. the theoretical analysis only provides worst-case analysis and fails to guide the resource configurations based on actual workloads;
4. each configuration is fixed for specific flow definitions;
5. administrators cannot readily quantify the extent of errors for measurement results.

# Contribution

## Problem

### Design Requirement

1. Small memory usage;
2. Fast per-packet processing;
3. Real-time response;
4. Generality.

### Limitations of Existing Approaches

<p align="center">
  <img src="/assets/images/X/PhD/a3d2kpt/sketch-learn/fig1.png" alt="Limitation">
</p>

1. Hard to specify expected errors (theoretical guarantees based on configurable parameters);
2. Performance degrades with different threshold (e.g. heavy hitter detection);
3. Hard to tune configurations;
4. Hard to re-define flowkeys (5-tuple, src-dst addr pairs, ...);
5. Hard to examine correctness.

## Solution

1. Multi-level Sketch (makes it possible to quantify hash collisions, bit-level flowkey distributions)

<p align="center">
  <img src="/assets/images/X/PhD/a3d2kpt/sketch-learn/fig4.png" alt="Multi-level sketch">
</p>

![Multi-level sketch](/assets/images/X/PhD/a3d2kpt/sketch-learn/fig4.png)

2. Model Inference (their assumptions require to separate large and small flows)

<p align="center">
  <img src="/assets/images/X/PhD/a3d2kpt/sketch-learn/fig5.png" alt="Model Inference">
</p>

<p align="center">
  <img src="/assets/images/X/PhD/a3d2kpt/sketch-learn/fig5.png" alt="Large flow extraction">
</p>

## Evaluation

### Testbed

OVS, P4. Simulator is used for larger scale test (eliminates network transfer overhead).

### Traces

CAIDA \[1\] and UN2 \[2\].

### Experiments

1. Memory usage

<p align="center">
  <img src="/assets/images/X/PhD/a3d2kpt/sketch-learn/fig7.png">
</p>

2. Per-packet processing
3. Testbed throughput
4. Simulator throughput
5. Inference time
6. Generality

<p align="center">
  <img src="/assets/images/X/PhD/a3d2kpt/sketch-learn/fig11.png">
</p>

7. Fitting theorems
8. Robust to small thresholds

<p align="center">
  <img src="/assets/images/X/PhD/a3d2kpt/sketch-learn/fig12.png">
</p>

9. Arbitrary field combinations
10. Attaching error measures
11. Network-wide coordination

<p align="center">
  <img src="/assets/images/X/PhD/a3d2kpt/sketch-learn/fig14.png">
</p>

## Source code

[Link to github repo](https://github.com/huangqundl/SketchLearn). Respect! :)

# Limitation

- Default parameters are limited
- Does the model inference algorithm converge, especially with larger number of rows?

# Ref

- \[1\] [Caida Anonymized Internet Traces 2016 Dataset. 2018.](http://www.caida.org/data/passive/passive_2016_dataset.xml)
- \[2\] [Network Traffic Characteristics of Data Centers in the Wild](https://www.microsoft.com/en-us/research/wp-content/uploads/2010/11/DC-Network-Characterization-imc2010.pdf)
