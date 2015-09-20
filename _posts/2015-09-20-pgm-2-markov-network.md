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
This example demonstrates one case where BN **cannot** capture the desired independecy assumtpions while MN can.

## Parametrization

- factors(clique potentials): denoted as (\\(\phi_i{D_i}\\))
- Markov network factorization: a distribution of (\\(\phi_i{D_i}\\)) factorizes over \\( \mathcal{H} \\)if each \\(D_i\\) in \\(\phi_i(D_i)\\) is a *complete* subgraph of \\(\mathcal{H}\\))
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

- pairwise/local(Markov blanket)/global indepenency assumptions of \\( \mathcal{H}\\) that \\( P \\ factorizes on are equivalennt if \\( P \\) is positive

## Parametrization Revisited

- enerny function: \\(-\ln(\phi(D))\\)
- factor graph(consisting of variable node and factor node) makes explicit the factor structure in the network
- feature: factor without nonnegativity requirement. More compact(something like 1 if .. and 0 otherwise)
- log-linear model: \\(P(X_1, X_2, \cdots, X_n) = \frac{1}{Z}\exp[-\sum\limits_{i=1}^{k}w_if_i(\mathbf{D}_i)]\\), where \\(f_i\\) is feature function, \\(D_i\\) is complete subgraph in \\( \mathcal{H}\\) and \\(w_i\\) are feature weights. More compact compared to the factor table that enumerates all combinations.

- Ising model, Boltzmann distribution, their connection and Boltzmann distribution's connection to activation model for neuron.

- Overparametrization: more parameters than needed => infinite ways to represent the same distribution
- Canoninical factorization defined over all cliques of \\( \mathcal{H}\\) avoid the parametrization ambiguity.
- Canoninical factorization used to prove the Hammersley-Clifford theorem: if \\( \mathcal{H}\\) is I-map of \\( P\\), then \\( P\\) facorizes over \\( \mathcal{H}\\)
- Linearly dependent features lead to non-unique parametrization. Or for linearly independent features, each set of feature weights unique define \\(P\\)
- To avoid overparametrization, choose linearly independent features(**how?**)

## BN and MN

- The question is how to convert from one to the other so that independency assumption in the original one is satisfied?
- Chordal network is the intersection betwenn MN and BN in that it can be represented in the other type of model perfectly if it's chordal


## Partially Directed Models

- Conditional Random Fields: models \\(P(Y \vert X)\\) instead of \\(P(Y, X))\\, \\(Y\\) target variables, \\(X\\) observed variables. Different parition function.
- Benefit of conditioning on \\(X\\): avoid modeling \\(X\\), which can be quite complex and the possibility to incorporate rich set of features
- The three different linear-chain graphical models: 1, fully undirected(CRF), 2, partially undirected and 3, fully directed(HMM). 1 and 2 differs in the feature function's scope on \\(X\\) and 2 and 3 differs in the way they normalize to probability and independency assumptions made
- Skip-chain CRF: one observation node can have multiple parents and long-range label dependency
- Coupled linear-chain CRF: joint inference on two chains(POS and NER)


