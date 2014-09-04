---
layout: post
title: "Studying Note: Gibbs Sampling"
date:   2014-09-04 20:43:00
categories: study-note
tags: gibbs-sampling mcmc metropolis-sampling
---

This is **NOT** a tutorial on Gibbs Sampling. But just a bunch of questions I had when reading the tutorial [here](http://leonidzhukov.net/hse/2013/stochmod/papers/intro_to_mcmc_mackay.pdf).

## My Questions

- **How to model the full conditional probability, e.g., \\( P(X\_i \| X\_1, \cdots, X\_{i-1},  X\_{i+1}, \cdots, X\_{K}) \\)?**

   If we know the explicit form of \\( P(\bf{X}) \\), then we should know the full conditional probability.

   **Question**: is the \\( P(\bf{X}) \\) always known? Or even it is know, is it the case that the full conditional probability is known?

- **Why sampling from \\( P(\bf{X}) \\) is harder than sampling each variable in turn?**

  One dimensional data should be simpler to sample from because of the dimension. Think of the sampling process as sample from a list of bars with different lengths. If there are many dimensions, the landscape will be too large.

- **If every proposal is always accepted, then how to make sure the proposal is within the range of landscape?**

  *Another question might be:*

  Why are there refusal for the sample points for Metropolis Sampling?

  *My answer:*

  Because jumping probability distribution is defined by us, thus the sample point might jump into the extremely low probability region.

  Back to this question, the intuition might be, because we are sampling *quite* approximately from \\(P(\bf{X})\\) using the full conditional probability, there are no such problem in Metropolis Sampling?


- **Why is it called Heat Bath Algorithm also?**

- **\\(P(X,Y)\\) can be rewritten using \\( P(X\|Y) \\) and \\(P(Y\|X) \\) (The Hammersley-Clifford Theorem). So what?**

  So \\(P(X,Y)\\) can known through the knowledge of \\(P(X\|Y)\\) and \\(P(Y\|X) \\).

  This sampling is equivalent to:

  Sampling \\( (X\_{i+1}, Y\_{i+1}) \\) by \\( X\_{i+1} \sim P(X\|Y\_i) \\) and \\( Y\_{i+1} \sim P(Y\|X\_{i+1}) \\) approximates sampling \\( (X,Y) \\) from \\( P(X,Y) \\).

  Intuitively, this *seems* to be right.

## Comparison between Gibbs Sampler and Metropolis Sampler

 - **Similarity:**


 They are both random walk methods. The next sample point depends on the previous one.
 
 - **Difference:**

 Gibbs *has to* work with more than two parameters.

 Gibbs has no refusal for sample points, this could be a good thing as it might accelerate the sampling process, while Metropolis requires that.

 Gibbs requires that the full conditional probability is known, while Metropolis does not.


## Futhur Questions

1. What is Gibbs distribution?
2. What is *Hammersley-Clifford Theorem*? And why is so important for Gibbs sampling and for other methods as mentioned [here](http://www.idi.ntnu.no/~helgel/thesis/forelesning.pdf)
3. In what cases the full conditional distribution is unknown so that Gibbs sampling can be used?

