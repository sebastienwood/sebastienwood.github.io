---
title: "A compendium of tricks and concepts in Deep Learning"
categories: tricks
date:   2019-05-19 11:30:19 -0400
toc_max_header: 2
visible: 1
---

Sometime you wish to had all the tricks and concepts of Deep Learnings/machine learning summarized in one place. I hope to gather them all slowly but steadily in this post. Everything is accessible with the navigation bar on the right.

# A Deep Learning Compedium

## Model
(terminology)

A model is akin to a box where you input something and you expect something in return. A deep neural network is a model, as well as a linear regression is a model, etc. Models are made of parameters that you can optimize during training, and hyperparameters that are chosen before training.

## Parameters
(terminology)

Parameters are a set of elements that the training phase of a model will modify to optimize for the objective function.

## Hyperparameters
(terminology)

Hyperparameters are a set of elements that are chosen before the training phase and that will influence the optimization of the objective function.

## Training / testing
(terminology)

Training is the process of tuning the parameters of a model given some hyperparameters to maximize an objective function. Testing is the process of applying this model on new, unseen data to ensure the model is relevant in the wild.

### Overfitting
Overfitting is when the model has learnt the pattern of the training set and thus will not perform well on new data.

### Regularization
Regularization is a set of techniques that are used to prevent overfitting. 

## Dataset
(terminology)

A dataset is comprised of samples (inputs) that may or may not have labels (outputs). Samples may be images, tabular data, sound, videos, anything really. Label could be a discrete/continuous value, ordinal/cardinal, etc.

### One hot encoding (dummy)
In case of a discrete value either for input or output it's most often a good practice to one hot encode them. Eg given a variable that takes value in $\{1,2,3\}$ if you had to one hot encode a $2$ you would get the vector $[0, 1, 0]$.

## Training validation and test sets
(terminology)

Given one dataset, you shouldn't 

### Cross validation
A typical methodology to ensure the model is reliable in traditionnal machine learning (statistics). Given $k$ an integer, split your dataset $k$ times in training/test sets with tests sets clearly distincts (no sample is in more than 1 test set). Often not applied to deep learning since it takes quite a lot of time to train only one Deep NN.

### Boostrapping

## Objective function
(terminology)

## Neural Network (NN) / Deep NN
(terminology model)

A neural network is a model that could generally be understood as a graph where the input is processed by "neurons" (nodes in the graph) and "synapses" (edges in the graph) to produce an output. The nodes are summing all the incoming values and pass them through a non linear function. The synapses are taking the output from one node, multiply them by a coefficient (a weight), and pass them as the input to another node. The parameters to tune are the weights of the edges. Can apply to any dataset, is less prone to overfitting, and offers a wide range of tools and methods to push farther the artificial intelligence field. The parameters are the weights, the hyperparameters are scattered across optimizer, objective function, etc. The breakthrough methodology to update the weights is the backpropagation algorithm which most optimizers exploit in their own ways.

### Optimizers
Deep NN offers quite a lot of solutions to learns the weights given an objective function, which are called optimizers. Most famous are SGD, Adam and so on.

## Backpropagation
(terminology optimizer)

## Gradient explosion / vanishing
(terminology)

NN speaking

## X-D Convolutions
(ops)

*TODO* develop :smile:
~~I assume the idea of 2D convolution is quite well known.~~ It however is generalizable to any dimension superior or equal to 1. It means the filter is scaled to the XD convolution. 1D convolution can be used in the context of eg. time series. 3D convolution can be used for 3D space. You see where it goes.

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
Introduced by ResNet, a residual connexion is a sum from a layer $l$ at layer $l+i$ in a NN. The goal is to ensure that blocks of the network are not trying to keep track of the identity matrix of the input. It helps lessen gradient vanishing for very deep networks.

## Adversarial Architecture
Generaly speaking, an adversial architecture makes use of one 'player' and one 'teacher' (I'm using gloves with those two words :smile:). Most often it refers to Generative Adversarial Network (GAN), but imo it also echoes with Actor/Critic architecture in RL, student/teacher (hence the gloves) knowledge distilling methods, etc.

## Unsupervised learning
(learning type)

An unsupervised learning means there are no label you can make use to optimize the parameters of your model.

## Supervised learning
(learning type)

In supervised learning you have access to all the labels you need.

## Semi-Supervised learning
(learning type)


## Weak supervision
(learning type)

Weak supervision is when you accept multiple noisy labels per sample. For example, you could have a tuple of heuristics to label your dataset that may diverge on the labels. It would most probably be useful when you have an industrial problem with expert that are too short on time to allow for the creation of a labeled dataset. Then you create/make use of a few labeling functions (heuristics) that are easy to do and *shazam* you have a noisy labelled dataset that one neural net may understand well enough to get rid of the noise. [Standford AI Lab blog](http://ai.stanford.edu/blog/weak-supervision/) has a great intro article about that.

## Reinforcement Learning (RL)
(learning type)

The model takes actions from $a$ in an environment $e$ and receive can receive a reward $r$ given $a$ at time $t$. The goal is to find $a$ that maximize the sum of $r$ in $e$ on a horizon of $t$. Under the hood there is a Markov Decision Process that would be too large for traditionnal optimisation techniques to apply ! (eg dynamic programming)

### Exploration vs exploitation
The most common problem in RL is the exploration/exploitation tradeoff. Exploration is to try new things unbeknownst of what the result would be (or guessing it) and exploitation is relying on what you already know.

## One shot / few shots learning
(learning type)

In the training dataset, there is only one/few sample per class. 

## Transfer learning
(learning type)

Make use of a model trained on different task to bootstrap on a new task (with possibly higher constraints on that task !). Eg I have a trained ResNet and I need to do things related to computer vision. 

## Multi task learning
(learning type)

One model 

## Augmentations

Are performed on the data at hand to ensure equivariance in the results. Standard augmentations are flipping, rotating, changing luminosity, warping, etc. They must be applied in a way such that an human could still give a good answer on the augmented data.

### Equivariance

## Mixup
(aug)

Mixup consist in randomly mixing two input images of different classes and 

## Cutout
(aug)

Cutout consist in randomly removing parts of the input image (in vision) so that the network should make up for what's missing.

## Student teacher architecture
(architecture)

In such an architecture, a good network is trained (teacher). Then, a smaller network (student) is also trained to mimick the outputs of the teacher. The teacher has uncertainty on its output (soft labels) and the task is thus easier for the student to approximate the soft labels thant the hard labels. 

# A wrap up

The community of Deep Learning offer an infinite source of tricks and concepts to enhance models. You can find an hopefully interesting sample of those in this post.

Feel free to give me a feedback, like a tricks that is missing from this list and you would like to see up there ! :smile:

*This article is perpetual WIP* 