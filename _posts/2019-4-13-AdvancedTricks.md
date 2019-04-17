---
layout: post
comments: true
title: Advanced tricks in Deep Learnings
---

# What you wish you would've known

Having taken one Deep Learning class at Polytechnique Montreal with a general approach for Deep Learning, I admit you can't expect to review every trick to overpower your model in such a limited amount of time.

However, after a torough year on Twitter (where the Deep Learning community is quite lively) and on other venues like Medium, conferences streams, and such; I've come to realise there are quite a lot of tricks one can think of when building a Neural Network. Those cover a wide range of concepts, from purely mathematical to hardware based tweaks.

In this post, I'll give some general ideas and classify them in Table 1 so one could have a quick answer for a given need.

# The (Mighty) Table 1

¯\_(ツ)_/¯

# The Compendium of Network Tricks

## Separable Convolutions

Simple and wide spread idea of using two 1d filters to represent one 2d filter. Eg. for a 3x3 filter, you could use a 3x1 filter followed by a 1x3 filter to have the same result. Computerphile made a great video about this : https://www.youtube.com/watch?v=SiJpkucGa1o.

In the usual 3x3 filter example, this leads to 6 multiplications per convolution op instead of 9. As the filter grows in size, it gets really interesting in terms of computational savings. If you're interested in reducing the nb of parameters, it also means you only have to store 6 weights instead of 9.

Unfortunately, not all filters can be *separated*. The famous Sobel filter for edge detection is an example of a separable filter.

### Depthwise Separable Convolutions to the rescue

If we want to abstract the idea of separability, Mobile Nets introduced the idea of using  

# A wrap up

The community of Deep Learning offer an infinite source of tricks to enhance your network. You can find an hopefully interesting sample of those in this post.

Feel free to give me a feedback, like a tricks that is missing from this list and you would like to see up there ! :)

*This article is WIP* 