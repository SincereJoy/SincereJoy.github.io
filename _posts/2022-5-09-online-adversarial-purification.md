---
title: [2021 ICLR] ONLINE ADVERSARIAL PURIFICATION BASED ON SELF-SUPERVISED LEARNING
key: 3
author: ZE
tags: 论文阅读，Adversarial purification
---

论文链接：https://openreview.net/forum?id=_i3ASPp12WS

<!--more-->

## 摘要

这篇论文提出了一种基于自监督学习的在线对抗性净化方法，SOAP。该方法利用自监督损失在测试阶段净化对抗样本，实现了与最先进的对抗训练和对抗性净化方法相当的鲁棒准确率，同时减少了训练的复杂度。即使面对已知SOAP防御策略的自适应的攻击者，SOAP依然保持鲁棒。

## 背景

目前最流行的对抗防御方法，比如对抗训练，计算开销很大。(问题）

最近的一系列研究表明[1-4]，自监督学习能够学习到更有意义的数据表示，从而有助于提高深度神经网络的鲁棒性。（可能的解决方案）

于是作者对自监督表示学习如何增强深度神经网络的鲁棒性做了研究，提出了SOAP，并通过充分的实验来说明SOAP的有效性和计算效率优势，最后通过自适应的攻击实验来进一步说明SOAP的强鲁棒性。

## Terminology

### 对抗训练

min-max problem：

先最大化损失生成对抗样本，然后用对抗样本训练，最小化对抗样本造成的损失。

### 对抗性净化

将对抗样本还原为干净样本。

### 自监督学习

自监督的学习旨在学习对无标签数据的中间表示，这些表示可以用于各种下游任务，其中该任务的监督来自数据本身。

## SOAP

### 问题描述

本文主要考虑针对分类任务的防御，但作者说他们的方法也可以扩展到其他任务上。作者把对抗性净化问题表示为一个带约束的优化问题：

$min_\pi L_{cls}((g \circ f)(x_{pfy}),y) 
\ \ \ \ \ \ \  s.t. ||x_{pfy}-x_{adv}|| \leq \epsilon_{adv}, x_{pfy} = \pi(x_{adv}) 
\ \ \ \ \ \ \  (1)$

其中$f$是特征提取器，$g$是分类器，$L_{cls}$是分类器的交叉熵损失，$x_{pfy}$是净化后的样本，$y$为干净样本对应的类别，$x_{adv}$是对抗样本，$\epsilon_{adv}$是对抗样本的扰动上限，$\pi$是净化算法。

公式（1）跟生成对抗样本的过程很相似，只不过对抗样本是在干净样本上添加微小扰动，使分类器将其分类为正确标签的loss最大化（untargeted攻击），而对抗净化是在对抗样本上添加微小扰动，使分类器将其分类为正确标签的loss最小化。
{:.info}

但上述公式是不可解的，因为在测试阶段我们无法获知对抗样本的真实标签y，也无法获知对抗样本生成时的扰动上限$\epsilon_{adv}$。

### 自监督在线净化

在训练时，将分类器$g$的loss $L_{cls}$跟一个自监督辅助模型的loss $L_{aux}$一起优化，相当于在正常训练的目标函数里添加一个正则化项。
$min_\theta {L_{cls}((g \circ f)(x;\theta_{enc};\theta_{cls}),y)+\alpha L_{aux}((h \circ f)(x;\theta_{enc};\theta_{cls}))} 
\ \ \ \ \ \ \  (2)$

测试阶段，用L_aux代替L_cls进行样本的净化（类似于对抗训练的公式）。

$min_\pi L_{aux}((h \circ f)(x_{pfy})) 
\ \ \ \ \ \ \  s.t. ||x_{pfy}-x_{adv}|| \leq \epsilon_{pfy}, x_{pfy} = \pi(x_{adv}) 
\ \ \ \ \ \ \  (3)$

公式（3）中的$\epsilon_{pfy}$是净化的界限

$\epsilon_{pfy}$是怎么得到的？
测试阶段，选取一系列的$\epsilon_{pfy}$值，选择其中对应辅助损失$L_{aux}$最小的$\epsilon_{pfy}$。
{:.info}

净化过程可以看作PGD算法的逆，PGD算法是一步步地向使分类loss L_cls增大地方向增大扰动，而这篇文章提出的净化算法也是一步步地增大扰动，不过是朝使辅助loss L_aux减小地方向调整。最终需要求解的优化问题如下：

$min_\pi L_{aux}((h \circ f)(x_{adv}+\delta)) 
\ \ \ \ \ \ \  s.t. ||\delta|| \leq \epsilon_{pfy}, x_{adv}+\delta \in [0,1] 
\ \ \ \ \ \ \  (4)$

优化过程采用多步的方式计算扰动：

$\delta_t = \delta_{t-1}+\gamma sign(\Delta_xL_{aux}((h \circ d)(x_{adv}+\delta_{t-1}))) \ \ \ \ \ \ \  (5)$

### 如何选取合适的自监督任务作为分类器的辅助？

自监督辅助任务需要具备以下几个特点：

1. 对噪声敏感

2. 对整个输入是可微的

3. 高效

4. 适用于原模型的数据集

作者选了数据重建，旋转预测和标签一致性判断三个任务

## 实验

## 思考

1. 减少了训练阶段的开销（相比于对抗训练），但增大了测试阶段的开销

2. 防御效果很大程度上取决于自监督辅助任务的选择

3. 没有在更大更符合实际应用场景的数据集上做实验

## References

[1] Dan Hendrycks, Mantas Mazeika, Saurav Kadavath, and Dawn Song. Using self-supervised learning can improve model robustness and uncertainty. In NeurIPS, 2019.

[2] Chengzhi Mao, Ziyuan Zhong, Junfeng Yang, Carl Vondrick, and Baishakhi Ray. Metric learning for adversarial robustness. In Advances in Neural Information Processing Systems, pp. 480–491, 2019.

[3] Tianlong Chen, Sijia Liu, Shiyu Chang, Yu Cheng, Lisa Amini, and Zhangyang Wang. Adversarial robustness: From self-supervised pre-training to fine-tuning. In Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition, pp. 699–708, 2020a.

[4] Muzammal Naseer, Salman Khan, Munawar Hayat, Fahad Shahbaz Khan, and Fatih Porikli. A selfsupervised approach for adversarial robustness. In Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition, pp. 262–271, 2020.