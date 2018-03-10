---
layout: post
title: 机器学习面试001——支持向量机SVM
categories: [机器学习, 面试]
description: 面试总结
keywords: ML, SVM, 支持向量机
---

<img src="/images/machine learning/001-SVM-01.png" width="80%" alt="gecko embed program run error 2" />
<img src="/images/machine learning/001-SVM-02.png" width="80%" alt="gecko embed program run error 2" />

### 1. 关于min和max交换位置满足的 d* <= p* 的条件并不是KKT条件

**Ans**：这里并非是KKT条件，要让等号成立需要满足strong duality(强对偶)，之后有学者在强对偶下提出了KKT条件。KKT条件成立需要满足constraint qualifications，而constraint qualifications之一就是Slater条件——即：凸优化问题，如果存在一个点x，使得所有等式约束都成立(即取严格不等号，不包括等号)，则满足Slater条件。SVM中此处，满足Slater条件，等号可以成立

### 2. 核函数是从高维空间构造超平面，是否会带来高维计算代价的问题？

**Ans**：并不会。在线性不可分的情况下，SVM首先在低维空间中完成计算，然后通过核函数将输入空间映射到高维特征空间，最终在高纬度空间中构造出最优分离超平面。

### 3. 高斯核函数在方差参数δ上选取有什么影响？

**Ans**：如果δ选的很大，高次特征上的权重会衰减的非常快，此时相当于一个低维度的子空间；如果δ选的很小，则可以将任意的数据映射为线性可烦，但可能带来非常严重的过拟合问题。

### 4. 核函数的本质是什么？

**Ans**：①解决线性不可分问题 ②在低维上先进行计算，将实质的分类效果在高维上呈现，巧妙地避免了高维计算复杂性的问题。

### 5. 在目标函数中，拉格朗日的参数α的取值有什么特点？
<img src="/images/machine learning/001-SVM-03.png" width="80%" alt="gecko embed program run error 2" />

**Ans**：对于远离平面的点为0；在边缘线的值在 [0, 1/N]之间；对于outlier数据的值为1/N
