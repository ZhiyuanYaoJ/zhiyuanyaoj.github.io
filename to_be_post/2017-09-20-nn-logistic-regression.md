---
title: Logistic Regression as a Neural Network
date: 2017-09-20 14:00:00
description: [Notes taken from deeplearning.ai @ Coursera]
categories:
- Deeplearning
tags:
- Deeplearning
photos:
---

## Binary Classification

Given an example of binary classification, for instance, tell if there is a cat in a photo:

![Classify a cat](https://ericyaoj.github.io/blog/assets/images/cat-classify.png)

Our input then is a \\(64 \times 64\\) image with three channels (RGB) as is shown below:

![RGB representation](https://ericyaoj.github.io/blog/assets/images/rgb_representation.png)

Thus, in total, there are \\( n_x = 64 \times 64 \times 3 = 12288 \\) digits as our input \\( x \in \mathbb{R}^{n_x} \\):

$$ x = \begin{bmatrix} 255\\ 231\\ \vdots\\ 255\\ 134\\ \vdots \\ 255\\ 134\\ \vdots \end{bmatrix} $$

and \\y \in \{0, 1\}\\) as our output.

Then we give the notifications as follows:

