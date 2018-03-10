---
layout: post
title: 机器学习面试003——逻辑斯蒂回归
categories: [机器学习, 面试]
description: 面试总结
keywords: ML, LR, 逻辑斯蒂回归
---

<img src="/images/machine learning/003-LR-01.png" width="80%" alt="gecko embed program run error 2" />
<img src="/images/machine learning/003-LR-02.png" width="80%" alt="gecko embed program run error 2" />

### 1. LR为什么可以用来做CTR预估？
**Ans**：若把点击的样本作为正例，未点击的样本作为负例，则样本的CTR就是样本为正例的概率，LR可以输出样本为正例的概率，故可以解决此类问题。此外，LR相对于其他模型，**求解简单，可解释强，方便并行**

### 2. LR相对于线性回归有什么优势？
**Ans**：LR本质上还是线性回归，但多了最后一层sigmoid函数的非线性映射。正是这这种映射，解决了线性回归在整个实数域内敏感度一致的缺点。在分类任务中，需要控制在[0,1]之间。逻辑回归曲线在z=0处很敏感，在z>>0和z<<0处，都不敏感。
<img src="/images/machine learning/003-LR-03.png" width="80%" alt="gecko embed program run error 2" />

### 3. LR与最大熵模型MaxEnt有什么关系？
**Ans**：**本质上没有区别**。LR是最大熵对应类别为**二类**时的特殊情况，也就意味着，当逻辑回归类别扩展到多分类时，就是最大熵模型。

### 4. LR和SVM的区别和联系？
**Ans**：①LR和SVM都可以处理分类问题，且一般都用于处理线性二分类问题(改进后，可处理多分类)
②都可以增加不同的正则化项，如L1、L2，在很多实验中，算法结果很相近。(区别：LR是参数模型，SVM是非参数模型)
③从目标函数上看，LR采用logisticcal loss；SVM采用Hinge Loss。两个损失函数的目的都是增加对分类影响较大的数据点的权重，减少与分类关系较小的数据点的权重
④SVM仅考虑support vectors，去学习分类器。LR是通过非线性映射，大大减少了离分类平面较远的点的权重，相对提升了与分类最相关的数据点的权重
⑤LR的模型更加单，好理解，可解释性抢，适合并行。SVM的理解和优化比较复杂，但是可以支持核函数，进行高维映射。
⑥**LR能做的，SVM也能做，但可能在准确率上有问题；SVM能做的，LR有的做不了**
