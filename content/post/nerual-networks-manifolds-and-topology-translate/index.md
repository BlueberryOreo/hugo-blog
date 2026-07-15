---
title: （翻译）神经网络、流形和拓扑（编辑中）
description: 原文："Neural Networks, Manifolds, and Topology" from colah's blog
slug: NN-Manifolds-and-Topology
date: 2026-07-15 7:00:00+0012
image: https://colah.github.io/posts/2014-03-NN-Manifolds-Topology/img/example_network.svg
categories:
    - study
math: true
tags:
    - 翻译
    - 文章阅读
weight: 1       # You can add weight to some posts to override the default sorting (date descending)
---

## 神经网络、流形和拓扑

> 原文：[Neural Networks, Manifolds, and Topology](https://colah.github.io/posts/2014-03-NN-Manifolds-Topology/) 发布于2014年4月6日 【*关键词：拓扑、神经网络、深度学习、流形假设*】

最近深度神经网络在不少领域（例如计算机视觉）取得了重大突破，引发了广泛关注[1]。然而我们对于深度神经网络还有很多困惑，例如我们很难去理解深度神经网络*到底*在做什么。如果训练的很好，深度神经网络可以得到高质量的结果，但我们不知道为什么这样会好；同样的，如果训练的不好，我们也很难去找问题出在哪儿了。

不过相比于去整体理解高维深度神经网络的原理，从低维的、一层只有几个神经元的“深度”神经网络来思考会容易很多。实际上，我们可以通过可视化的方法完全理解深度神经网络的机理以及如何去训练这样的网络。基于这个角度，我们可以对神经网络的机理有更加深入的认知，同时我们还可以发现它和数学里面的“拓扑”有很大的联系。通过这些分析，我们可以看到很多有趣的发现，~包括一个神经网络可以分类的数据集分布的基本上限在哪里~。

### 一个简单的例子

我们先从这个最简单的数据集开始：平面上的两条曲线。一个网络需要去学习将这两条曲线上的点区分开来。

![平面上的两条曲线](https://colah.github.io/posts/2014-03-NN-Manifolds-Topology/img/simple2_data.png)

想要直观看到一个神经网络、或者说一个分类算法的分类能力，我们只需要去看它如何去分类任意一个数据点。我们先考虑一个最简单的神经网络，只有一个输入层和一个输出层（译者注：即 $y=Wx$ ）。这种网络仅会用一条直线将数据集分成两类。

![简单网络分类](https://colah.github.io/posts/2014-03-NN-Manifolds-Topology/img/simple2_linear.png)

这种网络没什么意思。现代神经网络通常在输入和输出之间还有至少一层的神经元，这些层被称为“隐藏层（hidden layers）”。

![维基百科上的神经网络示意图](https://colah.github.io/posts/2014-03-NN-Manifolds-Topology/img/example_network.svg)

用这样的神经网络，我们再进行一次分类的可视化，可以看到它将数据用一条更复杂的曲线分成了两类。

![带有隐藏层的神经网络的分类](https://colah.github.io/posts/2014-03-NN-Manifolds-Topology/img/simple2_0.png)

这种多层的神经网络会在每一层将数据进行变换，得到一个新的*表示（representation）*[2]。我们可以在每一个表示中观察网络是如何分类这些数据的。在得到了最终的表示之后，神经网络就会直接用一条直线（或者高维空间中的超直线）来区分这些数据。

在之前的可视化里面，我们观察的是数据的“原始”表示，可以认为这是我们从输入的角度来看这些数据是如何分布的。现在我们来看看经过第一个隐藏层变换后的表示，可以认为我们换了一个角度，从隐藏层输出的角度来看数据的分布。

每一维代表隐藏层的一个神经元的输出的结果。

![隐藏层学到的表示，这种表示下的数据可以被线性区分](https://colah.github.io/posts/2014-03-NN-Manifolds-Topology/img/simple2_1.png)

### 层的连续可视化

通过上一节的内容，我们学习了如何利用每一层得到的表示来理解网络在做什么。这样我们得到了一系列离散的表示。

一个难点是我们如何去理解一个表示如何变化为另一个表示，不过神经网络的性质可以很容易地解决这个问题。

神经网络中有大量不同种类的层。我们以 `tanh` 层作为具体的例子。一个 `tanh` 层 $\tanh(Wx+b)$ 包含：

- 线性变换矩阵 $W$（也被称为“权重矩阵”）
- 偏置项 $b$
- 逐元素计算的激活函数 $\tanh$

我们可以用一个连续的变换过程来表示：

![tanh变换过程](https://colah.github.io/posts/2014-03-NN-Manifolds-Topology/img/1layer.gif)

其他网络也差不多是这种方式，包含一个仿射变换和一个逐元素计算的单调激活函数。

我们可以将这种可视化方法用到更加复杂的网络上去。例如下面这个有四层隐藏层的神经网络，可以将两个稍微纠缠在一起的数据区分开来。随着变换的进行，我们可以看到网络将原始表示变换成了更高级的表示，用来分类数据。即使最开始这两个数据是螺旋形纠缠在一起的，最终它们同样能够被线性分割。

![成功的螺旋状数据的表示变换](https://colah.github.io/posts/2014-03-NN-Manifolds-Topology/img/spiral.1-2.2-2-2-2-2-2.gif)

而像下面这个同样有多层的网络，却没能将两个数据成功区分开来，反而让两个数据更加纠缠在一起。

![失败的螺旋状数据的表示变换](https://colah.github.io/posts/2014-03-NN-Manifolds-Topology/img/spiral.2.2-2-2-2-2-2-2.gif)

值得注意的是，这里的任务看起来很复杂主要是因为我们仅仅使用了低维的神经网络。如果我们使用更高维的神经网络，这些分类任务就会变得很容易处理。

*（Andrej Karpathy 做了一个很棒的 [demo](http://cs.stanford.edu/people/karpathy/convnetjs//demo/classify2d.html) 可以交互式地可视化探索神经网络的训练过程）*

### tanh 层的拓扑

每一层都会拉伸和压缩数据空间，但从来不会剪切、破坏或者折叠这个空间。直觉上我们可以看出它们保留了拓扑的性质。例如一个连续的集合在变换后仍然是连续的。

这种不会影响到拓扑的变换被称为*同胚（homeomorphisms）*。准确来讲，它们是连续的双射函数。

> **定理：** 具有 $N$ 个输入和 $N$ 个输出的层是同胚的，如果权重矩阵是非奇异的。（需要注意激活函数的定义域和值域）

**证明：** 我们一步步来考虑：

- 假设 $W$ 的行列式不为零。那么这就是一个双射线性方程，并且线性可逆。线性方程是连续的，所以乘 $W$ 是同胚的。
- 加上偏置是同胚的。
- $\tanh$（包括 sigmoid 和 softplus，但不包括 $\text{ReLU}$）是连续函数，拥有连续的逆函数。如果我们仔细考虑定义域和值域，它们可以是双射的。所以按元素做这些运算是同胚的。

因此，如果 $W$ 的行列式不为零，这个层就是同胚的。

这个结论在任意多个层复合在一起的时候也是成立的。

### 拓扑和分类

[1]: This seems to have really kicked off with Krizhevsky et al., (2012), who put together a lot of different pieces to achieve outstanding results. Since then there’s been a lot of other exciting work.

[2]: These representations, hopefully, make the data “nicer” for the network to classify. There has been a lot of work exploring representations recently. Perhaps the most fascinating has been in Natural Language Processing: the representations we learn of words, called word embeddings, have interesting properties. See Mikolov et al. (2013), Turian et al. (2010), and, Richard Socher’s work. To give you a quick flavor, there is a very nice visualization associated with the Turian paper.
