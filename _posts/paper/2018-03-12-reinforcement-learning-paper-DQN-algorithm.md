---
layout: post
title:  强化学习论文——简述DQN论文算法细节
categories: [paper, 强化学习]
description: 强化学习论文阅读
keywords: 强化学习，DQN
---

### DQN算法
有关DQN的经典论文解读，请移步[《强化学习论文——DQN》](/2018-03-12-reinforcement-learning-paper-DQN.md)。
本文主要记录一下《Human-level control through deep reinforcement learning》论文中，算法实现的细节，供小伙伴们交流。论文提供的代码是用`lua`写的，[代码传送门](https://sites.google.com/a/deepmind.com/dqn/)

### 预处理
论文是基于Atari 2600视频帧图像数据，210×160像素，128色，不作处理的话，对计算内存要求太高。
**首先**，取前后两帧图像的最大值。因为有些闪烁的像素只出现在偶数帧，不出现在奇数帧
**然后**，从RGB数据中提取亮度作为Y通道值，并将图像缩放到84×84
作者最后也采用最相邻的`m（取值为4）帧`图像，进行堆叠（stack），生成最后的输入图像。（堆叠？？相邻m帧取平均？）
### 构建模型
#### 输入
网络输入是84×84×4的经过预处理后的图像
#### 卷积层和全连接层
<img src="/images/paper/DQN-01.png" width="80%" alt="gecko embed program run error 2" />

+ 第一层卷积—— 32 filters of 8×8， 步长4，Relu 激活
+ 第二层卷积——64 filters of 4×4，步长2，Relu 激活
+ 第三层卷积——64 filters of 3×3，步长1，Relu 激活
+ 全连接层——512隐藏层单元，Relu 几乎
+ 输出层——与action的数目有关系

### 训练细节
需要提到的第一点，在训练过程中，作者clipping了rewards。具体操作是：正向回报clip到1，负向回报clip到-1，0表示无回报。
**为什么要clip？**（黑人问号脸o(╯□╰)o）
> 以这种方式处理回报值，可以限制误差传导的幅度，更容易保证在不同的游戏之间保持相同的学习率。同时，不clip的话会影响agent的性能，因为不同的量级回报会导致求导问题（不可导？？）

实验中采用了RMSProb的梯度优化方法，mini-batch设置为32。在选取贪心策略的参数时，在前1M帧从1.到0.1递减，之后保持0.1不变。

同时，在每个episode训练时，采用k-th跳步法，即每隔k个帧进行样本选择，这样在相同的时间里，可以训练k次。

#### loss function
**最优动作-值函数遵循一个重要的条件：贝尔曼等式**
> 对于状态 s’ 的所有可能动作 a’ ，Q*(s’, a’)是最优值，则最优的策略是最大化r+γQ*(s’, a’)

<a href="https://www.codecogs.com/eqnedit.php?latex=Q^{\*}(s,a)=E_{s^{'}}[r&plus;\gamma&space;max_{a^{'}}Q^{\*}(s^{'},a^{'})|s,a]" target="\_blank"><img src="https://latex.codecogs.com/gif.latex?Q^{\*}(s,a)=E_{s^{'}}[r&plus;\gamma&space;max_{a^{'}}Q^{\*}(s^{'},a^{'})|s,a]" title="Q^{\*}(s,a)=E_{s^{'}}[r+\gamma max_{a^{'}}Q^{\*}(s^{'},a^{'})|s,a]" /></a>

因此借助贝尔曼进行Q的迭代更新：

<a href="https://www.codecogs.com/eqnedit.php?latex=Q_{i&plus;1}(s,a)=E_{s^{'}}[r&plus;\gamma&space;max_{a^{'}}Q_{i}(s^{'},a^{'})|s,a]" target="\_blank"><img src="https://latex.codecogs.com/gif.latex?Q_{i&plus;1}(s,a)=E_{s^{'}}[r&plus;\gamma&space;max_{a^{'}}Q_{i}(s^{'},a^{'})|s,a]" title="Q_{i+1}(s,a)=E_{s^{'}}[r+\gamma max_{a^{'}}Q_{i}(s^{'},a^{'})|s,a]" /></a>

但实际上，这种方式并不可行。因为动作-值函数是对每个序列进行独立评估的，并未涉及任何生成过程。因此，更常用函数逼近方法来估计动作-值函数，比如线性函数逼近，或者借助神经网络进行非线性函数逼近。

<a href="https://www.codecogs.com/eqnedit.php?latex=Q(s,a;&space;\Theta&space;)\approx&space;Q^{\*}(s,&space;a)" target="\_blank"><img src="https://latex.codecogs.com/gif.latex?Q(s,a;&space;\Theta&space;)\approx&space;Q^{\*}(s,&space;a)" title="Q(s,a; \Theta )\approx Q^{\*}(s, a)" /></a>

在迭代过程中计算均方差：

<a href="https://www.codecogs.com/eqnedit.php?latex=L_{i}(\Theta&space;_{i})=E_{s,a,r}[(E_{s^{'}}[y|s,a]-Q(s,a;\Theta&space;_{i}))^{2}]" target="_blank"><img src="https://latex.codecogs.com/gif.latex?L_{i}(\Theta&space;_{i})=E_{s,a,r}[(E_{s^{'}}[y|s,a]-Q(s,a;\Theta&space;_{i}))^{2}]" title="L_{i}(\Theta _{i})=E_{s,a,r}[(E_{s^{'}}[y|s,a]-Q(s,a;\Theta _{i}))^{2}]" /></a>

进一步约化为：

<a href="https://www.codecogs.com/eqnedit.php?latex=L_{i}(\Theta&space;_{i})=E_{s,a,r}[(y-Q(s,a;\Theta&space;_{i})^{2}]&plus;E_{s,a,r}[V_{s^{'}}[y]]" target="_blank"><img src="https://latex.codecogs.com/gif.latex?L_{i}(\Theta&space;_{i})=E_{s,a,r}[(y-Q(s,a;\Theta&space;_{i})^{2}]&plus;E_{s,a,r}[V_{s^{'}}[y]]" title="L_{i}(\Theta _{i})=E_{s,a,r}[(y-Q(s,a;\Theta _{i})^{2}]+E_{s,a,r}[V_{s^{'}}[y]]" /></a>

在监督学习中，目标值在训练过程中是确定的。但是在这里，目标值依赖于网络权重，在每一步的梯度优化中，我们固定先前迭代的参数`θi-`，去优化Loss函数。上式中的最后一项，是目标值方差，一般常忽略不作处理（不依赖与θi）。
对loss函数求导：

<a href="https://www.codecogs.com/eqnedit.php?latex=\bigtriangledown&space;_{\Theta&space;_{i}}L(\Theta&space;_{i})=E_{s,a,r,s_{'}}[(r&plus;\gamma&space;max_{a_{'}}Q(s_{'},a_{'};\Theta&space;_{i}^{-}))\bigtriangledown&space;_{\Theta&space;_{i}}Q(s,a;\Theta&space;_{i})]" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\bigtriangledown&space;_{\Theta&space;_{i}}L(\Theta&space;_{i})=E_{s,a,r,s_{'}}[(r&plus;\gamma&space;max_{a_{'}}Q(s_{'},a_{'};\Theta&space;_{i}^{-}))\bigtriangledown&space;_{\Theta&space;_{i}}Q(s,a;\Theta&space;_{i})]" title="\bigtriangledown _{\Theta _{i}}L(\Theta _{i})=E_{s,a,r,s_{'}}[(r+\gamma max_{a_{'}}Q(s_{'},a_{'};\Theta _{i}^{-}))\bigtriangledown _{\Theta _{i}}Q(s,a;\Theta _{i})]" /></a>

### 训练过程
采用经验回放的Deep Q-learning算法。训练过程如下：
<img src="/images/paper/DQN-03.png" width="80%" alt="gecko embed program run error 2" />

### 总结
+ **model-free**——算法直接使用模拟器的样本解决强化学习任务，并没有显式的估计回报和transition dynamics P(r, s’|s, a)
+ **off-policy**——算法学习的是贪心策略 a=argmaxQ(s,a’;θ)，按照行为分布来确保状态空间的足够性探索
