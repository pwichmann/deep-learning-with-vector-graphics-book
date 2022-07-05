# Deep generative models

## What are deep generative models?

How do generative models fall into the categories of supervised or unsupervised learning?
How does it differ from problems like classification?


The basic problem of generative modelling is an **unsupervised** learning problem.
In **supervised** learning, like classification, the goal is to learn a function to map input data to some label. And in order to learn that function, training data has to be provided that contains both the input data as well as the correct label.


However, for generative modelling, the basic problem is unsupervised. Data samples are provided but not labels. The goal is to learn the hidden or underlying structure of the data, in particular to learn the underlying probability distribution function. Once learned, one can then draw new samples from it.


### Goal of generative modeling

Take as input training samples from some distribution and learn a model that represents that distribution 
(source: MIT Introduction to Deep Learning 6.S191: Lecture 4). There are two main ways:

1. **Density estimation** -- the task is to learn an approximation of the probability distribution function
2. **Sample generation** -- given some input samples from some data distribution, we try to learn a model of that data distribution and use that process to generate new samples that hopefully fall in line with what the true data distribution is.

The overall task is the same:
We are trying to learn a probability distribution using a model that approximates the true probability distribution of the data.
What makes this task complex is that we are often working with data, such as images, that is very high-dimensional.


## Problem types

* Unconditional vs. conditional (e.g. on a class label, on an embedding)
* One-shot vs. sequential(?)
* Deep Generative Models for Graph Generation
* ...

## Common architectures / pipelines for generative models?

* GANs
* VAEs
* Generative RNN
* Diffusion models


### Variational Auto-Encoder (VAE)

### Generative Adversarial Network (GAN)

### Generative Recursive Neural Network (Generative RNN)



### Deep Graph Generation

Excellent survey paper on the topic: https://arxiv.org/pdf/2007.06686.pdf
Great illustration of the deep graph generation space.



## Material

Excellent lecture by MIT on "Deep Generative Modeling" published on Youtube: https://www.youtube.com/watch?v=QcLlc9lj2hk. MIT Introduction to Deep Learning 6.S191: Lecture 4 with lecturer Ava Soleimany from January 2022.