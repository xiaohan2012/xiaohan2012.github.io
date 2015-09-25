---
layout: post
title: "Graphical Model: Learning in Fully Observed Bayesian Networks "
date: 2015-09-25 10:25:05
categories: cat
tags: kw
---

## Structural learning

An interesting problem but not many statistically optimal solutions by far. Optimal means maximizing some objective funciton such as log-likelihood.

For \\(n\\)nodes, there are \\(O(2^{n^2})\\) possible graphs, while \\(n!\\) trees.

### Chow-Liu algorithm

Chow-Liu algorithm finds the exact solution for an optimal **directed** tree(under MLE)

Tricks:

1. MLE decomposable
2. Each child has only one child

#### Decomposing MLE

![chowliu-mle-1](assets/images/pgm/chowliu-mle-decompose-1.png)
![chowliu-mle-2](assets/images/pgm/chowliu-mle-decompose-2.png)

MLE can be decomposed into sum of mutual information between nodes and their parents.

Several transformation note:

- Changing the index order \\(n\\) and \\(t\\)
- Use \\(count(x_i, x_{\pi_i(G)})\\)


#### Single parent constraint

As each node can only have at most one parent, we only consider the \\(I^{\^}\\) between **each pair** of nodes.

$$
I^{\^}(X_i, X_j) = \sum\limits_{x_i, x_j} p^{\^}(x_i, x_j) \log \frac{p^{\^}(x_i, x_j)}{p^{\^}(x_i)p^{\^}(x_j)}
$$
$$
p^{\^}(x_i, x_j) = \frac{count(x_i, x_j)}{M}
$$

Given the edge weights(MI) between each pair of nodes, we want to find the set of edges that:

1. form a tree
2. maximumize the sum

This reduces to maximum spanning tree problem. You can solve it using [Kruskal's Algorithm](http://mathworld.wolfram.com/KruskalsAlgorithm.html) for example.

#### Multiple optimal trees

Given one tree topology, there can be multiple BNs with the same loss value. For example:

![ChowLiu-i-equivalent](/assets/images/pgm/chow-liu-i-equivalent.png)

Moreover, those BNs are I-equivalent.

However, those I-equivalent BNs have different interpretations.

#### Application: DNA sequence classification

The motif/junk DNA sequence classification example.

Basic idea: use the tree and CPD as define a density function, apply the pairwise CPD to give the likelihood that a sequence(a list of nodes can be seen as list of pairs of nodes) appear. 

## Parameter learning for fully observable GM

Estimation principles:

- MLE
- Bayesian estimation
- Maximmal conditional likelihood(part of the graph)
- Maximal margin(origin from SVM)
- Maximum entropy



## Summary
