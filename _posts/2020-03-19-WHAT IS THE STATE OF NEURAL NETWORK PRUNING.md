---
layout: post
title: '论文笔记：WHAT IS THE STATE OF NEURAL NETWORK PRUNING?'
subtitle: 'WHAT IS THE STATE OF NEURAL NETWORK PRUNING?'
date: 2020-03-19
categories: 模型压缩
cover: ''
tags: Pruning
---

## Summary

作者归纳总结了81篇过去的压缩算法，并进行了初步的同步比较，然后给出了无法进一步比较的原因。提出了[ShrunkBench](https://github.com/jjgo/shrinkbench)，方便未来的Pruning类文章进行比较，得出真正有效的Pruning算法。。提出应当指定的未来Pruning论文应当遵循的标准，如同时报告acc1和acc5，需要在大型数据集上（不止MNIST）和使用现代的网络（不能用AlexNet）等要求。

最后揭露了一些能够使压缩算法表现良好的Tricks，比如只比较一个指标、使用不同的初始权重值、只针对特定的网络模型等等

## Research Objective

作者的研究目标。是比较81篇论文中的Pruning方法，并提出了一个用于比较压缩方法的框架，在比较不同压缩方法方面做出了贡献。并提出了未来评判Pruning类论文的一些标准，减少了使用Trick发论文的可能。

## Problem Statement

Few papers compare to one another, and methodologies are so inconsistent between papers that we could not make these comparisons ourselves.  （很少有论文可以相互比较，而且论文之间的方法学如此不一致，以至于我们自己无法进行这些比较。）

例如，有四分之一的论文与其他修剪方法相比，有一半的论文与至多其他修剪方法相比，并且数十种方法从未与任何后续工作相比。 此外，即使在三分之一的论文中也没有出现数据集/网络对，评估指标差异很大，超参数和其他联合创始人（counfounders  ）也有所不同或不确定。

## Method(s)

作者采用的方法是论文复现，基于前人的方法，提供了Baseline，写了一套适合评估压缩算法进行对比的工具集。

## Evaluation

作者总结出图表（**Table 1**），归纳出<u>使用最多的数据集-网络对</u>，并在下面的测试中使用top4使用频率的Pair结对。**figure3**中不同时间的实验用不同颜色标出，然后做了Acc-压缩率的对比实验

作者总结出了论文给出的最优点的数量和论文使用的数据集-网络对的数目，提出大多数论文都使用了3个或以下的实验

**ss3** **figure1**通过绘制ImageNet上的压缩/加速图，对于不同的架构（MobileNet-v2、ResNet、VGG、EfficientNet），作者给出了几点结论。1）压缩有效性，2）修剪无法生成更好的结构，3）对于效率低的网络效果更好

 This lack of comparisons stems largely from a lackof experimental standardization and the resulting fragmen-tation in reported results. （缺乏比较的主要原因是缺乏实验的标准化以及报告结果的不完整。）

作者如何评估自己的方法，实验的setup是什么样的，有没有问题或者可以借鉴的地方。

**ss4**中归纳并提出1）以往Pruning论文缺少与其他论文比较 2）实验的数据集和网络结构不同 3）调节参数不同 4）不完全的归纳结果 5）不同的参数设置

**ss5**中，作者提出了两点无法进一步比较的原因 1）Architecture Ambiguity架构模糊 2）Metrics Ambiguity指标模糊

**ss6**总结和建议，提出了过去论文中的5点问题，并为了将来能够在方法之间进行直接比较，提出了9点未来论文需要做到的要求，包含需要同时报告acc1和acc5，需要在大型数据集上（不止MNIST）和使用现代的网络（不能用AlexNet）等要求。

在**ss7**中，作者提出了他们的比较框架，提供了“*标准化和可扩展的功能，用于训练，修剪，微调，计算指标和绘图，并使用一组标准化的预训练模型和数据集.ShrinkBench基于PyTorch*，旨在允许 具有任意评分功能的方法的简便评估，跨层修剪的分配以及稀疏结构。 特别是，给定一个回调定义如何在给定的迭代中为模型的参数张量计算掩码，ShrinkBench将自动应用修剪，根据标准训练或微调设置更新网络，并跨许多模型，数据集，随机计算度量 种子和修剪水平。”

提供了baseline的Pruning修建方法：

​	•全局幅度修剪-修剪网络中任何位置绝对值最低的权重。

​	•分层幅度修剪-对于每个层，修剪绝对值最低的权重。

​	•全局梯度幅度修剪-修剪绝对值最低的权重（权重×平均值），在一批输入上进行评估。

​	•分层梯度幅度修剪-对于每一层，修剪在一批输入上评估的最小绝对值（权重×梯度）的权重。

​	•随机修剪-以等于要修剪的网络部分的概率独立地修剪每个权重

identi-fied various pitfalls 提出了在文献中可能遇到的陷阱，并使用ShrinkBench可以避免

分为三个部分：1）不同指标是不同的，不能用一个来代表，**fig6**给出了验证2个指标加速比和压缩比时，修剪方式表示的性能不同，层权重修剪在加速比上优秀而在压缩比上不占优势， 2）结果与3点相关：数据集，模型，修剪量，不能仓促得出结论，**figure7**给出了一个不同模型相同修剪方法会有差异。3）使用相同的初始模型。**fig8**给出了两个不同初始权重的模型AB，发现在A上Global修剪和Layer修剪差不多，但是在B上Global就比Layer好很多。

## Conclusion

- suggest concrete remedies in the form of a list of best practices (列出了一系列pruning的最佳实践)(Strong)

- open-source library **ShrinkBench** (Strong)



## Notes

(optional) 不符合此框架，但需要额外记录的笔记。



## Reference

(optional) 列出相关性高的文献，以便之后可以继续track下去。