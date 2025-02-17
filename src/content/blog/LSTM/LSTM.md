---
title: LSTM学习纪录
description: 这是我在学习LSTM这个神经网络时的一些笔记
tags:
  - deep learning
language: 中文
author: Zhidong Zhang
publishDate: 2025-02-18
---

## LSTM 简单介绍

LSTM即长短期记忆网络（Long Short-Term Memory），它是RNN的一种改进型结构，专门设计用于解决传统RNN在处理长序列数据遇到的梯度消失/爆炸问题。

LSTM 通过引入**门控机制**（Gating Mechanism）来控制信息的流动，从而能够更好地捕捉长距离依赖关系。这里的门控机制包括：输入门、遗忘门、输出门。

## 原理学习

有一篇博客讲解LSTM原理很清晰：[colah.github.io/posts/2015-08-Understanding-LSTMs/](https:colah.github.io/posts/2015-08-Understanding-LSTMs/)

### 核心组件

1. 遗忘门
2. 输入门
3. 单元状态更新
4. 输出门

## LSTM与RNN的关系

在初学的时候有看到说法是LSTM是RNN的一种，但是这两个又经常并列来讲。简单而言，二者的关系是：

* RNN
  * 结构：简单循环单元（无门控机制）
  * 短时记忆，依赖最近几步输入
  * 短时记忆，依赖最近几步输入
  * 简单序列任务（如短文本分类）
* LSTM
  * 复杂门控机制
  * 长时记忆，可捕捉长距离依赖（改进之一）
  * 显著缓解梯度消失问题
  * 复杂序列任务

LSTM的变体有：

* **GRU（Gated Recurrent Unit）**：简化版 LSTM，合并输入门和遗忘门为“更新门”，参数更少。
* **Bi-LSTM**：双向 LSTM，同时捕捉前向和后向的上下文信息
* **Attention 机制**：与注意力结合，进一步提升对长序列的建模能力

## Pytorch代码实现
