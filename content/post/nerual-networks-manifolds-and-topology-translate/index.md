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
    - 文章分享
    - 流形
    - 拓扑
    - 理论
weight: 1       # You can add weight to some posts to override the default sorting (date descending)
---

## 神经网络、流形和拓扑

> 原文：[Neural Networks, Manifolds, and Topology](https://colah.github.io/posts/2014-03-NN-Manifolds-Topology/) 发布于2014年4月6日 【*关键词：拓扑、神经网络、深度学习、流形假设*】

最近深度神经网络在不少领域（例如计算机视觉）取得了重大突破，引发了广泛关注[1]。然而我们对于深度神经网络还有很多困惑，例如我们很难去理解深度神经网络*到底*在做什么。如果训练的很好，深度神经网络可以得到高质量的结果，但我们不知道为什么这样会好；同样的，如果训练的不好，我们也很难去找问题出在哪儿了。

不过相比于去整体理解高维深度神经网络的原理，从低维的、一层只有几个神经元的“深度”神经网络来思考会容易很多。实际上，我们可以通过可视化的方法完全理解深度神经网络的机理以及如何去训练这样的网络。基于这个角度，我们可以对神经网络的机理有更加深入的认知，同时我们还可以发现它和数学里面的“拓扑”有很大的联系。通过这些分析，我们可以看到很多有趣的发现，~包括一个神经网络可以分类的数据集分布的基本上限在哪里~。

### 一个简单的例子

我们先从这个最简单的数据集开始：平面上的两条曲线。一个网络需要去学习将这两条曲线上的点区分开来。

<p align="center">
  <img src="https://colah.github.io/posts/2014-03-NN-Manifolds-Topology/img/simple2_data.png" alt="平面上的两条曲线" width="50%">
</p>

想要直观看到一个神经网络、或者说一个分类算法的分类能力，我们只需要去看它如何去分类任意一个数据点。我们先考虑一个最简单的神经网络，只有一个输入层和一个输出层（译者注：即 $y=Wx$ ）。这种网络仅会用一条直线将数据集分成两类。

<p align="center">
  <img src="https://colah.github.io/posts/2014-03-NN-Manifolds-Topology/img/simple2_linear.png" alt="简单网络分类" width="50%">
</p>

这种网络没什么意思。现代神经网络通常在输入和输出之间还有至少一层的神经元，这些层被称为“隐藏层（hidden layers）”。

<p align="center">
  <img src="https://colah.github.io/posts/2014-03-NN-Manifolds-Topology/img/example_network.svg" alt="维基百科上的神经网络示意图" width="50%">
</p>

用这样的神经网络，我们再进行一次分类的可视化，可以看到它将数据用一条更复杂的曲线分成了两类。

<p align="center">
  <img src="https://colah.github.io/posts/2014-03-NN-Manifolds-Topology/img/simple2_0.png" alt="带有隐藏层的神经网络的分类" width="50%">
</p>

这种多层的神经网络会在每一层将数据进行变换，得到一个新的*表示（representation）*[2]。我们可以在每一个表示中观察网络是如何分类这些数据的。在得到了最终的表示之后，神经网络就会直接用一条直线（或者高维空间中的超直线）来区分这些数据。

在之前的可视化里面，我们观察的是数据的“原始”表示，可以认为这是我们从输入的角度来看这些数据是如何分布的。现在我们来看看经过第一个隐藏层变换后的表示，可以认为我们换了一个角度，从隐藏层输出的角度来看数据的分布。

每一维代表隐藏层的一个神经元的输出的结果。

<p align="center">
  <img src="https://colah.github.io/posts/2014-03-NN-Manifolds-Topology/img/simple2_1.png" alt="隐藏层学到的表示，这种表示下的数据可以被线性区分" width="50%"><br>
  <sub>隐藏层学到的表示，这种表示下的数据可以被线性区分</sub>
</p>

### 层的连续可视化

通过上一节的内容，我们学习了如何利用每一层得到的表示来理解网络在做什么。这样我们得到了一系列离散的表示。

一个难点是我们如何去理解一个表示如何变化为另一个表示，不过神经网络的性质可以很容易地解决这个问题。

神经网络中有大量不同种类的层。我们以 `tanh` 层作为具体的例子。一个 `tanh` 层 $\tanh(Wx+b)$ 包含：

- 线性变换矩阵 $W$（也被称为“权重矩阵”）
- 偏置项 $b$
- 逐元素计算的激活函数 $\tanh$

我们可以用一个连续的变换过程来表示：

<p align="center">
  <img src="https://colah.github.io/posts/2014-03-NN-Manifolds-Topology/img/1layer.gif" alt="tanh变换过程" width="50%">
</p>

其他网络也差不多是这种方式，包含一个仿射变换和一个逐元素计算的单调激活函数。

我们可以将这种可视化方法用到更加复杂的网络上去。例如下面这个有四层隐藏层的神经网络，可以将两个稍微纠缠在一起的数据区分开来。随着变换的进行，我们可以看到网络将原始表示变换成了更高级的表示，用来分类数据。即使最开始这两个数据是螺旋形纠缠在一起的，最终它们同样能够被线性分割。

<p align="center">
  <img src="https://colah.github.io/posts/2014-03-NN-Manifolds-Topology/img/spiral.1-2.2-2-2-2-2-2.gif" alt="成功的螺旋状数据的表示变换" width="50%">
</p>

而像下面这个同样有多层的网络，却没能将两个数据成功区分开来，反而让两个数据更加纠缠在一起。

<p align="center">
  <img src="https://colah.github.io/posts/2014-03-NN-Manifolds-Topology/img/spiral.2.2-2-2-2-2-2-2.gif" alt="失败的螺旋状数据的表示变换" width="50%">
</p>

值得注意的是，这里的任务看起来很复杂主要是因为我们仅仅使用了低维的神经网络。如果我们使用更高维的神经网络，这些分类任务就会变得很容易处理。

*（Andrej Karpathy 做了一个很棒的 [demo](http://cs.stanford.edu/people/karpathy/convnetjs//demo/classify2d.html) 可以交互式地可视化探索神经网络的训练过程）*

### tanh 层的拓扑

每一层都会拉伸和压缩数据空间，但从来不会剪切、破坏或者折叠这个空间。直觉上我们可以看出它们保留了拓扑的性质。例如一个连续的集合在变换后仍然是连续的。

这种不会影响到拓扑的变换被称为*同胚（homeomorphisms）*。准确来讲，它们是连续的双射函数。

> **定理：** 具有 $N$ 个输入和 $N$ 个输出的层是同胚的，如果权重矩阵是非奇异的。（需要注意激活函数的定义域和值域）

**证明：** 我们一步步来考虑：

- 假设 $W$ 的行列式不为 0。那么这就是一个双射线性方程，并且线性可逆。线性方程是连续的，所以乘 $W$ 是同胚的。
- 加上偏置是同胚的。
- $\tanh$（包括 sigmoid 和 softplus，但不包括 $\text{ReLU}$）是连续函数，拥有连续的逆函数。如果我们仔细考虑定义域和值域，它们可以是双射的。所以按元素做这些运算是同胚的。

因此，如果 $W$ 的行列式不为 0，这个层就是同胚的。$\Box$

这个结论在任意多个层复合在一起的时候也是成立的。

### 拓扑和分类

考虑一组包含两类的二维数据 $A, B\in\mathbb{R}^2$：

<img
  src="https://colah.github.io/posts/2014-03-NN-Manifolds-Topology/img/topology_base.png"
  alt="红色的点为类别A，蓝色的点为类别B"
  width="300"
  style="float: right; margin: 0 0 16px 20px;"
/>

$$
A = \{ x\mid d(x, 0) < \frac{1}{3}\}\\
B = \{ x\mid \frac{2}{3} < d(x, 0) < 1\}
$$

**断言：** 隐藏层每层神经元数量小于 3 的神经网络无法分类这样的数据，不论网络有多深。

上文提到，使用 sigmoid 或者 softmax 构建网络的分类方式等同于尝试在最终的表示中寻找一个超平面（或像这里的一条直线）来将两类数据划分开来。如果只有两个隐藏神经元，神经网络在拓扑层面是无法将这种数据区分开来的。

在下面的可视化例子中，我们来观察训练过程中的一个网络的隐藏表示，以及划分的线。我们可以看到，不论网络怎么尝试变换隐藏表示，划分线始终无法将两类数据进行正确地划分。

<p align="center">
  <img src="https://colah.github.io/posts/2014-03-NN-Manifolds-Topology/img/topology_2D-2D_train.gif" alt="简单网络失败样例" width="50%"><br>
  <sub>这个网络无法将这两类数据正确分类</sub>
</p>

最终表示被压缩到了一个近乎无意义的局部空间内。不过即便这样，这个网络也能够达到 80% 左右的分类准确率。

这个例子只有一个隐藏层，但更多的隐藏层也同样无法处理这种数据。

**证明：** 一个层或者是一同胚的，或者其权重矩阵的行列式为 0。如果一个层是同胚的，那么 $A$ 始终环绕着 $B$，因此一条直线无法将它们划分开来。如果我们假设权重矩阵的行列式为 0，那么数据就会坍缩到某些维度上。由于我们处理的是一个与原始 $A$ 围绕着 $B$ 的数据集同胚的对象，坍缩到任何维度意味着 $A$ 和 $B$ 中的一些点会混合到一起，这样我们更无法将它们区分开来。$\Box$

如果我们增加一维，那问题就简单了。神经网络可以学习到这样的表示：

<p align="center">
    <img src="https://colah.github.io/posts/2014-03-NN-Manifolds-Topology/img/topology_3d.png" alt="三维中的表示" width="50%">
</p>

在这样的表示下，我们可以通过一个超平面来区分这个数据集。

为了更好地理解到底发生了什么，我们可以考虑一组更简单的一维数据：

<img
  src="https://colah.github.io/posts/2014-03-NN-Manifolds-Topology/img/topology_1d.png"
  alt="一维数据样例"
  width="300"
  style="float: right; margin: 0 0 16px 20px;"
/>

$$
A = [-\frac{1}{3}, \frac{1}{3}] \\[1em]
B = [-1, -\frac{2}{3}] \cup [\frac{2}{3}, 1]
$$

如果我们不用有两个及以上的神经元的隐藏层，我们无法将这组数据进行分类。但如果我们只用一个有两个神经元的隐藏层，我们可以学习到一个完美的弧线形的表示，使得我们用一条直线可以将两类数据划分开来：

<p align="center">
    <img src="https://colah.github.io/posts/2014-03-NN-Manifolds-Topology/img/topology_1D-2D_train.gif" alt="一维数据划分" width="50%">
</p>

发生了什么？一个神经元会在 $x>-\frac{1}{2}$ 的时候被激活，另一个会在 $x>\frac{1}{2}$ 的时候被激活。如果只有第一个神经元被激活，说明我们得到的数据点是属于 $A$ 类的。

### 流形假设（The Manifold Hypothesis）

真实世界中的数据集，例如图像数据，是否也是类似的情况？如果你认真对待流形假设，我认为这是值得考虑的。

流形假设认为自然数据集在嵌入空间中组成低维流形，有理论[3]和实验[4]可以说明这一点。如果你认为这是正确的，那分类算法的任务实际上就是将一组纠缠在一起的流形。

我们可以看前面的那个例子，一类被完全包围在另一类里面，这就是一组纠缠在一起的流形。不过一般来说狗的图像的流形也不一定就是像这种样子完全被猫的图像的流形所“包围”。在下一节中我们可以看到一些拓扑结构，它们虽然看起来很合理，但会引起一些问题。

[1]: This seems to have really kicked off with Krizhevsky et al., (2012), who put together a lot of different pieces to achieve outstanding results. Since then there’s been a lot of other exciting work.

[2]: These representations, hopefully, make the data “nicer” for the network to classify. There has been a lot of work exploring representations recently. Perhaps the most fascinating has been in Natural Language Processing: the representations we learn of words, called word embeddings, have interesting properties. See Mikolov et al. (2013), Turian et al. (2010), and, Richard Socher’s work. To give you a quick flavor, there is a very nice visualization associated with the Turian paper.

[3]: A lot of the natural transformations you might want to perform on an image, like translating or scaling an object in it, or changing the lighting, would form continuous curves in image space if you performed them continuously.

[4]: Carlsson et al. found that local patches of images form a klein bottle.
