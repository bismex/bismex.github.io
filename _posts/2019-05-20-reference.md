---
layout: post
title: "From Autoencoder to Beta-VAE"
date: 2019-05-20 23:18:00
tags: autoencoder vae
# image: "vae-gaussian.png"
---

> Autocoders are a family of neural network models aiming to learn compressed latent variables of high-dimensional data. Starting from the basic autocoder model, this post reviews several variations, including denoising, sparse, and contractive autoencoders, and then Variational Autoencoder (VAE) and its modification beta-VAE.


<!--more-->


Autocoder is invented to reconstruct high-dimensional data using a neural network model with a narrow bottleneck layer in the middle (oops, this is probably not true for [Variational Autoencoder](#vae-variational-autoencoder), and we will investigate it in details in later sections). A nice byproduct is dimension reduction: the bottleneck layer captures a compressed latent encoding. Such a low-dimensional representation can be used as en embedding vector in various applications (i.e. search), help data compression, or reveal the underlying data generative factors. 


{: class="table-of-content"}
* TOC
{:toc}


## Notation

{: class="info"}
| Symbol | Mean |
| ---------- | ---------- |
| $$\mathcal{D}$$ | The dataset, $$\mathcal{D} = \{ \mathbf{x}^{(1)}, \mathbf{x}^{(2)}, \dots, \mathbf{x}^{(n)} \}$$, contains $$n$$ data samples; $$\vert\mathcal{D}\vert =n $$. |
| $$\mathbf{x}^{(i)}$$ | Each data point is a vector of $$d$$ dimensions, $$\mathbf{x}^{(i)} = [x^{(i)}_1, x^{(i)}_2, \dots, x^{(i)}_d]$$. |
| $$\mathbf{x}$$ | One data sample from the dataset, $$\mathbf{x} \in \mathcal{D}$$. |
| $$\mathbf{x}’$$| The reconstructed version of $$\mathbf{x}$$. |
| $$\tilde{\mathbf{x}}$$ | The corrupted version of $$\mathbf{x}$$. |
| $$\mathbf{z}$$ | The compressed code learned in the bottleneck layer. |
| $$a_j^{(l)}$$ | The activation function for the $$j$$-th neuron in the $$l$$-th hidden layer. |
| $$g_{\phi}(.)$$ | The **encoding** function parameterized by $$\phi$$. |
| $$f_{\theta}(.)$$ | The **decoding** function parameterized by $$\theta$$. |
| $$q_{\phi}(\mathbf{z}\vert\mathbf{x})$$ |Estimated posterior probability function, also known as **probabilistic encoder**.  |
| $$p_{\theta}(\mathbf{x}\vert\mathbf{z})$$ | Likelihood of generating true data sample given the latent code, also known as **probabilistic decoder**. |
| ---------- | ---------- |



## Autoencoder

**Autoencoder** is a neural network designed to learn an identity function in an unsupervised way  to reconstruct the original input while compressing the data in the process so as to discover a more efficient and compressed representation. The idea was originated in [the 1980s](https://en.wikipedia.org/wiki/Autoencoder), and later promoted by the seminal paper by [Hinton & Salakhutdinov, 2006](https://pdfs.semanticscholar.org/c50d/ca78e97e335d362d6b991ae0e1448914e9a3.pdf).

It consists of two networks:
- *Encoder* network: It translates the original high-dimension input into the latent low-dimensional code. The input size is larger than the output size.
- *Decoder* network: The decoder network recovers the data from the code, likely with larger and larger output layers.


![Autoencoder architecture]({{ '/assets/images/sample/autoencoder-architecture.png' | relative_url }})
{: style="width: 100%;" class="center"}
*Fig. 1. Illustration of autoencoder model architecture.*


The encoder network essentially accomplishes the [dimensionality reduction](https://en.wikipedia.org/wiki/Dimensionality_reduction), just like how we would use Principal Component Analysis (PCA) or Matrix Factorization (MF) for. In addition, the autoencoder is explicitly optimized for the data reconstruction from the code. A good intermediate representation not only can capture latent variables, but also benefits a full [decompression](https://ai.googleblog.com/2016/09/image-compression-with-neural-networks.html) process.

The model contains an encoder function $$g(.)$$ parameterized by $$\phi$$ and a decoder function $$f(.)$$ parameterized by $$\theta$$. The low-dimensional code learned for input $$\mathbf{x}$$ in the bottleneck layer is $$\mathbf{z} = $$ and the reconstructed input is $$\mathbf{x}' = f_\theta(g_\phi(\mathbf{x}))$$.

The parameters $$(\theta, \phi)$$ are learned together to output a reconstructed data sample same as the original input, $$\mathbf{x} \approx f_\theta(g_\phi(\mathbf{x}))$$, or in other words, to learn an identity function. There are various metrics to quantify the difference between two vectors, such as cross entropy when the activation function is sigmoid, or as simple as MSE loss:

$$
L_\text{AE}(\theta, \phi) = \frac{1}{n}\sum_{i=1}^n (\mathbf{x}^{(i)} - f_\theta(g_\phi(\mathbf{x}^{(i)})))^2
$$


## References

[1] Geoffrey E. Hinton, and Ruslan R. Salakhutdinov. ["Reducing the dimensionality of data with neural networks."](https://pdfs.semanticscholar.org/c50d/ca78e97e335d362d6b991ae0e1448914e9a3.pdf) Science 313.5786 (2006): 504-507.

[2] Pascal Vincent, et al. ["Extracting and composing robust features with denoising autoencoders."](http://www.cs.toronto.edu/~larocheh/publications/icml-2008-denoising-autoencoders.pdf) ICML, 2008.

[3] Pascal Vincent, et al. ["Stacked denoising autoencoders: Learning useful representations in a deep network with a local denoising criterion."](http://www.jmlr.org/papers/volume11/vincent10a/vincent10a.pdf). Journal of machine learning research 11.Dec (2010): 3371-3408.

[4] Geoffrey E. Hinton, Nitish Srivastava, Alex Krizhevsky, Ilya Sutskever, and Ruslan R. Salakhutdinov. "Improving neural networks by preventing co-adaptation of feature detectors." arXiv preprint arXiv:1207.0580 (2012).

[5] [Sparse Autoencoder](https://web.stanford.edu/class/cs294a/sparseAutoencoder.pdf) by Andrew Ng.

[6] Alireza Makhzani, Brendan Frey (2013). ["k-sparse autoencoder"](https://arxiv.org/abs/1312.5663). ICLR 2014.

[7] Salah Rifai, et al. ["Contractive auto-encoders: Explicit invariance during feature extraction."](http://www.icml-2011.org/papers/455_icmlpaper.pdf) ICML, 2011.

[8] Diederik P. Kingma, and Max Welling. ["Auto-encoding variational bayes.”](https://arxiv.org/abs/1312.6114) ICLR 2014.

[9] [Tutorial - What is a variational autoencoder?](https://jaan.io/what-is-variational-autoencoder-vae-tutorial/) on jaan.io

[10] Youtube tutorial: [Variational Autoencoders](https://www.youtube.com/watch?v=9zKuYvjFFS8) by Arxiv Insights

[11] ["A Beginner's Guide to Variational Methods: Mean-Field Approximation"](https://blog.evjang.com/2016/08/variational-bayes.html) by Eric Jang.

[12] Carl Doersch. ["Tutorial on variational autoencoders."](https://arxiv.org/abs/1606.05908) arXiv:1606.05908, 2016.

[13] Irina Higgins, et al. ["$$\beta$$-VAE: Learning basic visual concepts with a constrained variational framework."](https://openreview.net/forum?id=Sy2fzU9gl) ICLR 2017.

[14] Christopher P. Burgess, et al. ["Understanding disentangling in beta-VAE."](https://arxiv.org/abs/1804.03599) NIPS 2017.



