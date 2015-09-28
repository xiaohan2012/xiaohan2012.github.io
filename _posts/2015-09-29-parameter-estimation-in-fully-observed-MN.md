---
layout: post
title: "Graphical Model: Learning in Fully Observed Markov Networks"
date: 2015-09-28 23:09:56
categories: course
tags: pgm markov-network structure-learning parameter-estimation
---

## Structural Learning


Precision matrix and covariance matrix: different implication.


C matrix: captures marginal dependence and independence
Q matrix: captures conditional dependence and independence

- **Q**: Why this conclusion?
- **Q**: Why precision matrix more meaningful than covariance matrix?
  Because many seemingly unrelated factors can be correlated(even butterfly and hurricane) thus there is the densely connected graph under the C matrix implication. However under Q matrix, we only consider conditional independence, thus a more sparsely connected graph. Easier to interpret and compute.

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

No decomposibility. Partition function \\(Z\\) defined on all parameters.

In order to maximize the log-likelihood:

![](/assets/images/pgm/log_p_mn.png)

we get the derivative:

![](/assets/images/pgm/derivative_log_p.png)

And setting the derivative to zero leads to:

![](/assets/images/pgm/p_mle.png)

which is the condition that must be satisfied for MLE, though this result does not tell us **how** to estimate.

### Decomposable UGM

Roadmap:

![](/assets/images/pgm/estimation-method-table-for-ugm.png)

### In-decomposable UGM

For triangulated graph and potentials defined on maximal cliques(**why this?**):

![](/assets/images/pgm/joint-distribution-of-decomposable-ugm.png)

We can verify that if

$$ \hat{p}_{MLE}(\mathbf{x}) = \frac{\prod \widetilde{p}(\mathbf{x}_c)}{\prod \widetilde{p}(\mathbf{x}_s)}$$

then above condition is satisfied.

So if the UGM is decomposable, its potentials defined on maximal cliques and potential function is tabular, the potential functions' value can be directly inspected(easily computed) using the MLE condition.


### Iterative Proportional Fitting(IPF)

Observing that:

![](/assets/images/pgm/ipf_p_over_psi.PNG)

we can know that this is a iterated function, meaning \\( X \rightarrow X \\) and finding the closed form solution is hard.

Fixed point iteration can help:

![](/assets/images/pgm/ipf_fixed_point_iteration.PNG)

Calculating \\(p^{t}(\mathbf{x}_c)) is actually an inference problem. What makes learning in MN different to that in BN is the the inference problem nested in the learning problem.

IPF from the information theoretic view(I-projection).

[Coordinate ascent](https://en.wikipedia.org/wiki/Coordinate_descent) algorithm? Convergence proof?

### Feature-based clique potentials(Generalized Iterative Scaling)

Motivation for feature-based one: fewer parameters while using the same graphical model.

Basic ideas in learning: instead of directly taking the derivative(because computing \\(\log Z \\) term is expensive), lower bound is derived using the maximum value of log(tagent line) and Jensen's inequality for \\(\exp\\)

The result is:

![](/assets/images/pgm/gis_result.png)

The \\(\sum\limits_x \tilde{p}(x)f_i(x)\\) is actually the sum of \\(f_i\\) in training dataset. If \\(f_i\\) is binary, it's essentially counting.


GIS is more general than IPF as the tabular potential function is a special case of feature based potential function.


