---
title: "NEWCAS19 - M. Omrani Tamrin, S. Henwood & al"
excerpt: "Using deep learning approaches to overcome limited dataset issues within semiconductor domain"
sidebar:
    - title: "Using deep learning approaches to overcome limited dataset issues within semiconductor domain"
    - image: http://placehold.it/350x250 
    - text: "We show that a basic GAN can manage to output something convincing with a ridiculously small dataset for the needs of anomaly detection in semiconductor industry."
---

To be presented in June @ Munich.

## Summary
We make use of VAE and GAN with an extremely limited dataset (approx 20 samples) of wafers exhibiting anomalies. Results are compared given the likelihood of the generated wafers (human evaluation). We show that GAN manage to achieve a convincing result, while VAE fails. 

The output is convincing as we see a mix of anomalies classes, and a bit of out of disribution anomalies. This model is still far from perfect as the anomalies aren't fully understood by the model and expanded for the generated samples. The model cannot differentiate what is the "anomaly" from the wafer caracteristics (eg color).

Future work will make use of this result to further refine the output of the GAN to differentiate what an anomaly from the wafer caracteristics.

## Personal note
There aren't many datasets on industrial problems in the wild and semiconductor (SC) is a great example. Causes are most often in the (dumb) IP war that the world is undergoing. Working as scholars with partners in SC we couldn't even gain access to real samples. We try to accomodate for this as much as possible in this work by looking for generative models as a way to give us biggers datasets from web scrapped images. The issue is then to generate samples that make senses. 

**Q : Wouldn't privacy enabled neural networks be a good way to go then ?**

A :Could be for problems where diagnosis is not needed. Here we need to explicitely tell what's going wrong and where on the wafer. As we need to *understand* what's happening and the output of the model, we can't operate on a black box input/output. Hence our generative approach.