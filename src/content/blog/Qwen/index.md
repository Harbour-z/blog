---
title: "QWen通义千问系列模型上手体验"
author: Zhidong Zhang
description: 由于项目需要研究了Qwen系列模型的调用方法
publishDate: 2025-03-26
# updatedDate: 2025-03-02
tags:
  - deep learning
  - Qwen
  - Debug
language: 中文
draft: false
heroImage: {src: './cover.png' , color: '#9698C1'}
---

## Qwen系列模型介绍

Qwen也即通义千问，是由阿里研究的系列开源模型，这是国内最强的开源模型，在全世界甚至都很有名气。

## 两个模型初上手体验

最近需要做一个项目，我初步入门研究了下[Qwen2.5系列的文档]((https://qwen.readthedocs.io/zh-cn/latest/index.html))，并尝试跑通了 `Qwen2.5-7B-VL-Instruct`和 `Qwen2-7B-audio`两个模型，并在A100显卡上跑通了代码。

接下来的工作是利用vllm、ollama等工具进行部署，这种部署的好处是持久化加载模型权重进内存，否则在测试的时候我每次运行代码都会重新载入一遍模型，载入时间甚至要超过推理时间。（当然我猜肯定有API可以实现持久化加载，不过我目前还不会）第二个好处就是可以进行多线程加速。

## 遇到的bug与解决方案

1. 有个比较坑的点在于调用`Qwen2.5-VL`的时候如果用了pip安装的qwen_vl_utils包，大概率会遇到一个函数参数获取错误的报错，升级为0.0.10就可以解决了。

2. [flash-attention](https://github.com/Dao-AILab/flash-attention))是这个模型支持的一个比较重要的机制，但是一直安装不上去显示CUDA版本没有大于11.7，但是我的CUDA版本是12.4，不知道哪里出错了，拉下来其Github的代码进行本地安装也遇到了同样的错误。于是我怒翻`setup.py`安装代码(bushi)，发现了报错位置相关的代码：

   ```python
   if CUDA_HOME is not None:
           if bare_metal_version >= Version("11.8") and "90" in cuda_archs():
               cc_flag.append("-gencode")
               cc_flag.append("arch=compute_90,code=sm_90")
           if bare_metal_version >= Version("12.8") and "100" in cuda_archs():
               cc_flag.append("-gencode")
               cc_flag.append("arch=compute_100,code=sm_100")
           if bare_metal_version >= Version("12.8") and "120" in cuda_archs():
               cc_flag.append("-gencode")
               cc_flag.append("arch=compute_120,code=sm_120")
   ```

​		于是定位到了CUDA_HOME这个变量上，查阅了相关资料，然后通过Linux中环境变量的设置定位到了其指向 `/usr` 这个路径，进去一看发现这里的版本是10.1，谜题解开了！由于CUDA环境变量的错误指向导致了代码安装的错误判断，因此在我自己用户下的`.bashrc`中修改了环境变量到`/usr/local/CUDA12.4` 后问题得到了解决。





