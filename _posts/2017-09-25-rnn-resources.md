---
title: Deep Learning Resources
date: 2017-09-25 14:00:00
description: Resources gathering for learning deep learning, including papers in relative fields, references to APIs, etc.
categories:
- Deeplearning
- RNN
tags:
- Deeplearning
- RNN
photos:
---

## Hyperparameters

### Learning Rate

- Learning Rate Decay
    + [Exponential Decay function in tensorflow](https://www.tensorflow.org/api_docs/python/tf/train/exponential_decay)
- Adaptive Learning Rate Optimizers
    + [AdamOptimizer](https://www.tensorflow.org/api_docs/python/tf/train/AdamOptimizer)
    + [AdagradOptimizer](https://www.tensorflow.org/api_docs/python/tf/train/AdagradOptimizer)

### Minibatch Size

- [Systematic evaluation of CNN advances on the ImageNet](https://arxiv.org/abs/1606.02228), Author: Dmytro Mishkin, Nikolay Sergievskiy, Jiri Matas

### Epoch

- Early Stopping
    + Recently, TensorFlow has abandoned Monitor function, but uses [SessionRunHooks](https://www.tensorflow.org/api_docs/python/tf/train/SessionRunHook) instead. SessionRunHook is a part of tf.train, which has been developing. It will be the most important API to implement Early Stopping.
    + [Reference to API - Train Hooks](https://www.tensorflow.org/api_guides/python/train#Training_Hooks)

### Number of Hidden Units / Layers

Typically, 3-layer NNs performs better than 2-layer NNs in practice. But deeper NNs (i.e. 4-, 5-, 6-layer NNs) don't help a lot, which is for sure contrary to CNNs. People have found that the depth is one of the most important components for CNNs. And the deeper the CNNs are, the better they perfrom as recognision systems. ([Reference: cs231n - Andrej Karpathy](https://cs231n.github.io/neural-networks-1/))

### Others 

- [Practical recommendations for gradient-based training of deep architectures](https://arxiv.org/abs/1206.5533), Author: Yoshua Bengio
- [Deep Learning - 11.4 - Choosing Hyperparameters](http://www.deeplearningbook.org/contents/guidelines.html), Author: Ian Goodfellow、Yoshua Bengio、Aaron Courville
- [Neural Networks and Deep Learning - Chap.3 - How to choose a neural network's hyper-parameters](http://neuralnetworksanddeeplearning.com/chap3.html#how_to_choose_a_neural_network's_hyper-parameters), Author: Michael Nielsen
- [Efficient BackProp](http://yann.lecun.com/exdb/publis/pdf/lecun-98b.pdf), Author: Yann Lecun
- [How to Generate a Good Word Embedding?](https://arxiv.org/abs/1507.05523), Author: Siwei Lai, Kang Liu, Liheng Xu, Jun Zhao
- [Systematic evaluation of CNN advances on the ImageNet](https://arxiv.org/abs/1606.02228), Author: Dmytro Mishkin、Nikolay Sergievskiy、Jiri Matas
- [Visualizing and Understanding Recurrent Networks](https://arxiv.org/abs/1506.02078), Author: Andrej Karpathy、Justin Johnson、Li Fei-Fei

## RNN

### LSTM vs. GRU

> The evaluation clearly demonstrated the superiority of the gated units; both the LSTM unit and GRU, over the traditional tanh unit. This was more evident with the more challenging task of raw speech signal modeling. However, we could not make concrete conclusion on which of the two gating units was better.

[Empirical Evaluation of Gated Recurrent Neural Networks on Sequence Modeling](https://arxiv.org/abs/1412.3555), Author: Junyoung Chung, Caglar Gulcehre, KyungHyun Cho, Yoshua Bengio

> The GRU outperformed the LSTM on all tasks with the exception of language modelling.

[An Empirical Exploration of Recurrent Network Architectures](http://proceedings.mlr.press/v37/jozefowicz15.pdf)

> Our consistent finding is that depth of at least two is beneficial. However, between two and three layers our results are mixed. Additionally, the results are mixed between the LSTM and the GRU, but both significantly outperform the RNN.

[Visualizing and Understanding Recurrent Networks](https://arxiv.org/abs/1506.02078), Author: Andrej Karpathy, Justin Johnson, Li Fei-Fei

> Which of these variants is best? Do the differences matter? [Greff, et al. (2015)](https://arxiv.org/pdf/1503.04069.pdf) do a nice comparison of popular variants, finding that they’re all about the same. [Jozefowicz, et al. (2015)](http://proceedings.mlr.press/v37/jozefowicz15.pdf) tested more than ten thousand RNN architectures, finding some that worked better than LSTMs on certain tasks.

[Understanding LSTM Networks](https://colah.github.io/posts/2015-08-Understanding-LSTMs/), Author: Chris Olah

| Application | Cell | Num. of Layers | Size | Vocabulary | Embedding Size | Learning Rate | Reference |
| ---:| ---:| ---:| ---:| ---:| ---:| ---:| ---:|
| Large Vocabulary Speech Recognition | LSTM | 5, 7 | 600, 1000 | 82K, 500K | - | - | [paper](https://arxiv.org/abs/1610.09975) |
| Speech Recognition | LSTM | 1, 3, 5 | 250 | - | - | 0.001 | [paper](https://arxiv.org/abs/1303.5778) |
| seq2seq | LSTM | 4 | 1000 | Origin: 160K, Target: 80K | 1000 | - | [paper](https://arxiv.org/abs/1409.3215) |
| Image Caption Generator | LSTM | - | 512 | - | 512 | (fixed) | [paper](https://arxiv.org/abs/1411.4555) |
| Image Generation | LSTM | - | 256, 400, 800 | - | - | - | [paper](https://arxiv.org/abs/1502.04623) |
| Question Answering | LSTM | 2 | 500 | - | 300 | - | [paper](http://www.aclweb.org/anthology/P15-2116) |
| Text Summarization (seq2seq) | GRU | - | 200 | Origin: 119K, Target: 68K | 100 | 0.001 | [paper](https://pdfs.semanticscholar.org/3fbc/45152f20403266b02c4c2adab26fb367522d.pdf) |








