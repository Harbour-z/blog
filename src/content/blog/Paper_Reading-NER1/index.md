---
title: "Paper Reading: NER -1"
author: Zhidong Zhang
description: 记录我读的一些NER任务相关论文
publishDate: 2025-02-20
# updatedDate: 2025-02-18
tags:
  - deep learning
  - paper reading
  - NER
draft: false
---

## Few-Shot Named Entity Recognition: A Comprehensive Study

http://arxiv.org/abs/2012.14978

这是一篇few-shot NER的综述。

PLMs即 Pretrained Language models。

文中提到

SOTA NER models are often initialized with PLM weights, fine-tuned with standard supervised learning. One classical approach is to add a linear classifier on top of representations provided by PLMs, and fine-tune the entire model with a cross-entropy objective on domain labels. 这也是为本文提供baseline的方法

遇到的问题是，尽管有很多的方法，但是NER构建还是一种labor-intensive 和 time-consuming的任务，需要大量的专家标注，但是现实世界的新领域的数据是很有限的。

![image-20250220111246595](./image-20250220111246595.png)



本文中提出的方法是：

1. create prototypes as the representations for different entity types, and assign labels via the nearest neighbor criterion
2. Continuously pre-train PLMs using web data with noisy labels that is available in much larger quantities to improve NER accuracy and robustness
3. employ unlabeled in-domain tokens to predict their soft labels using self-training, and perform semi-supervised learning in conjunction with the limited labeled data

这三个方法简单来说就是：Prototype-based methods, noisy supervised pre-training and self-training.

他们的贡献在于对少样本NER的系统研究，提出了三种方式，在十个公开的不同领域数据集上做了对比，达到SOTA。

![image-20250220152651321](./image-20250220152651321.png)















