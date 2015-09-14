---
layout: post
title: "Low-Rank Tensors for Scoring Dependency Structures"
date: 2015-09-14 10:49:50
categories: paper
tags: tensor tensor-factorization soft-margin-maximization embedding
---

[Paper link](https://people.csail.mit.edu/regina/my_papers/tens14.pdf)

## Introduction

For each sentence \\(x\\) and candidate dependency tree, \\( y \in \mathcal{Y}(x)\\), the score:

$$ S(x, y) = \sum\limits_{h \rightarrow m \in y} s(h \rightarrow m)$$

where \\(s(h \rightarrow m)\\) is the score for each arc from head \\(h\\) to modifier \\(m\\).

The prediction is:

$$ \hat{y} = argmax_{y \in \mathcal{Y}(x)} S(x, y)$$


## Motivation

**Tradional way for dependency parsing**:

The arc score is simply vector inner product:

$$ s_{\theta}( h \rightarrow m) = <\theta, \phi_{h \rightarrow m}> $$

where \\(\theta \in \mathcal{R}^L \\) is the parameter to be estimated.

Problem with the vector space method:

Feature number \\(L\\) is usually large. Feature selection is often used. However, manual way is laborsome while automatic way might get rid of useful features(as some feature lack clear linguistic meaning such as word embedding)

**Improvement**:

Basic idea:

- Start with some simple features(e.g, word, lemma, morph, pos) for head, modifier and arc, and apply Kronecker product to combine differet features.
- Use low-rank approximation to approximate the parameter used to compute the arc score
- Use soft-margin maximization as the training objective and passive-aggressive algorithm for training


## Details

### Model

Now they propose a different way to compute \\(s(h \rightarrow m)\\). 

Given head, modifier and the arc, we first extract three feature vectors, $$ \phi_{h}, \phi_{m}, \phi_{h \rightarrow m} $$ respectively.

Kronecker product is applied on them:

$$ (\phi_{h} \otimes \phi_{m} \otimes \phi_{h \rightarrow m})_{i,j,k} = \phi_{h, i} \phi_{m, j} \phi_{h \rightarrow m, k} $$

which aims at combine those features.

The score is defined as:

$$ s(h \rightarrow m) = <A, \phi_{h} \otimes \phi_{m} \otimes \phi_{h \rightarrow m}> $$

equavalent to the inner product between A and combined features.

For training, our task to to estimat \\(A \\), which can be potentially large.

To reduce the number of parameters as well as adding genealization power, we approximate \\(A\\) via low-rank approximation, \\( U, V \in \mathcal{R}^{r \times n}, W \in \mathcal{R}^{r \times d} \\):

$$ A = \sum\limits_{i=1}^r U(i, :) \otimes V(i, :) \otimes W(i, :) $$

We say \\(A\\) is in the Kruskal form.

The score becomes

$$ \sum\limits_{i=1}^r [U\phi_h]_i [V\phi_m]_i [W\phi_{h \rightarrow m}]_i $$

**Why?**

Finally, the tensor score is combined by weight with the some other score.


### Training

Soft margin maximization is used and online learning via alternatively updating parameters is used.

The details cannot be understood for now.

## Learned

- Factorizing tensor via Kruskal form(a new way)
- Combining features through tensor outer product/cross product. 
- Soft margin maximization(like the objective for SVM) is used for the training of structure prediction problem
- Definition of online learning: update parameter successively based on each instance

## Questions

- How the prediction/decoding is done?
- How Passive Aggresive training works? 
- How does Equation (2) comes up?
- Can we apply the feature combination and tensor factorization to other prediction problem
