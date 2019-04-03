---
layout: post
comments: true
title: The challenge for fewer bits
---

Usual deep learning parameters are stored on 32 bits FP hardware (though 16 bits has became supported by Pytorch, hopefully things are on a good way !).

Fewer bits will reduce by a huge margin the total resources requirements : memory, compute, communication between hardware parts. In the extreme case of binarization, demanding multiplication and/or division may become bitwise shifts (moving the binary representation to the left or the right).  

The main use case is for edge computing or low resources hardware : with those computational savings, running the network is less demanding. The battery, often an issue, has less strain.

Another use case would be to scale properly the needs for datacenters. 

## Concepts
Quantization : putting things in bins (AKA discretization). Useful to switch from a mulitple bits/bytes representation to fewer bits/bytes. Binarization is a quantization on 1 bit (2 possible states).

# Architecture review

*Binary Connect (Courbariaux & al. in 2015)* : from 32 FP weights to 1 bit weights. Using 32x less memory at run time. At training, makes use of FP representation to update the weights. Then reduce the weights to the set {-1, 1} with the sign(weight) function for all forward passes.

The set with negative and positive values will allow the network to use ReLU activations functions. "Naïve" binary (0, 1) would otherwise cause all the values to be positive eventually (rendering ReLU effectless). It would require tricks like batch normalization to keep negative values. 

It will reduce the diversity of filters for a convolution to a combination of 2 possibles states on the size of the filter. A 3x3 filter will then have 2^(3x3)=512 possible states.

> What if it used separable convolutions ? 2^(3+3)=64 possibles states. Would the result keep up ?

Results are mostly good if the network is "big" enough. Savings are hard to see with Pytorch since a weight bitwidth cannot be defined out the scope 16/32bits supported natively.

Computationnal savings would be scarce since the convolutions are still operating on FP inputs.  

DoReFa-Net :

Binary-Net :

XNOR-Net :

# PyTorch time

2 paths : external weight manager or customs layers.

## External Weight Manager
Can be applied to any nn.Module on the fly. Severe limitations if we want to modify in-depth the behavior of e.g. grads : would require far more code to do the same results as path 2.

'''some_tensor.data.copy_''' allow to modify in place a Pytorch tensor value.
Logic may be included in the quantization function, e.g. applying to Conv2d layers only. Would need to test additionnal attributes at model initialization to diversify the logics used. E.g. for ResNet, differentiating blocks numbers. 

'''class EWM():
      def __init__(self, model):
      	  # grab the pointer to the real weights
	  self.real_weights = []
	  self.stored_weights = []
	  for m in model.modules(): # iterate over the layers
	      self.real_weights.append(m.weight)
	      self.stored_weights.append(m.weight.data.clone())

      def quantize(self, quantization_function):
      	  # store the real weight and modify in place the actual weights
	  for i, m in enumerate(self.real_weights):
	      self.stored_weights[i].data.copy_(m)
	      m.data.copy_(quantization_function(m))
'''

## Custom layers
Require to redefine each architecture using the custom layers. Can do anything.


