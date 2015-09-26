---
layout: post
title: "Graphical Model: Learning in Fully Observed Markov Networks"
date: 2015-09-26 23:53:46
categories: course
tags: pgm markov-network structure-learning parameter-estimation
---

## Structural Learning


Precision matrix and covariance matrix: different implication.


C matrix: captures marginal dependence and independence
Q matrix: captures conditional dependence and independence

- **Q**: Why this conclusion?
- **Q**: Why precision matrix more meaningful than covariance matrix?
  Because many seemingly unrelated factors can be correlated(even butterfly and hurricane) thus there is the densely connected graph under the C matrix implication. However under Q matrix, we only consider conditional independence, thus a more sparsely connected graph. Easiler to interpret and compute.

For example, some contrast between covariance matrix and precision matrix:

![cov-prec-mat](/assets/images/pgm/covariance-and-precision-matrix-example.png)

If \\(n\\) is sufficiently large compared to \\(p\\), which means the covariance matrix is well-conditioned, then it's easy to get \\(Q\\).


However, if \\(p >> n\\), data sample size much smaller than data dimension. Covariance matrix is *ill-conditioned*, thus not invertible(**why?**)

It's a hot topic on estimation of the precision matrix for high-dimensional data from limited amount of samples. One way is called *Graph Regression*.

*Graph Regression* works as follows:

1. Use Lasso to selected neighbors for each node
   For each \\(x_i\\), treat it as the output of the linear regression on the rest of variables \\(\mathbf{x}_{-i}\\), or \\(x_i \sim \mathcal{N}(\mathbf{q}_{i} \mathbf{x}_{-i}, \sigma^2)\\). By adding a Lasso regularizer, we can obtain a sparse \\(\mathbf{q}_{i}\\) and only the non-zero ones are selected as the neighbors.
   The \\(\mathbf{q}_i\\) might be wrong, but using the below theorem,  the neighbors are correct.
2. Estimate \\(Q\\) by constraining the nonzero entries in \\(Q\\) to correspond to the selected neighbors of each node. In this way, the problem reduces to parameter estimation given known graph structure(discussed next)

Lasso gives sparse parameter estimation, which is attractive because we would like a sparsely connected graph.

![lasso](/assets/images/pgm/linear-regression-with-lasso.png)

Tuning \\(\lambda\\): cross validation. At some point, \\(\mathbf{q}_{i}\\) drops to zero more quickly.

One theorem on the graph consistency(sparsistency) of Graph Regression algorithm:

![sparsistent](/assets/images/pgm/sparsistent.png)

We are actually using Lasso to recover the graphical structure.

For **discrete** values, we can use logistic regression.

## Parameter Estimation given known structure


