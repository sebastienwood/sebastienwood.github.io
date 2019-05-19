---
title: "A compendium of tricks in Deep Learnings"
categories: tricks
date:   2019-05-19 11:30:19 -0400
---

Sometime you wish to had all the tricks for Deep Learnings summarized at one place. I hope to gather them all slowly but steadily in this post. Everything is accessible with the navigation bar on the right.

# The Compendium of Tricks

## X-D Convolutions
(ops)

I assume the idea of 2D convolution is quite well known. It however is generalizable to any dimension superior or equal to 1. It means the filter is scaled to the XD convolution. 1D convolution can be used in the context of eg. time series. 3D convolution can be used for 3D space. You see where it goes.

## Grouped Convolutions

## 1x1 Convolution
(bottleneck)

A 1x1 convolution is a special kind of 3D convolution. It means the receptive field is $1\times 1\times k$ where $k$ is the depth of the receptive field. It helps to synthetize across the feature maps to keep only the relevant features.

## Dilated Convolutions
(ops)

Traditionnal convolutions have parameters stride, filter size, padding. Dilation is an extra parameters that control the spreading in the receptive field. Allow to grab feature that are less localized (since we spread the receptive field). Doesn't add problematic complexity to the convolution operation (since the receptive field stays constant).

## Separable Convolutions
(ops, efficiency)

Simple and wide spread idea of using two 1D filters to represent one 2D filter. Eg. for a $3\times 3$ filter, you could use a $3\times 1$ filter followed by a $1\times 3$ filter to have the same result. Computerphile made a great video about this : [Separable Filters and a Bauble](https://www.youtube.com/watch?v=SiJpkucGa1o).

In the usual $3\times 3$ filter example, this leads to 6 multiplications per convolution op instead of 9. As the filter grows in size, it gets really interesting in terms of computational savings. If you're interested in reducing the nb of parameters, it also means you only have to store 6 weights instead of 9.

Unfortunately, not all filters can be *separated*. The famous Sobel filter for edge detection is an example of a separable filter.

### Depthwise Separable Convolutions

We can however make use of another "separable" convolution.

## Residual Connexion
Introduced by ResNet, a residual connexion is a sum from a layer $l$ at layer $l+i$. The goal is to ensure that blocks of the network are not trying to keep track of the identity matrix of the input. It helps lessen gradient vanishing for very deep networks.

# A wrap up

The community of Deep Learning offer an infinite source of tricks to enhance your network. You can find an hopefully interesting sample of those in this post.

Feel free to give me a feedback, like a tricks that is missing from this list and you would like to see up there ! :smile:

*This article is WIP* 