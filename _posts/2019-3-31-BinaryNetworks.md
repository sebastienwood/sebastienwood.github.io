---
title: "The challenge for fewer bits in Deep Learning"
categories: quantization
date:   2019-05-19 11:30:19 -0400
visible: 1
---

Usual deep learning parameters are stored on 32 bits FP hardware (though 16 bits has became supported by Pytorch, hopefully things are on a good way !).

Fewer bits will reduce by a huge margin the total resources requirements : memory, compute, communication between hardware parts. In the extreme case of binarization, demanding multiplication and/or division may become bitwise shifts (moving the binary representation to the left or the right).  

The main use case is for edge computing or low resources hardware : with those computational savings, running the network is less demanding. The battery, often an issue, has less strain.

Another use case would be to scale properly the needs for datacenters. 

## Concepts
*Quantization* : putting things in bins (AKA discretization). Useful to switch from a mulitple bits/bytes representation to fewer bits/bytes. Binarization is a quantization on 1 bit (2 possible states).

*Normalized energy* : to better understand the gains of a quantization tweak regarding energy, we need to compare what's comparable. The network should be considered as a whole and thus the given performance (eg. accuracy) should be interpreted under the light of the global energy consumption.

Consider for example a 1 bit quantization scheme that would degrade the performance by a margin. The same performance could be achieved by reducing the network depth, hence reducing the memory/energy/throughput by a proportional amount.

Considering normalized energy as a metric is appropriate for a rigorous scientific approach.

### Lazyness notations

- $\textbf{W}$ is a weight matrix
- $\textbf{I}$ is the input matrix
- $\odot$ is the element wise product
- $*$ is the convolution op. when used on matrix (otherwise, multiplication)
- a bold letter in a math context is usually a matrix
- $w$, $h$ denote width and heigth of a convolution filter unless stated otherwise

# Architecture review

## Binary Connect (Courbariaux & al. in 2015)

From 32 FP weights to 1 bit weights. Using 32x less memory at run time. When backpropagating, makes use of FP representation to update the weights. Then reduce the weights to the set ${-1, 1}$ with the $sign(weight)$ function for all forward passes.

The set with negative and positive values will allow the network to use ReLU activations functions. "Naïve" binary (0, 1) would otherwise cause all the values to be positive eventually (rendering ReLU effectless). It would require tricks like batch normalization to keep negative values. 

It will reduce the diversity of filters for a convolution to a combination of 2 possibles states on the size of the filter. A 3x3 filter will then have 2^(3x3)=512 possible states.

> What if it used separable convolutions ? 2^(3+3)=64 possibles states. Would the result keep up ?

Results are mostly good if the network is "big" enough. Savings are hard to see with Pytorch since a weight bitwidth cannot be defined out the scope 16/32bits supported natively. Some memory saving may be achieveable would the binarized weights be saved, hence discarding the real valued weights. It's unclear if doing so would harm potential post-treatments.

Computationnal savings are good if there is a good handle of the new operations : change of sign for the multiplication operation and simple addition/substraction as usual.  

## DoReFa-Net (Zhou & al. in 2016)

Has a more holistic approach for low bitwidth networks : quantization is applied to activations and gradients as well. DoReFa stands for the 1-bit weight, 2-bits activation and 6-bits gradients that the authors used on an Alexnet experiment with the ImageNet dataset (an octave double the frequency, whereas they double the bitwidth). Authors have shown that a performance comparable to the 32-bits network was possible on the SVHN and ImageNet dataset. A comparison is made for a few handpicked combinations and it seems it was the most interesting.

> Combinatorial choices had to be made, but the approach seems rather heuristically-guided. 

Weights and activations make use of a deterministic quantization scheme; while the gradients "need to be stochastically quantized". First and last layers are kept out of the optimization they propose since people tend to agree doing so would tends to degrade "too much" (without a measure of "how much") the network performance.

Authors propose a quantization scheme as follow :

## XNOR-Net / Binary Weight Network (Rastegari et al. in 2016)

