---
title: TPAMI'20 - Imbalance Problems in Object Detection:A Review
key: 5
author: ZE
tags: 论文阅读 ObjectDetction
---

论文链接：https://arxiv.org/abs/1909.00169

Github: https://github.com/kemaloksuz/ObjectDetectionImbalance

<!--more-->

这篇论文主要对目标检测领域存在的**不平衡问题**进行了分类，并对解决这些不平衡问题的方法进行了全面的综述，在此基础上讨论了目前尚未解决的开放问题，另外还简要介绍了其它领域的不平衡问题。

这篇论文并不是对目标检测背景和发展过程的全面总结，可以参考其它survey[1,2,3]和[github仓库](https://github.com/hoya012/deep_learning_object_detection)

## Brief background
### top-dwon approach
直接对完整的物体进行检测
1. two-stage methods
   
   第一阶段：通过提案机制从预定义的anchor中选出最可能出现物体的区域，Region of Interests (RoIs)

   第二阶段：检测网络从RoIs中预测出bounding boxes和类别概率，最后通过非极大值抑制机制（non-maxima suppression, NMS）消除重复或高度重叠的结果。
   > NMS：计算所有bounding boxes的面积，按面积由大到小排列，计算其它bounding boxes跟面积最大的bounding box的IoU，抑制（忽略/去除）IoU大于设定阈值的bounding box
2. one-stage methods
   
   没有提案的过程，直接利用anchors进行目标检测。
   把图像中提取的特征和从预定义的anchors中跟ground truth重合的labeled anchors（bounding box）一起输入到目标检测网络（包括分类和定位）中进行训练。
   e.g. SSD, YOLO, RetinaNet

### bottom-up approach
先找出物体的重要组件，然后把它们组合成完整的物体。
e.g. CornerNet

## Taxonomy of imbalance problems
### Class imbalance 
不同类别的训练样本数有较大差异，训练集被一小部分类别支配
#### foreground-background
   一张图片中的大多数bounding boxes被识别为negative（背景），少数是有实际类别标签的前景

Solutions:
1. Hard sampling：计算损失时，给每个bounding box加权重w，w ∈ {0,1}
   
   e.g. Hard-example mining methods，从negative的bb中选出hard examples（loss高的bb）进行训练，即这些hard examples权重为1，而其它的背景bb权重为0
2. Soft sampling: 计算损失时，给每个bounding box加权重w，w ∈ [0,1]
   
   **YOLO**：所有背景bb的loss贡献权重都是0.5

   **Focal loss**： $w_i = (1-p_s)^\gamma$，hard examples会有更大的权重

   **Gradient Harmonizing Mechanism (GHM)**: easy examples are those with too many similar gradients. $w_i = \frac{1}{G(BB_i)/m}$, G(BBi) is the count of samples whose gradient norm is close to the gradient norm of BBi; and m is the number of input bounding boxes in the batch. 对分类和回归过程都有效

   **PrIme Sample Attention (PISA)**
3. Sampling-free methods: 
4. Generative methods: 用生成网络来直接生成hard examples
   e.g. Task Aware Data Synthesis
#### foreground-foreground
### Scale imbalance
同一类物体有不同的大小，不同大小的样本数差异
### Spatial imbalance
### Object imbalance
目标不平衡，有多个优化目标。e.g. classification and regression

## Reference
[1] L. Liu, W. Ouyang, X. Wang, P. W. Fieguth, J. Chen, X. Liu, and M. Pietikäinen, “Deep learning for generic object detection: A survey,” arXiv, vol. 1809.02165, 2018.

[2] Z. Zou, Z. Shi, Y. Guo, and J. Ye, “Object detection in 20 years: A survey,” arXiv, vol. 1905.05055, 2018.

[3] S. Agarwal, J. O. D. Terrail, and F. Jurie, “Recent advances in object detection in the age of deep convolutional neural networks,” arXiv, vol. 1809.03193, 2018.