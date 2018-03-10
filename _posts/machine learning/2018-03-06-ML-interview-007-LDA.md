---
layout: post
title: 机器学习面试007——主题模型
categories: [机器学习, 面试]
description: 面试总结
keywords: ML, LDA, pLSA
---

### 请大致对比一下pLSA和LDA的区别？
> **PLSA中 P(z\|d) 和 P(w\|z) 是确定的值**

<img src="/images/machine learning/007-LDA-01.png" width="80%" alt="gecko embed program run error 2" />
> **LDA是pLSA的贝叶斯版本。文档d产生主题z（其实是Dirichlet先验为文档d生成主题分布θ，然后根据主题θ参数主题z）**

<img src="/images/machine learning/007-LDA-02.png" width="80%" alt="gecko embed program run error 2" />

-------

### 简要说说EM算法
#### 1. 隐含变量
> 有时候样本的产生和隐含变量有关，而求解模型的参数一般采用最大似然估计，由于隐含变量的存在，导致似然函数的求导无法求解，故采用EM算法。

#### 2. E步骤
> 选取一组参数，求解在该参数下隐含变量的条件概率值

#### 3. M步骤
> 结合E步中求出的隐含变量条件概率，求出似然函数下界函数（本质上是某个期望函数）的最大值

> 重复以上两个步骤直至收敛

#### 4. 高斯混合模型GMM
> EM典型的例子，每个样本都可能由k个高斯产生，只不过每个高斯产生的概率不同。因此每个样本都有对应的高斯分布（k中的某一个）。**此时的隐含变量就是每个样本对应的某个高斯分布**