Let's begin with BWN, a simple tweak to BC. The author propose to add a scaling factor $\alpha = \frac{1}{n} ||W||_{l1}$ (the average of FP32 weights) at the end of a binary convolution to make up for the loss of information due to the weight binarization. It would seem that it results in more accurate networks. At the same time, the memory hurdle is "relatively" small (store $\alpha$ for all filters) and there is an additional multiplication to perform for each output of a filter.

> In fact, it's a generalization of BC for which $\alpha = 1$. 

XNOR-Net is more aggressive : inputs of filters are also binarized. To do so, all activations are also binarized using the $sign(\textbf{W})*\beta$ function. $\beta$ is the counterpart for $\alpha$ when speaking about input/ouput quantization. To avoid the multiplications due to $\beta$ over all the possible configurations of the input, author make use of an approximation. The final operation is thus $sign(\textbf{I}) * sign(\textbf{W}) \odot \textbf{K}\alpha$ with the new element $\textbf{K} = \textbf{A} * \textbf{k}$ that is used to approximate efficiently the $\beta$ multiplications. The matrix $\textbf{A}$ is the per channel average of absolute values and \textbf{k} a filter the same size as the current convolution filters filled with $\frac{1}{w*h}$. In that context, since both matrix are binary the convolution operation is a bitcount operation.

*Binary-Net* :



# PyTorch time

3 paths I know of : external weight manager (an object called at will to quantize), customs layers which autoregulate themselves, or forward/backwards hooks. 

## External Weight Manager
Can be applied to any nn.Module on the fly. 

'''some_tensor.data.copy_''' allow to modify in place a Pytorch tensor value.
Logic may be included in the quantization function, e.g. applying to Conv2d layers only. Would need to test additionnal attributes at model initialization to diversify the logics used. E.g. for ResNet, differentiating blocks numbers. 

```python
class EWM():
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
```

## Custom layers
Require to redefine each architecture using the custom layers. Can do anything.

## Forward/backward hooks
Pytorch offers to register forward and backwards hooks on a model. Pytorch documentation for this method is parse/non-existent.

It appears that Pytorch forward hook doesn't allow on the fly modification to the output tensor (e.g. applying our  quantization function would fail). However the backward hooks allow to modify the grad tensor (developer logic ?).

Given https://github.com/pytorch/pytorch/issues/262 it seems that it's just awaiting a courageous developer to implement a functional forward hook. L-*hook forward* to it !

## Diving deeper
If you want to optimize the actual hardware ops (CUDA 1 bit, etc) while it's not supported officially by either Pytorch or NVIDIA, you're bound to write custom kernels (a kernel is just a piece of software for CUDA) and custom Pytorch OPS. Cross your fingers for backward compatibility in updates of those frameworks.

Otherwise, FPGA (a customizable piece of hardware) can be used too, mostly if you're planning to use it for inference only (may be overly complicated for training).

# TL;DR
You can effectively quantize the building blocs of a neural networks and still manage to reach a good accuracy.

There are diverse way to quantize the blocs, with the challenging objective to reduce as much as possible the bits used while keeping a high accuracy. The inherent quantization noise can act as a regulariser.

It all depends on the context for the results, and the field is still too novel to ensure one basic quantization scheme will work flawlessly across all models. I personnally would tend to think overly large networks (i.e. many parameters)  would manage to work in a quantized context. The real challenge lies in quantizing small enough networks.

Small enough networks echoes the fact that the same energy savings could be achieved by only reducing the network size. It thus matter to compare the energy at a constant accuracy (AKA normalized energy) so as to have more meaningful hindsights toward the *real* value of the quantization scheme proposed.

In the end, you just reduce the amount of information (or reduce the entropy) of the network.

# Open questions

- what are the necessary conditions for a quantization scheme to work ?
- how would it cope in contexts of smalls tweaks like separable convolutions ?
- given the finite combination of filters for a binarized CNN, what can we learn about the dynamics of a neural networks ? 

*This article is a WIP*

*Feel free to contact me on Twitter if you have any question/remark/suggestion*
