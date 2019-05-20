---
layout: post
title: "Reference"
date: 2019-05-20 23:18:00
tags: autoencoder vae
# image: "vae-gaussian.png"
---

> This is a sample sentence.

<!--more-->

Content samples. abcdefg

{: class="table-of-content"}
* TOC
{:toc}

## Autoencoder

**My homepage** is in [ref](https://bismex.github.io/).

It consists of two networks:
- *asd* network: asdf

![picture]({{ '/assets/images/sample/autoencoder-architecture.png' | relative_url }})
{: style="width: 100%;" class="center"}
*Fig. 1. Illustration.*

The model contains an encoder function $$g(.)$$ parameterized by $$\phi$$ and a decoder function $$f(.)$$ parameterized by $$\theta$$. 

$$
L_\text{AE}(\theta, \phi) = \frac{1}{n}\sum_{i=1}^n (\mathbf{x}^{(i)} - f_\theta(g_\phi(\mathbf{x}^{(i)})))^2
$$


## References

[1] Geoffrey E. Hinton, and Ruslan R. Salakhutdinov. ["Reducing the dimensionality of data with neural networks."](https://pdfs.semanticscholar.org/c50d/ca78e97e335d362d6b991ae0e1448914e9a3.pdf) Science 313.5786 (2006): 504-507.

