---
layout: post
title: "PGM course: Variable Elimination"
date: 2015-09-22 14:04:38
categories: course
tags: pgm variable-elimination exact-inference
---

## Background

Two typical tasks of graphical model:

- Inference: \\(P(X \vert Y)\\)
- Estimate a model(learning): learning point estimate for model parameters or inference in Bayesian modeling

We talk about inference here. Several types of inference tasks:

- likelihood: \\(P(e)\\), just marginalization the remaining uninterested variables
- conditional probability(or posterior belief): \\(P(X \vert e)\\), which equals to \\( \frac{P(X, e)}{P(e)}\\)
- most probable assignment or maximum a posterior: \\(argmax_{y \in \mathcal{Y}} P(y \vert e)\\), Usually, the unnormalized quantity is considered. Can be used for classification/prediction and explanation

In this section, exact inference method called *variable elimination* is discussed.

## Complexity in general

Computing \\(P(X = x \vert e)\\) in a GM is NP-hard. This means this is not **general** algorithm that can solve the problem in polynomial time.

Two major inference approaches:

- exact inference: variable elimination, belief propagation(message passing), junction tree algorithm
- approximate inference: MCMC, variational methods

For certain structures, we can solve it efficiently and exactly.

## Elimination in chains

Given the graph:

$$ A \rightarrow B \rightarrow C \rightarrow D \rightarrow E $$

Calculating  \\(P(e)\\)

$$
\begin{eqnarray*}
P(e)
& = &
\sum\limits_{a,b,c,d} P(a) P(b \vert a) P(c \vert b) P(d \vert c) P(e \vert d) \\
& = &
\sum\limits_{b,c,d} P(c \vert b) P(d \vert c) P(e \vert d) \sum\limits_{a} P(a) P(b \vert a) \\
& = &
\sum\limits_{b,c,d} P(d \vert c) P(e \vert d) \sum\limits_{b} m(b) P(c \vert b) 
\end{eqnarray*}
$$ 

Complexity drops from \\(O(k^n)\\) to \\(O(nk^2)\\), where there are \\(n\\) nodes and each node takes \\(k\\) values.

More examples: HMM, linear chain CRF

Forward/backward algorithm in quadratic time

## Exact inference

Inference is in this general form:

$$ \sum\limits_{z}\prod\limits_{\phi \in \mathcal{F}} \phi$$

Essentially elimination of variables. Called *sum-product* inference

## Dealing with evidence

in a computationally homogeneous way

$$ \tau(Y, e) = \sum\limits_{z, e} \prod\limits_{\phi \in \mathcal{F}} \phi \times \delta(E, e) $$

where \\(Y\\) is the query, \\(e\\) is the evidence, \\(z\\) are the hidden variables and \\(\delta\\) is the evidence potential(indicator)

## Elimination algorithm

1. **Initialization**
   Let \\(\mathcal{F}\\) is the full set of factors
2. **Evidence**
   Multiply the factors with their evidence potential
3. **Sum-product variable elimination**
   Eliminate one hidden variable, \\(z\\) at a time
   During each elimination, factors \\(\mathcal{F}^{'}\\) whose scope contains \\(z\\) are first selected from \\(\mathcal{F}\\). Remaining factors \\(\mathcal{F}^{''} = \mathcal{F}^ - \mathcal{F}^{'}\\).  \\(\mathcal{F}^{''} \\) are multiplied and summed over \\(z\\). The resulting new factors are \\(\tau \cup \mathcal{F} \\)
4. **Normalization**

**Q**:

- How to determine the optimal variable elimination order? NP-hard
- **What types** of graphical model can we perform efficient exact inference on?

## Complexity of variable elimination

For each elimination step, suppose \\(X\\) is to be eliminated, \\( Y_c \\) are in the factors and \\( \vert Y_c \vert = k \\)

For the factor multiplication: \\( k \times \vert Val(X) \vert \times \prod\limits_i \vert Val(Y_{c_i}) \vert \\)

For the summation:  \\( \vert Val(X) \vert \times \vert Val(Y_{c_i}) \vert \\)

Thus, **exponential** for the intermediate factor values.

Variable elimination produces a sequence of elimination factors along with a corresponding sequence of elimination cliques for the graphical model.

Elimination orders determines both the elimination factors and the elimination cliques.

The complexity is determined by the clique with the largest cardinality, \\( \prod\limits_i \vert Val(Y_{c_i}) \vert \\)

Good elimination order lead to cliques with small cardinality.

Finding the best elimination ordering is NP-hard.

Example that illustrates the importance of elimination order: star and tree model

Benefit of graphical model:

- figure out the capacity/complexity of a certain elimination order
- easily figure out a good variable elimination order


## Intractable exact inference

Exact inference can be intractable. For example Ising model:

Inference can lead to clique of size \\(n\\), the row/column number, which can be quite large(e.g, image)(try to eliminate variables in the Ising model)
