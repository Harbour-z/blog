---
title: "Paper Reading: NER -1"
author: Zhidong Zhang
description: 记录我读的一些NER任务相关论文
publishDate: 2025-02-20
updatedDate: 2025-02-27
tags:
  - deep learning
  - paper reading
  - NER
draft: false
heroImage: {src: './cover.png' , color: '#9698C1'}
---

## Few-Shot Named Entity Recognition: A Comprehensive Study

文章地址在：http://arxiv.org/abs/2012.14978

文章中没有给出github代码库链接，搜了搜才找到：[few-shot-NER-benchmark/BaselineCode: The 3 baseline methods for few-shot NER tasks](https://github.com/few-shot-NER-benchmark/BaselineCode)

这是一篇few-shot NER的综述。PLMs即 Pretrained Language models。

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

这几种方法的backbone都是RoBERTa model，且本文在实验方面采用了若干种不同的组合方式：
![image-20250225004140376](./image-20250225004140376.png)

Conclusion：supervised pre-training and self-training turn out to be particularly effective

**关于代码部分**：

这里有个小坑，3.8的python环境是可以的，但是3.9会出问题。

---

## Continual Learning for Named Entity Recognition

这篇提出的是AddNER&ExtendNER模型。

在现实世界中，命名实体的类型（entity types）会经常新增，这就需要重新训练模型，但是如果重新标注原始数据或者数据集在经济性和安全性上都可能不太实际，这就引出了本文的主题**Continual Learning for NER**，也就是连续学习或者增量学习。

**灾难性遗忘（catastrophic forgetting）**对增量NER来说是很严重的问题，本文用的是知识蒸馏的方法。

出现新的实体类别就需要更新模型，因此需要新的数据，全量标注基本行不通，更常见的做法是：Annotate a new dataset only for new entity types。不过本文用的是self-training的方法，用旧模型标注新数据集，来教导学生模型学习。

基于的backbone是`BERT-based` models，datasets是`CoNLL-03`和`OntoNotes`，tags格式是IBO format，训练是在单卡V100上做的。

模型参数设置：batch size=32 lr=5e-5; 20 epochs with early stopping(patience=3), 学生模型 $$T_m=2$$，weighted sum of the losses $$\alpha = \beta = 1$$.

本文中教师和学生模型的组成：

* **encoder layers**, which produce an h-dimensional contextual representation for each token of a sentence
* **tagger layers**, which output probability distributions for each token with respect to the target labels

**AddNER Model**

![image-20250227010106406](./image-20250227010106406.png)

绿色层是新加的层，用来学习新的实体类型。需要注意的是，这里的绿色层在迭代过程中实际加了许多的输出层用来识别不同的实体类别：

1. if the model is iteratively extended, a new output layer is added at each iteration;
2. if m is the number of new entity types added in a given iteration, then the matrix of the corresponding output layer will have a dimensionality of h × (2m + 1)
3. 这里的2m+1因为每个类都有B-和I-，以及有一个O

这里在计算KD loss时，$$\alpha =1$$也就是全部依赖于学习模仿教师模型输出的soft labels。

AddNER由KD loss和CE loss联合训练，$$L^{Add}=\alpha L_{KL}^{Add}+\beta L_{CE}^{Add}$$。

模型的一个问题是对单个token的不同layer的输出需要做合并，毕竟有可能不同层对同一个token输出的结果是截然不同的，作者设置了一套规则和启发式算法来帮助完成这件事（下面几条是列举原文的）：

* If all layers predict the O tag, then output=O
* If exactly one layer predicts a B- tag and the other layers predict O, then output=B-.
* If multiple layers predict B- tags and the remaining layers predict O, then output=B- with the highest probability.
* If a layer predicts an I- tag, output=I- only if it matches the preceding tag in the sequence, i.e., the tag of the previous token must be a B- or I- of the same entity type. Otherwise, the output of that layer is treated as O, and the heuristics are applied again to determine the final output.



**ExtendNER Model**

![image-20250227010753326](./image-20250227010753326.png)

ExtendNER中教师和学生模型也是一致的，在识别新类的策略上，直接对Linear layer也就是tagger output layer直接进行维度的拓展；每新增一个类dimension增加2。损失函数的计算和上一个模型是一样的。

最终tags获取需动态调整输出维度；通过维特比算法（Viterbi）解码全局最优路径。



