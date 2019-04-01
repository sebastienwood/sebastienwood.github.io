---
layout: post
title: The challenge for fewer bits
---

# Concepts
Quantization : putting things in bins (AKA discretization). Useful to switch from a mulitple bits/bytes representation to fewer bits/bytes.

# Architecture review

Binary Connect (Courbariaux & al. in 2015) : from 32 FP weights to 1 bit weights. Using 32x less memory at run time. At training, makes use of FP representation to update the weights. Then reduce the weights to the set {-1, 1} with the sign(weight) function for all forward passes.
A 3x3 filter will then have 2^(3x3)=512 possible states. What if it used separable convolutions ? 2^(3+3)=64 possibles states. Would the result keep up ?
Results are mostly good if the network is "big" enough.

DoReFa-Net :

Binary-Net :

XNOR-Net :

# PyTorch time

2 paths : external weight manager or customs layers.

## External Weight Manager
Can be applied to any nn.Module on the fly. Severe limitations if we want to modify in-depth the behavior of e.g. grads.

class EWM():
      def __init__(self, model):
      	  # grab the pointer to the real weights
	  self.real_weights = []
	  self.stored_weights = []
	  for m in model.modules(): # iterate over the layers
	      self.real_weights.append(m.weight)
	      self.stored_weights.append(m.weight.clone())

      def quantize(self):
      	  # store the real weight and modify in place the actual weights
	  for m in self.real_weights:
	      self.stored_weights

## Custom layers
Require to redefine each architecture using the custom layers. Can do anything.


