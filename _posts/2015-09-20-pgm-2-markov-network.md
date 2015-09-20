---
layout: post
title: "PGM course: Markov Network"
date: 2015-09-20 17:09:47
categories: course
tags: pgm markov-network
---


# Learned from book

BN and MN are different. They are incomparable in some sense.
Log-linear model, feature functions
They are be converted into I-map of each other. If MN(BN) is chordal, there exist BN(MN) that is a perfect I-map for it.
Conditional random field: no need to model the distribution for the observed variables.

## Misconception Example
This example demonstrates one case where BN **cannot** capture the desired independency assumptions while MN can.

## Parametrization

- factors(clique potentials): denoted as \\(\phi_i(D_i)\\)
- Markov network factorization: a distribution of \\(\phi_i(D_i)\\) factorizes over \\( \mathcal{H} \\)if each \\(D_i\\) in \\(\phi_i(D_i)\\) is a *complete* subgraph of \\(\mathcal{H}\\))
- There are multiple ways to factorize a MN(maximal clique or pair-wise)


For CV:

- Applications: image denoising, image segmentation, stereo reconstruction
- Pixel value for each pixel is one variable
- image denoising: recovering the real pixel value. Node potential for each pixel that penalizes discrepancy between observed value and predicted value. Edge potential that encourages continuity between adjacent pixel values. To avoid overpenalize, truncated norm is used.
- stereo reconstruction: restore the depth of each pixel.
- image segmentation: node potential for each superpixel and edge potential for adjacent superpixel to encourage continuity
- values can be clustered to reduce dimensionality

## Markov Network Independencies

Too many details here. The conclusion is:

- pairwise/local(Markov blanket)/global independency assumptions of \\( \mathcal{H}\\) that \\( P \\) factorizes on are equivalent if \\( P \\) is positive

## Parametrization Revisited

- energy function: \\(-\ln(\phi(D))\\)
- factor graph(consisting of variable node and factor node) makes explicit the factor structure in the network
- feature: factor without nonnegativity requirement. More compact(something like 1 if .. and 0 otherwise)
- log-linear model: \\(P(X_1, X_2, \cdots, X_n) = \frac{1}{Z}\exp[-\sum\limits_{i=1}^{k}w_if_i(\mathbf{D}_i)]\\), where \\(f_i\\) is feature function, \\(D_i\\) is complete subgraph in \\( \mathcal{H}\\) and \\(w_i\\) are feature weights. More compact compared to the factor table that enumerates all combinations.

- Ising model, Boltzmann distribution, their connection and Boltzmann distribution's connection to activation model for neuron.

- Overparametrization: more parameters than needed => infinite ways to represent the same distribution
- Canonical factorization defined over all cliques of \\( \mathcal{H}\\) avoid the parametrization ambiguity.
- Canonical factorization used to prove the Hammersley-Clifford theorem: if \\( \mathcal{H}\\) is I-map of \\( P\\), then \\( P\\) factorizes over \\( \mathcal{H}\\)
- Linearly dependent features lead to non-unique parametrization. Or for linearly independent features, each set of feature weights unique define \\(P\\)
- To avoid overparametrization, choose linearly independent features(**how?**)

## BN and MN

- The question is how to convert from one to the other so that independency assumption in the original one is satisfied?
- Chordal network is the intersection between MN and BN in that it can be represented in the other type of model perfectly if it's chordal


## Partially Directed Models

- Conditional Random Fields: models \\(P(Y \vert X)\\) instead of \\(P(Y, X) \\), \\(Y\\) target variables, \\(X\\) observed variables. Different partition function.
- Benefit of conditioning on \\(X\\): avoid modeling \\(X\\), which can be quite complex and the possibility to incorporate rich set of features
- The three different linear-chain graphical models: 1, fully undirected(CRF), 2, partially undirected and 3, fully directed(HMM). 1 and 2 differs in the feature function's scope on \\(X\\) and 2 and 3 differs in the way they normalize(local and global) to probability and independency assumptions they make
- Skip-chain CRF: one observation node can have multiple parents and long-range label dependency
- Coupled linear-chain CRF: joint inference on two chains(POS and NER)


# Learned from course video

- Example of `sky or water?`: need context information to infer. Introduction to the Ising model and UGM.
- Local Markov independencies: useful for Gibbs sampling, which requires such knowledge
- max clique and sub clique parametrization: two different ways for parametrization. They capture the same conditional independencies while max-clique can capture probability distribution that sub-clique cannot catch(for example *explicitly* capture the multi-way interaction) but the representation cost(parameter space size) is bigger. Again, compactness vs expressive power.
- potential function: more flexible/general than probability function. They can be probability function but not necessarily. They measure the compatibility. (Originates from atomic physics where we assign a number to various spin configurations of two atoms) We only have indirect measures sometimes.
- The way BN and MN normalizes is quite different, which results in their different capabilities: **local normalization** in BN, of which variables are independent of other variables, is not very good in some cases. For example, long range dependency in natural language where we like **global normalization** is not possible in BN. Seems to be related to the *label bias problem* discussed in some CRF paper.
- Computing the partition function is one of the biggest problem in PGM.
- Directed and undirected graphical model both have their both representation power(v-structure cannot be captured by UGM while the diamond structure cannot be captured by DGM) and they both cannot capture all probability distributions


Model examples:

- Boltzmann distribution in physics refers to log-linear model in statistics
- Boltzmann Machine defined on **fully-connected** graph with **pairwise** edges on nodes with **binary** values: $$ P(\mathbf{X}) = \frac{1}{Z}\exp{[\sum\limits_{i,j}\theta_{i,j} x_i x_j + \sum\limits_i \alpha_i x_i + C]} = \frac{1}{Z} \exp{(\mathbf{X} - \mu)^T \Theta (\mathbf{X} - \mu) } $$
- Ising model: sparse version of Boltzmann machine
- Potts model: multi-state Ising model
- Restricted Boltzmann Machine: hidden variables are introduced(breaking the homogeneous property of nodes)
- RBM properties: hidden variables **conditionally independent** given observation variables(**decoupling**).
- RBM for text modeling: define conditional probability for \\( P(h \vert x)\\) and \\( P(x \vert h)\\) of your preference and you 



Soundness/completeness, those theorem between \\( \mathcal{G}\\) and \\(P\\) are not talked in detail. This might indicate I don't need to pay too much attention on those topics.

