---
layout: post
title:  强化学习论文——DQN
categories: [paper, 强化学习]
description: 强化学习论文阅读
keywords: 强化学习，DQN
---

### 简介
最近在学习斯坦福2017年秋季学期的[《强化学习》课程](http://rll.berkeley.edu/deeprlcourse/)，感兴趣的同学可以follow一下，Sergey大神的，有英文字幕，语速有点快，适合有一些基础的入门生。
今天主要总结上午看的有关DQN的一篇论文《Human-level control through deep reinforcement learning》，在Atari 2600 games上用DQN网络训练的，训练结果明，DQN能够比较稳定的收敛到Human-level的游戏水平。

### 前言
目前，强化学习已经在现实中很多复杂的情形中取得小小胜利，尤其是在可人工构建有效特征、可全观测的低维状态空间等领域。当然也在一些任务场景中碰了不少的壁：智能体必须学会从高维的“感官”输入中，识别并“感知”外界环境的特征表示，并从过去的经验中，学习以及迁移适应新的环境。这对人和动物来说，是与生俱来的能力，是和我们层级的感知系统与自我强化学习的神秘结合有关。

#### 摘要
本文提出了一种Deep Q-Network（DQN），借助 **端到端(end-to-end)** 的强化学习方法能够直接从高维的输入中，学习一种很优的策略（policy）。输入是游戏的实时图像（当前`状态S`），借助卷积神经网络捕捉局部特征的关联性，输出所有可能采取`动作A`的概率分布。
引入了经验回放（Experience Replay）。

### 思路
#### Step1. Action-Value Function
DQN中，借助了深度神经网络来拟合**动作-值函数**，即折扣累计回报：

<a href="https://www.codecogs.com/eqnedit.php?latex=Q^{*}(s,a)=max_{\pi&space;}E[r_{t}&plus;\gamma&space;r_{t&plus;1}&plus;\gamma&space;^{2}r_{t&plus;2}&plus;...|s_{t}=s,&space;a_{t}=a,&space;\pi]" target="_blank"><img src="https://latex.codecogs.com/gif.latex?Q^{*}(s,a)=max_{\pi&space;}E[r_{t}&plus;\gamma&space;r_{t&plus;1}&plus;\gamma&space;^{2}r_{t&plus;2}&plus;...|s_{t}=s,&space;a_{t}=a,&space;\pi]" title="Q^{*}(s,a)=max_{\pi }E[r_{t}+\gamma r_{t+1}+\gamma ^{2}r_{t+2}+...|s_{t}=s, a_{t}=a, \pi]" /></a>
其中`π`是要采取的策略，即在观察到状态`s`时，按照策略采取动作`a`。
<a href="https://www.codecogs.com/eqnedit.php?latex=\pi&space;=&space;P(a|s)" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\pi&space;=&space;P(a|s)" title="\pi = P(a|s)" /></a>

**强化学习过程常常不稳定，而且训练时易于发散(diverge)，尤其当神经网络采用非线性函数逼近Q时**。主要有以下原因：
+ 在训练时，输入的观察序列（样本）之间具有关联性。比如后个序列样本，是紧着前一个样本的。
+ Q函数小小的更新可能给策略（policy）带来很大的波动（更新前后策略分布明显有别），并进一步改变数据分布（策略影响下一步动作选取）
+ 动作-值函数Q与目标值（target value）的关联性。目标值定义如下：

<a href="https://www.codecogs.com/eqnedit.php?latex=y&space;=&space;r&space;&plus;&space;\gamma&space;max_{a^{'}}Q(s^{'},&space;a^{'})" target="_blank"><img src="https://latex.codecogs.com/gif.latex?y&space;=&space;r&space;&plus;&space;\gamma&space;max_{a^{'}}Q(s^{'},&space;a^{'})" title="y = r + \gamma max_{a^{'}}Q(s^{'}, a^{'})" /></a>

#### Step2. 经验回放
借助深度网络来拟合Q函数这里就不做赘述了，详见下文的网络图。论文作者在模型训练中加入了 **经验回放（Expericen Replay）**，这里解释一下这个非常有用的概念（敲黑板~~）：
> 在训练过程中，会维护一个序列样本池Dt= {e1, ...., et}，其中et=(st, at, rt, st+1)，et就是在状态st下，采取了动作at，转移到了状态st+1，得到回报rt，这样就形成了一个样本（经验），一般样本池大小有限制（设为N）

> 回放的意思，就是在训练中，比如让agent玩游戏，并不是把样本按照时间顺序喂给网络，而是在一局游戏未结束之前，把生成的样本（经验）都更新地扔进经验池中，从池中平均采样minBatch个，作为训练样本


这样，通过回放，就可以减少上面提到的**因为前后样本存在关联导致的强化学习震荡和发散**问题。还有以下好处：
+ 保证了每个样本在权重更新中，都有足够的可能被利用多次，提高样本利用率
+ 直接从连续的样本学习会导致震荡问题，随机从样本池抽取，可以打乱这种关联性
+ 形象化解释是，当agent在上一个样本最后采取的动作是left时，在采样中，可以只从状态为left的样本中进行采样，保证训练的分布更具有有效性

#### step3. Double Q-network 迭代
因为在逼近Q函数时，由于目标值函数与下个状态的最优动作对应的Q函数有关，而动作选取又依赖于策略π的更新，因此二者相互关联。
在DQN中我们用网络拟合`Q(s, a; θ)`，其中θ是网络中权重参数，Q-learning的迭代更新使用如下的loss函数：

<a href="https://www.codecogs.com/eqnedit.php?latex=L_{i}(\Theta&space;_{i})=E_{(s,a,r,s^{'})\rightarrow&space;U(D)}[(r&plus;\gamma&space;max_{a^{'}}Q(s^{'},a^{'};\Theta&space;_{i}^{-})-Q(s,a,;\Theta&space;_{i}))^{2}]" target="_blank"><img src="https://latex.codecogs.com/gif.latex?L_{i}(\Theta&space;_{i})=E_{(s,a,r,s^{'})\rightarrow&space;U(D)}[(r&plus;\gamma&space;max_{a^{'}}Q(s^{'},a^{'};\Theta&space;_{i}^{-})-Q(s,a,;\Theta&space;_{i}))^{2}]" title="L_{i}(\Theta&space;\_{i})=E_{(s,a,r,s^{'})\rightarrow U(D)}[(r+\gamma&space;max_{a^{'}}Q(s^{'},a^{'};\Theta \_{i}^{-})-Q(s,a,;\Theta \_{i}))^{2}]" /></a>

其中`θi`是第 i 步Q-network参数；`θ-`是计算第i步的目标值（target value）。 **一般`θi`更新`C`步，`θ-`才更新一步。**

#### step4. 网络训练
如下图，输入是游戏的视频帧，通过3层卷积层，后接2层全连接层，最后输出当前状态（视频帧）在采取的所有动作的Q值函数。作者在论文中提到，此模型尽可能的少的进行先验假设。
<img src="/images/paper/DQN-01.png" width="80%" alt="gecko embed program run error 2" />

在训练过程中，采用贪心策略，即在网络输出得到的所有动作值函数Q时，并非以直接选取最大值对应的动作，而是采取`ξ-greedy policy`，即可能以很小的概率选取其他动作，以保证探索空间的多样性。在追踪平均每个episode的得分情况，可以看出Q函数能够稳定的收敛到一定值。
<img src="/images/paper/DQN-02.png" width="80%" alt="gecko embed program run error 2" />

### 小结
+ 在论文中，作者还提到DQN能够学习到相对长期的策略（提到在小霸王里消砖的那款游戏：agent可以通过强化学习学到，优先把一个角打通，然后就会在天花板里来回谈，以获得很高的回报）
+ 盛赞了一下提出的DQN网络以很少的先验知识，简单的网络，相同的模型算法，就能在多样的环境中（多款游戏），仅借助像素信息和游戏得分，得到human-level的agent。
+ **Replay算法**很好使，减少训练的震荡性
+ 独立的target value网络（其实就是复制了一下Q-network参数，延迟C step 进行更新）

### 算法细节
关于DQN详细的算法实现过程，请参考另一篇博客[《强化学习论文——简述DQN论文算法细节》](https://killersdeath.github.io//2018/03/12/reinforcement-learning-paper-DQN-algorithm/)
