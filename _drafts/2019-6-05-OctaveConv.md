---
title: "Octave Convolutions"
categories: ops
date:   2019-06-05 11:30:19 -0400
---

Convolutions are at the heart of modern Deep Learning. However, it has some serious limitations depending the task it's on thus motivating either the use of bigger models or architectural tricks. One such architectural trick is to hard encode the multi-scale information : Feature Pyramid Network for example are extracting multiple resolutions for the same image and use that as a feature map bank. It follows that the network must be handcrafted to better suit the trick that is applied. By doing so, we expect to ease the task of the convolutions : with 3x3 filters detecting features that are hundreds of pixel is bothersome. On the other hand, one could make use of bigger filters, at the expense of speed (accurate convolution time complexity grow cubicaly). There is thus a compromise to seek : either getting slow (but not sure to have better results) or relying on tricks.

In *Drop an Octave: Reducing Spatial Redundancy in Convolutional Neural Networks with Octave Convolution* one appealing trick is proposed : a drop-in replacement for usual convolutions that put an emphasis on the feature frequency in images.

