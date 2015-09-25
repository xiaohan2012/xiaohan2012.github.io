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

![chowliu-mle-1](/assets/images/pgm/chowliu-mle-decompose-1.png)
![chowliu-mle-2](/assets/images/pgm/chowliu-mle-decompose-2.png)

MLE can be decomposed into sum of mutual information between nodes and their parents.

Several transformation note:

- Changing the index order \\(n\\) and \\(t\\)
- Use \\(count(x_i, x_{\pi_i(G)})\\)


#### Single parent constraint

As each node can only have at most one parent, we only consider the \\(\hat{I}\\) between **each pair** of nodes.

$$
\hat{I}(X_i, X_j) = \sum\limits_{x_i, x_j} \hat{p}(x_i, x_j) \log \frac{\hat{p}(x_i, x_j)}{\hat{p}(x_i)\hat{p}(x_j)}
$$
$$
\hat{p}(x_i, x_j) = \frac{count(x_i, x_j)}{M}
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


The log likelihood can be decomposed into a set of smaller CPDs, each parametrized by local paramater, \\(\theta_i\\). Density estimation for the GM can be done independently for each node. As the CPD is actually multinomial, which is an instance of exponential family distribution. We can get the MLE for natural parameter via moment matching.

### Discrete example: mutlinomial distribution

#### MLE

Objective function:

![ll-for-multinomial](/assets/images/pgm/ll-for-multinomial.png)

with constraint:

$$ \sum\limits_k=1^K \theta_k = 1$$

Which leads to constraint cost function with Lagrange multiplier

![cost-for-multinomial-in-lagrange](/assets/images/pgm/cost-function-for-multinomial-in-lagrange-form.png)

Thus we have \\(\lambda = N\\). So \\(\hat{\theta}_{k, MLE} = \frac{n_k}{N}\\)

#### Bayesian Estimation(Dirichlet prior)

We use Dirichlet distribution as prior(conjugate prior):

$$ P(\theta \vert \alpha) = C(\alpha) \prod\limits_k \theta_{k}^{\alpha_k-1}$$

Posterior:

$$ P(\theta \vert \mathbf{X}) \propto  \prod\limits_k \theta_{k}^{n_k + \alpha_k-1}$$

Posterior mean estimation $$ \theta_k = E_{p(\theta \vert \mathbf{X})} (\theta_k) = \frac{n_k + \alpha_k}{N + \sum \alpha_k} $$

Sequantial Bayesian update is equivalent to batch update.

Beside manually setting \\(\alpha\\) anothre way is through Empirical Bayes, where

$$ \alpha_{MLE} = argmax_{\alpha} p(D | \alpha) = \int p(D \vert \theta) p(\theta \vert \alpha) d \theta$$

#### Logistic Normal prior

Compared to Dirichlet, it's more flexible because of co-variance structure, but non-conjugate for multinomial distribution.

![dirichlet-3d](/assets/images/pgm/dirichlet-3d.png)
![logisitc-normal-3d](/assets/images/pgm/logistic-normal-3d.png)

Used in [Correlated Topic Modeling](https://www.cs.princeton.edu/~blei/papers/BleiLafferty2006.pdf)


### Continuous example: multivariate Gaussian

#### MLE

![mle-multivariate-gaussian][/assets/images/pgm/mle-multivariate-gaussian.png]

Recall moment matching(from sufficient statistics to natural parameter), the sufficient statistics here are \\(\sum x_n\\) and \\(\sum x_n x_n^T\\)

#### Bayesian estimation

Unknown \\(\mu\\) and \\(\sigma\\), the resulting posterior is parametrized by:

![mle-multivariate-gaussian][/assets/images/pgm/bayesian-estimation-result-for-multivariat-gaussian.png]

See the details in *Bayesian Data Analysis* book.

The list of prior distributions for unknown/known \\(\mu\\) and \\(\sigma\\)...

## Learning for fully observable BN in general

Parameter estimation for fully observable BN can be decomposed into local conditional or marginal probability(smaller BNs) whose parameters are globally independently. So we can break down the global parameter estimation into local pieces. We rely on this property to perform efficient parameter estimation.

An illustration:

![illustration for learning for BN](/assets/images/pgm/parameter-estimation-for-BN-illustration.png)

Moreover, if the smaller BNs belongs to exponential famility, MLE amounts to moment matching.

### Defining prior for graphical model

This is not arbitrary. Guidelines are:

- Global Parameter Independence: if we want local estimation for efficiency, we cannot couple the priors of different nodes. Instead, the priors should be separated by nodes. For example, \\( p(\Theta \vert G)=\prod\limits_i p(\theta_i \vert G)\\)
- Local Parameter Independence: parameters for a single node under different *configuraiton/conditions* shouldn't be coupled. For example,\\(p(\theta_i) = \prod\limits_j p(\theta_{x_i \vert \pi_i(x)_j} \vert G)\\)

Some safe priors satisfying the requirements:

- Dirichlet for discrete DAG
- Normal prior for Gaussian DAG(\\(\mu\\) is unknown) and Normal-Wishart prior when \\(\sigma\\) also unknown.

### Parameter sharing

Example: the transition and emission probability in HMM. Instead of using one set of parameters indexed by the position, we replicate them along all positions so that only one set of parameters are shared.

## Summary

1. Structural learning: ChowLiu algorithm guarantees optimality for directed tree.
2. Learning in fully observable BN is easy and it can be decomposed into local estimation problems, which usually belongs to exponential family

