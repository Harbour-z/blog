---
title: "Paper Reading: NER -2"
author: Zhidong Zhang
description: 记录我读的一些NER任务相关论文
publishDate: 2025-03-02
#updatedDate: 2025-03-
tags:
  - deep learning
  - paper reading
  - NER
language: 中文
draft: true
#heroImage: {src: './cover.png' , color: '#9698C1'}
---

## SKD-NER: Continual Named Entity Recognition via Span-based Knowledge Distillation with Reinforcement Learning

> 这篇文章有点坑爹的一点就是文中提到了github的代码仓库，***进去一看只有个readme
>
> <img src="./image-20250302013229029.png" alt="image-20250302013229029" style="zoom:50%;" />

提出SKD-NER模型，是一种span-based+reinforcement learning strategies，和大多数CL-NER模型一样，也用到了知识蒸馏，本文加入了强化学习测策略去优化soft labeling和distillation losses

![image-20250302013412792](./image-20250302013412792.png)